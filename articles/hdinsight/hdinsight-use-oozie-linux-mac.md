---
title: Utilisation des workflows Hadoop Oozie dans HDInsight basé sur Linux | Microsoft Docs
description: Utilisation de Hadoop Oozie dans HDInsight basé sur Linux. Découvrez comment définir un workflow Oozie et envoyer une tâche Oozie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Utilisation d’Oozie avec Hadoop pour définir et exécuter un flux de travail dans HDInsight
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apprenez à utiliser Apache Oozie pour définir un flux de travail qui utilise Hive et Sqoop, puis exécutez le flux de travail sur un cluster HDInsight basé sur Linux.

Apache Oozie est un système de workflow/coordination qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour Apache MapReduce, Apache Pig, Apache Hive et Apache Sqoop. Il peut également être utilisé pour planifier des travaux propres à un système comme des programmes Java ou des scripts shell.

> [!NOTE]
> Une autre option pour définir des flux de travail avec HDInsight consiste à utiliser Azure Data Factory. Pour en savoir plus sur Azure Data Factory, voir [Utilisation de Pig et Hive avec Data Factory][azure-data-factory-pig-hive].
> 
> 

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**: consultez la page [Obtention d’un essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Une interface de ligne de commande Azure**: consultez la page [Install and Configure the Une interface de ligne de commande Azure](../xplat-cli-install.md)
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]
* **Un cluster HDInsight**: consultez la page [Prise en main de HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
* **Une base de données SQL Azure**: elle est créée à l’aide de la procédure décrite dans ce document.

## <a name="example-workflow"></a>Exemple de flux de travail
Le flux de travail que vous allez implémenter en suivant les instructions de ce didacticiel contient deux actions. Les actions sont des définitions de tâches, telles que l’exécution de Hive, Sqoop, MapReduce ou un autre processus :

![Diagramme du workflow][img-workflow-diagram]

1. Une action Hive exécute un script HiveQL pour extraire des enregistrements à partir de la table **hivesampletable** incluse avec HDInsight. Chaque ligne de données décrit un accès depuis un appareil mobile spécifique. Le format d’enregistrement ressemble à ce qui suit :
   
        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
   
    Le script Hive utilisé dans ce document comptabilise le nombre total d’accès pour chaque plateforme (par exemple, Android ou iPhone) et stocke les nombres dans une nouvelle table Hive.
   
    Pour plus d’informations sur Hive, voir [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
2. Une action Sqoop exporte le contenu de la nouvelle table Hive vers une table dans la base de données SQL Azure. Pour plus d’informations sur Sqoop, voir [Utilisation de Hadoop Sqoop avec HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Pour obtenir la liste des versions d’Oozie prises en charge sur les clusters HDInsight, voir [Nouveautés des versions de cluster fournies par HDInsight][hdinsight-versions].
> 
> 

## <a name="create-the-working-directory"></a>Création du répertoire de travail
Oozie s’attend à ce que les ressources requises pour un travail soient stockées dans le même répertoire. Cet exemple utilise **wasbs:///tutorials/useoozie**. Utilisez la commande suivante pour créer ce répertoire et le répertoire de données qui contiendra la nouvelle table Hive créée par ce flux de travail :

    hdfs dfs -mkdir -p /tutorials/useoozie/data

> [!NOTE]
> Le répertoire `-p` a provoqué la création de tous les répertoires dans le chemin d’accès s’ils n’existent pas déjà. Le répertoire **data** sera utilisé pour contenir les données utilisées par le script **useooziewf.hql**.
> 
> 

Exécutez également la commande suivante, qui garantit que Oozie peut emprunter l’identité de votre compte d'utilisateur lors de l’exécution de travaux Hive et Sqoop. Remplacez **USERNAME** par votre nom de connexion :

    sudo adduser USERNAME users

Si vous recevez une erreur indiquant que l’utilisateur est déjà un membre, vous pouvez simplement l’ignorer.

## <a name="add-a-database-driver"></a>Ajout d’un pilote de base de données
Étant donné que ce flux de travail utilise Sqoop pour exporter des données vers la base de données SQL, vous devez fournir une copie du pilote JDBC utilisé pour communiquer avec la base de données SQL. Pour le copier dans le répertoire de travail, utilisez la commande suivante :

    hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

Si votre flux de travail utilisait d’autres ressources, par exemple un fichier jar contenant une application MapReduce, vous devez également les ajouter.

## <a name="define-the-hive-query"></a>Définition de la requête Hive
Utilisez les étapes suivantes pour créer un script HiveQL qui définit une requête qui sera utilisée dans un flux de travail Oozie plus loin dans ce document.

1. Utilisez le protocole SSH pour vous connecter au cluster HDInsight basé sur Linux :
   
   * **Clients Linux, Unix ou OS X**: consultez la page [Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * **Clients Windows**: consultez la page [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Utilisez la commande suivante pour créer un fichier :
   
        nano useooziewf.hql
3. Une fois que l’éditeur nano est ouvert, utilisez les éléments suivants comme contenu du fichier :
   
        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
   
    Deux variables sont utilisées dans le script :
   
   * **${hiveTableName}**: contiendra le nom de la table à créer
   * **${hiveDataFolder}**: contiendra l’emplacement où stocker les fichiers de données pour la table
     
     Le fichier de définition du workflow (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l'exécution.
4. Appuyez sur Ctrl+X pour quitter l’éditeur. Lorsque vous y êtes invité, sélectionnez **Y** pour enregistrer le fichier, puis sélectionnez **Entrée** pour utiliser le nom de fichier **useooziewf.hql**.
5. Utilisez les commandes suivantes pour copier **useooziewf.hql** vers **wasbs:///tutorials/useoozie/useooziewf.hql** :
   
        hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql
   
    Ces commandes stockent le fichier **useooziewf.hql** sur le compte de stockage Azure associé à ce cluster, ce qui conserve le fichier même si le cluster est supprimé. Cela vous permet de réaliser des économies en supprimant les clusters lorsqu’ils ne sont pas en cours d’utilisation, tout en conservant vos tâches et vos flux de travail.

## <a name="define-the-workflow"></a>Définition du flux de travail
Les définitions des workflows Oozie sont écrites en hPDL (un langage de définition du processus XML). Pour définir le flux de travail, procédez comme suit :

1. Pour créer et modifier un fichier, utilisez l’instruction suivante :
   
        nano workflow.xml
2. Une fois que l’éditeur nano est ouvert, entrez les informations suivantes comme contenu du fichier :
   
        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>
            <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
            </action>
            <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>
   
    Deux actions sont définies dans le flux de travail :
   
   * **RunHiveScript** : il s’agit de l’action de départ, qui exécute le script Hive **useooziewf.hql**
   * **RunSqoopExport**: cette action exporte les données créées à partir du script Hive vers la base de données SQL à l’aide de Sqoop. Elle n’est exécutée que si l’action **RunHiveScript** a abouti.
     
     > [!NOTE]
     > Pour plus d’informations sur le workflow Oozie et sur l’utilisation d’actions de workflow, voir la [documentation Apache Oozie 4.0][apache-oozie-400] (pour HDInsight version 3.0) ou la [documentation Apache Oozie 3.3.2][apache-oozie-332] (pour HDInsight version 2.1).
     > 
     > 
     
     Notez que le flux de travail a plusieurs entrées telles que `${jobTracker}`, qui seront remplacées par les valeurs que vous utiliserez dans la définition de travail, plus loin dans ce document.
     
     Notez également l’entrée `<archive>sqljdbc4.jar</arcive>` dans la section Sqoop. Cela indique à Oozie de rendre cette archive disponible pour Sqoop lors de l’exécution de cette action.
3. Utilisez Ctrl-X, puis **Y** et **Entrée** pour enregistrer le fichier.
4. Utilisez la commande suivante pour copier le fichier **workflow.xml** vers **wasbs:///tutorials/useoozie/workflow.xml** :
   
        hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

## <a name="create-the-database"></a>Création de la base de données
Suivez les étapes du document [Création d’une base de données SQL](../sql-database/sql-database-get-started.md) afin de créer une base de données. Lorsque vous créez la base de données, utilisez **oozietest** comme nom de base de données. Notez également le nom utilisé pour le serveur de base de données ; vous en aurez besoin dans la section suivante.

### <a name="create-the-table"></a>Créer la table
> [!NOTE]
> Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.
> 
> 

1. Utilisez la commande suivante pour installer FreeTDS sur le cluster HDInsight :
   
        sudo apt-get --assume-yes install freetds-dev freetds-bin
2. Une fois FreeTDS installé, utilisez la commande suivante pour vous connecter au serveur de base de données SQL créé précédemment :
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
   
    Le résultat ressemble à ce qui suit :
   
        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>
3. À l’invite de commandes `1>` , entrez les lignes suivantes :
   
        CREATE TABLE [dbo].[mobiledata](
        [deviceplatform] [nvarchar](50),
        [count] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
        GO
   
    Une fois entrée l’instruction `GO` , les instructions précédentes sont évaluées. Cela crée une table nommée **mobiledata** qui sera écrite par Sqoop.
   
    Vérifiez que la table a été créée à l’aide des éléments suivants :
   
        SELECT * FROM information_schema.tables
        GO
   
    Le résultat ressemble à ce qui suit :
   
        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE
4. Entrée `exit` at the `1>` .

## <a name="create-the-job-definition"></a>Création de la définition de travail
La définition du travail explique où trouver workflow.xml, ainsi que les autres fichiers utilisés par le flux de travail (par exemple, useooziewf.hql). Elle définit également les valeurs des propriétés utilisées dans le flux de travail et les fichiers associés.

1. Utilisez la commande suivante pour obtenir l’adresse WASB complète de stockage par défaut. Nous nous en servirons dans le fichier de configuration dans un moment :
   
        sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
   
    Cette commande renvoie des informations similaires à celles-ci :
   
        <name>fs.defaultFS</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
   
    Enregistrez la valeur **wasbs://mycontainer@mystorageaccount.blob.core.windows.net**, car elle sera utilisée dans les prochaines étapes.
2. Utilisez la commande suivante pour obtenir le nom de domaine complet du nœud principal du cluster. Il sera utilisé comme adresse du JobTracker pour le cluster. Nous nous en servirons dans le fichier de configuration dans un moment :
   
        hostname -f
   
    La commande retourne des informations similaires à ce qui suit :
   
        hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net
   
    Le port utilisé pour le JobTracker est 8050 ; l’adresse complète à utiliser pour le JobTracker est donc **hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.
3. Utilisez la commande suivante pour créer la configuration de définition de travail Oozie :
   
        nano job.xml
4. Une fois que l’éditeur nano est ouvert, utilisez les éléments suivants comme contenu du fichier :
   
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
   
          <property>
            <name>nameNode</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
          </property>
   
          <property>
            <name>jobTracker</name>
            <value>JOBTRACKERADDRESS</value>
          </property>
   
          <property>
            <name>queueName</name>
            <value>default</value>
          </property>
   
          <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
          </property>
   
          <property>
            <name>hiveScript</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
          </property>
   
          <property>
            <name>hiveTableName</name>
            <value>mobilecount</value>
          </property>
   
          <property>
            <name>hiveDataFolder</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
          </property>
   
          <property>
            <name>sqlDatabaseConnectionString</name>
            <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
          </property>
   
          <property>
            <name>sqlDatabaseTableName</name>
            <value>mobiledata</value>
          </property>
   
          <property>
            <name>user.name</name>
            <value>YourName</value>
          </property>
   
          <property>
            <name>oozie.wf.application.path</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
          </property>
        </configuration>
   
   * Remplacez toutes les instances de **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** par la valeur que vous avez reçue précédemment.
     
     > [!WARNING]
     > Vous devez utiliser le chemin d’accès WASB complet, avec le conteneur et le compte de stockage inclus dans le chemin d’accès. L’utilisation du format court (wasbs:///) provoque l’échec de l’action RunHiveScript lorsque le travail démarre.
     > 
     > 
   * Remplacez **JOBTRACKERADDRESS** par l’adresse de JobTracker/ResourceManager reçue précédemment.
   * Remplacez **YourName** par votre nom de connexion pour le cluster HDInsight.
   * Remplacez **serverName**, **adminLogin** et **adminPassword** par les informations de votre Azure SQL Database.
     
     La plupart des informations de ce fichier sont utilisées pour remplir les valeurs utilisées dans les fichiers workflow.xml ou ooziewf.hql (comme ${nameNode}).
     
     > [!NOTE]
     > L’entrée **oozie.wf.application.path** définit l’emplacement du fichier workflow.xml, qui contient le flux de travail exécuté par ce travail.
     > 
     > 
5. Utilisez Ctrl-X, puis **Y** et **Entrée** pour enregistrer le fichier.

## <a name="submit-and-manage-the-job"></a>Soumission et gestion du travail
Les étapes suivantes utilisent la commande Oozie pour soumettre et gérer des flux de travail Oozie sur le cluster. La commande Oozie est une interface conviviale sur l’ [API REST Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Lorsque vous utilisez la commande Oozie, vous devez utiliser le nom de domaine complet pour le nœud principal HDInsight. Ce nom de domaine complet est uniquement accessible à partir du cluster, ou, si le cluster se trouve sur un réseau virtuel Azure, à partir des autres ordinateurs sur le même réseau.
> 
> 

1. Utilisez la commande suivante pour obtenir l’URL du service Oozie :
   
        sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
   
    Cette commande retourne une valeur semblable à la suivante :
   
        <name>oozie.base.url</name>
        <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
   
    La partie **http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** est l’URL à utiliser avec la commande Oozie.
2. Pour créer une variable d’environnement pour l’URL de manière à ne pas être obligé de la taper pour chaque commande :
   
        export OOZIE_URL=http://HOSTNAMEt:11000/oozie
   
    Remplacez l’URL par celle reçue précédemment.
3. Pour envoyer le travail, utilisez la commande suivante :
   
        oozie job -config job.xml -submit
   
    Cette commande charge les informations du travail à partir de **job.xml** et les envoie à Oozie, mais n’exécute pas le travail.
   
    Une fois la commande terminée, elle retourne normalement l’ID du travail. Par exemple, `0000005-150622124850154-oozie-oozi-W`. Ce dernier sera utilisé pour gérer le travail.
4. Affichez l’état du travail à l'aide de la commande suivante. Entrez l’ID de travail retourné par la commande précédente :
   
        oozie job -info <JOBID>
   
    Cette commande retourne des informations similaires à ce qui suit.
   
        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasbs:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------
   
    Ce travail a le statut `PREP`, ce qui indique qu’il a été envoyé, mais qu’il n’a pas encore été démarré.
5. Pour démarrer le travail, utilisez la commande suivante :
   
        oozie job -start JOBID
   
    Si vous vérifiez l’état après cette commande, il sera en cours d’exécution et des informations pour les actions au sein du travail seront retournées.
6. Une fois le travail terminé correctement, vous pouvez vérifier que les données ont été générées et exportées vers la table de base de données SQL en utilisant les commandes suivantes :
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
   
    À l’invite `1>` , entrez les informations suivantes :
   
        SELECT * FROM mobiledata
        GO
   
    Vous recevez normalement des informations similaires à ce qui suit :
   
        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Pour plus d’informations sur la commande Oozie, consultez la page [Outil en ligne de commande Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API REST Oozie
L'API REST Oozie vous permet de créer vos propres outils fonctionnant avec Oozie. Les informations suivantes sont des informations spécifiques de HDInsight sur l’utilisation de l’API REST Oozie :

* **URI** : l’API REST est accessible depuis l’extérieur du cluster à l’adresse `https://CLUSTERNAME.azurehdinsight.net/oozie`
* **Authentification**: vous devez vous authentifier à l’API en utilisant le compte HTTP (admin) et le mot de passe du cluster. Par exemple :
  
        curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Pour plus d'informations sur l’utilisation de l’API REST Oozie, consultez la page [API des services web Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interface utilisateur web Oozie
L’interface utilisateur web Oozie fournit une vue web de l’état des travaux Oozie sur le cluster. Elle permet d’afficher l’état du travail, la définition du travail, la configuration, un graphique des actions dans le travail et les journaux du travail. Vous pouvez également afficher les détails pour les actions du travail.

Pour accéder à l'interface utilisateur web Oozie, procédez comme suit :

1. Créez un tunnel SSH vers le cluster HDInsight. Pour plus d’informations sur la façon de procéder, consultez [Utiliser SSH Tunneling pour accéder à l’interface web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Internet](hdinsight-linux-ambari-ssh-tunnel.md).
2. Une fois qu’un tunnel a été créé, ouvrez l’interface utilisateur web Ambari dans votre navigateur web. L’URI du site Ambari est **https://CLUSTERNAME.azurehdinsight.net**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight basé sur Linux.
3. Sur le côté gauche de la page, sélectionnez **Oozie**, puis **Liens rapides**, et enfin **Interface utilisateur web Oozie**.
   
    ![image des menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)
4. L’interface utilisateur web Oozie affiche par défaut les travaux du flux de travail en cours d’exécution. Pour voir tous les travaux du flux de travail, sélectionnez **Tous les travaux**.
   
    ![Tous les travaux affichés](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)
5. Sélectionnez un travail pour afficher plus d’informations sur celui-ci.
   
    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)
6. Sous l’onglet Job Info, vous pouvez voir les informations de base sur le travail, ainsi que les actions individuelles au sein du travail. Les onglets visibles en haut de la page vous permettent d’afficher la définition du travail et la configuration du travail, d’accéder au journal du travail ou d’afficher un graphique non cyclique dirigé du travail.
   
   * **Job Log** : cliquez sur le bouton **GetLogs** pour obtenir tous les journaux du travail, ou utilisez le champ **Enter Search Filter** pour filtrer les journaux.
     
       ![Journal du travail](./media/hdinsight-use-oozie-linux-mac/joblog.png)
   * **JobDAG**: le graphique non cyclique dirigé est une représentation graphique des chemins de données utilisés à travers le flux de travail.
     
       ![Graphique non cyclique dirigé du travail](./media/hdinsight-use-oozie-linux-mac/jobdag.png)
7. Lorsque vous sélectionnez l’une des actions sous l’onglet **Job Info** , des informations sur l’action s’affichent. Par exemple, sélectionnez l’action **RunHiveScript** .
   
    ![Informations sur l’action](./media/hdinsight-use-oozie-linux-mac/action.png)
8. Vous pouvez voir les détails de l’action, notamment un lien vers l’ **URL de la console**, qui peut être utilisée pour afficher les informations de JobTracker pour le travail.

## <a name="scheduling-jobs"></a>Planification des travaux
Le coordinateur vous permet de spécifier le début, la fin et la fréquence d’occurrence des travaux afin qu’ils puissent être planifiés pour certaines heures.

Pour définir une planification pour le flux de travail, procédez comme suit :

1. Utilisez la commande suivante pour créer un fichier nommé **coordinator.xml**:
   
        nano coordinator.xml
   
    Utilisez les données suivantes comme contenu du fichier :
   
        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
          <action>
            <workflow>
              <app-path>${workflowPath}</app-path>
            </workflow>
          </action>
        </coordinator-app>
   
    Notez que cet exemple utilise des variables `${...}` qui seront remplacées par des valeurs dans la définition du travail. Les variables sont les suivantes :
   
   * **${coordFrequency}**: délai entre les instances du travail en cours d’exécution
   * **${coordStart}**: heure de début du travail
   * **${coordEnd}**: heure de fin du travail
   * **${coordTimezone}**: les travaux du coordinateur se trouvent dans un fuseau horaire fixe sans passage à l’heure d’été (généralement représenté avec UTC). Ce fuseau horaire est appelé le « fuseau horaire du traitement d’Oozie ».
   * **${wfPath}**: chemin d’accès de workflow.xml
2. Utilisez Ctrl-X, puis **Y** et **Entrée** pour enregistrer le fichier.
3. Pour le copier dans le répertoire de travail pour ce travail, utilisez la commande suivante :
   
        hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml
4. Utilisez la commande suivante pour modifier le fichier **job.xml** :
   
        nano job.xml
   
    Effectuez les modifications suivantes :
   
   * Remplacez `<name>oozie.wf.application.path</name>` par `<name>oozie.coord.application.path</name>`. Cela ordonne à Oozie d’exécuter le fichier coordinateur au lieu du fichier de flux de travail
   * Ajoutez ce qui suit, qui définit une variable utilisée dans le coordinator.xml pour pointer vers l’emplacement de workflow.xml :
     
           <property>
             <name>workflowPath</name>
             <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
           </property>
     
       Remplacez les valeurs de **mycontainer** et **mystorageaccount** par les valeurs utilisées dans d’autres entrées du fichier job.xml.
   * Ajoutez le code suivant, qui définit le début, la fin et la fréquence à utiliser pour le fichier coordinator.xml :
     
           <property>
             <name>coordStart</name>
             <value>2015-06-25T12:00Z</value>
           </property>
     
           <property>
             <name>coordEnd</name>
             <value>2015-06-27T12:00Z</value>
           </property>
     
           <property>
             <name>coordFrequency</name>
             <value>1440</value>
           </property>
     
           <property>
             <name>coordTimezone</name>
             <value>UTC</value>
           </property>
     
       Ce code fixe l’heure de début à 00h00 le 25 juin 2015, l’heure de fin au 27 juin 2015 et définit l’intervalle d’exécution de ce travail sur Quotidien (la fréquence est exprimée en minutes, par conséquent, 24 heures x 60 minutes = 1 440 minutes.) Enfin, le fuseau horaire est défini au format UTC.
5. Utilisez Ctrl-X, puis **Y** et **Entrée** pour enregistrer le fichier.
6. Utilisez la commande suivante pour exécuter le travail :
   
        oozie job -config job.xml -run
   
    Cette commande envoie et démarre le travail.
7. Si vous accédez à l’interface utilisateur web Oozie et sélectionnez l’onglet **Coordinator Jobs** , vous obtenez des informations semblables à ce qui suit :
   
    ![Onglet Travaux du coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)
   
    Notez l’entrée **Next Materialization** ; il s’agit de l’heure de la prochaine exécution du travail.
8. De même que pour le travail de flux de travail précédent, lorsque vous sélectionnez l’entrée du travail dans l’interface utilisateur web, des informations sur le travail s’affichent :
   
    ![Informations sur les travaux du coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)
   
    Notez que seules les exécutions réussies du travail s’affichent, pas les actions individuelles dans le flux de travail planifié. Pour voir ces dernières, sélectionnez l’une des entrées **Action** . Ceci affichera des informations similaires à celles extraites pour le travail de flux de travail antérieur.
   
    ![Informations sur l’action](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Résolution des problèmes
Lors de la résolution des problèmes avec les travaux Oozie, l’interface utilisateur Oozie est très utile, car elle permet de visualiser facilement les deux journaux Oozie, ainsi que des liens vers les journaux JobTracker des travaux MapReduce telles que les requêtes Hive. En général, le modèle pour la résolution des problèmes doit être le suivant :

1. Afficher le travail dans l’interface utilisateur web Oozie.
2. En cas d’erreur ou d’échec d’une action spécifique, sélectionnez l’action pour voir si le champ **Message d’erreur** fournit plus d’informations sur l’échec.
3. Si elle est disponible, utilisez l’URL de l’action pour afficher des détails supplémentaires (tels que les journaux JobTracker) pour l’action.

Voici des erreurs spécifiques que vous pouvez rencontrer avec une description de la marche à suivre pour les résoudre.

### <a name="ja009:-cannot-initialize-cluster"></a>JA009 : Cannot initialize cluster (Impossible d'initialiser le cluster)
**Symptômes** : l’état du travail devient **SUSPENDED**. Dans les détails du travail, **START_MANUAL** est affiché pour l’état de RunHiveScript. Lorsque vous sélectionnez l’action, le message d’erreur suivant apparaît :

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Cause** : les adresses WASB utilisées dans le fichier **job.xml** ne contiennent pas le conteneur de stockage ou le nom du compte de stockage. Le format d’adresse WASB doit être `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Résolution**: modifiez les adresses WASB utilisées par le travail.

### <a name="ja002:-oozie-is-not-allowed-to-impersonate-&lt;user>"></a>JA002 : Oozie is not allowed to impersonate &lt;USER> (Oozie ne peut pas emprunter l’identité USER>)
**Symptômes** : l’état du travail devient **SUSPENDED**. Dans les détails du travail, **START_MANUAL** est affiché pour l’état de RunHiveScript. Lorsque vous sélectionnez l’action, le message d’erreur suivant apparaît :

    JA002: User: oozie is not allowed to impersonate <USER>

**Cause**: les paramètres d’autorisation actuels ne permettent pas à Oozie d’emprunter l’identité du compte d’utilisateur spécifié.

**Résolution** : Oozie est autorisé à emprunter l’identité des utilisateurs dans le groupe **users**. Utilisez le `groups USERNAME` pour voir les groupes dont le compte d’utilisateur est membre. Si l’utilisateur n’est pas membre du groupe **users** , utilisez la commande suivante pour ajouter l’utilisateur au groupe :

    sudo adduser USERNAME users

> [!NOTE]
> Il peut se passer plusieurs minutes avant que HDInsight reconnaisse que l'utilisateur a été ajouté au groupe.
> 
> 

### <a name="launcher-error-(sqoop)"></a>Launcher ERROR (Sqoop) (Erreur du lanceur, Sqoop)
**Symptômes** : l’état du travail devient **KILLED**. Dans les détails du travail, **ERROR**est affiché pour l’état de RunSqoopExport. Lorsque vous sélectionnez l’action, le message d’erreur suivant apparaît :

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Cause**: Sqoop ne peut pas charger le pilote de base de données requis pour accéder à la base de données.

**Résolution**: lors de l’utilisation de Sqoop à partir d’un travail Oozie, vous devez inclure le pilote de base de données avec les autres ressources (telles que workflow.xml) utilisées par le travail.

Vous devez également référencer l’archive contenant le pilote de base de données à partir de la section `<sqoop>...</sqoop>` de workflow.xml.

Par exemple, pour le travail de ce document, vous utiliseriez les étapes suivantes :

1. Copier le fichier sqljdbc4.1.jar dans le répertoire /tutorials/useoozie :
   
         hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
2. Modifier workflow.xml pour ajouter les éléments suivants sur une nouvelle ligne au-dessus de `</sqoop>`:
   
        <archive>sqljdbc41.jar</archive>

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment définir un flux de travail Oozie et comment exécuter un travail Oozie. Pour en savoir plus sur l’utilisation de HDInsight, consultez les articles suivants :

* [Utilisation du coordinateur Oozie basé sur le temps avec HDInsight][hdinsight-oozie-coordinator-time]
* [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx



<!--HONumber=Oct16_HO2-->


