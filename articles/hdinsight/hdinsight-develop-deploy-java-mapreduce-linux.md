---
title: Développement de programmes MapReduce en Java pour HDInsight basé sur Linux | Microsoft Docs
description: Découvrez comment développer des programmes MapReduce en Java et les déployer dans HDInsight basé sur Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: ''
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Développer des programmes MapReduce en Java pour Hadoop dans HDInsight Linux
Ce document vous guide dans l’utilisation d’Apache Maven pour créer une application MapReduce, la déployer vers un cluster Hadoop Linux dans HDInsight, puis l’exécuter.

## <a name="<a-name="prerequisites"></a>prerequisites"></a><a name="prerequisites"></a>Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou ultérieur (ou un équivalent, par exemple, OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* **Un abonnement Azure**
* **Interface de ligne de commande Azure**
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement
Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du JDK. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur un système OS X, Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`
* **PATH** :doit contenir les chemins d’accès suivants :
  
  * **JAVA_HOME** (ou le chemin d’accès équivalent)
  * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)
  * Le répertoire d’installation de Maven

## <a name="create-a-new-maven-project"></a>Création d’un projet Maven
1. À partir d’une session terminal ou de la ligne de commande de votre environnement de développement, modifiez les répertoires par l'emplacement dans lequel vous souhaitez stocker ce projet.
2. Utilisez la commande **mvn** , installée avec Maven, pour générer la structure du projet.
   
        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    Cela créera un répertoire dans le répertoire actuel avec le nom spécifié par le paramètre **artifactID** (**wordcountjava** dans cet exemple). Ce répertoire contiendra les éléments ci-après :
   
   * **pom.xml** - Le [modèle d’objet du projet (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) qui contient les informations et la configuration utilisées pour générer le projet.
   * **src** - Le répertoire contenant le répertoire **main\java\org\apache\hadoop\examples** dans lequel vous créerez l’application.
3. Supprimez le fichier **src\test\java\org\apache\hadoop\examples\apptest.java** , car il ne sera pas utilisé dans cet exemple.

## <a name="add-dependencies"></a>Ajout de dépendances
1. Modifiez le fichier **pom.xml** et ajoutez l’élément suivant dans la section `<dependencies>` :
   
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
   
    Ceci indique à Maven que le projet a besoin des bibliothèques (figurant dans &lt;artifactId\>) avec une version spécifique (figurant dans &lt;version\>). Au moment de la compilation, il sera téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [recherche du référentiel Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) pour en afficher davantage.
   
    `<scope>provided</scope>` indique à Maven que ces dépendances ne doivent pas être fournies avec l'application, car elles sont fournies par le cluster HDInsight au moment de l'exécution.
2. Ajoutez le code suivant au fichier **pom.xml** . Il doit être contenu entre les balises `<project>...</project>` dans le fichier, par exemple entre `</dependencies>` et `</project>`.
   
        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>
   
    Le premier plug-in configure le [plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), qui est également utilisé pour créer un uberjar (parfois appelé fatjar) contenant toutes les dépendances nécessaires à l’application. Il empêche également la duplication des licences dans le package jar, ce qui peut entraîner des problèmes sur certains systèmes.
   
    Le second plug-in configure le compilateur Maven, qui est utilisé pour affecter la version de Java nécessaire à cette application à la version utilisée sur le cluster HDInsight.
3. Enregistrez le fichier **pom.xml** .

## <a name="create-the-mapreduce-application"></a>Création de l’application MapReduce
1. Accédez au répertoire **wordcountjava\src\main\java\org\apache\hadoop\examples** et renommez le fichier **app.java** en **WordCount.java**.
2. Ouvrez le fichier **WordCount.java** dans un éditeur de texte et remplacez le contenu par les éléments suivants :
   
        package org.apache.hadoop.examples;
   
        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;
   
        public class WordCount {
   
          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{
   
            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();
   
            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }
   
          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();
   
            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }
   
          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }
   
    Notez que le nom du package est **org.apache.hadoop.examples** et que le nom de la classe est **WordCount**. Vous utiliserez ces noms lors de l’envoi de la tâche MapReduce.
3. Enregistrez le fichier.

## <a name="build-the-application"></a>Création de l'application
1. Remplacez le répertoire par **wordcountjava** , si cela n’a pas déjà été fait.
2. Utilisez la commande suivante pour générer un fichier JAR contenant l’application :
   
        mvn clean package
   
    Cela nettoie les artefacts de build précédents, télécharge toute dépendance non encore installée, puis génère et met l’application en package.
3. Une fois la commande exécutée, le répertoire **wordcountjava\target** contient un fichier appelé **wordcountjava-1.0-SNAPSHOT.jar**.
   
   > [!NOTE]
   > Le fichier **wordcountjava-1.0-SNAPSHOT.jar** est un uberjar, qui contient non seulement la tâche WordCount, mais également les dépendances nécessaires à la tâche au moment de l'exécution.
   > 
   > 

## <a name="<a-id="upload"></a>upload-the-jar"></a><a id="upload"></a>Téléchargement du fichier jar
Utilisez la commande suivante pour télécharger le fichier jar dans le nœud principal HDInsight :

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

De cette façon, les fichiers du système local sont copiés dans le nœud principal.

> [!NOTE]
> Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d'accès à la clé privée. Par exemple : `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.
> 
> 

## <a name="<a-name="run"></a>run-the-mapreduce-job"></a><a name="run"></a>Exécution de la tâche MapReduce
1. Connectez-vous à HDInsight à l’aide de SSH comme décrit dans les articles suivants :
   
   * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. À partir de la session SSH, utilisez la commande suivante pour exécuter l’application MapReduce :
   
        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout
   
    Elle utilise l’application MapReduce WordCount pour compter les mots dans le fichier davinci.txt et stocker les résultats dans **wasbs:///example/data/wordcountout**. Les fichiers d’entrée et de sortie sont stockés dans le stockage par défaut du cluster.
3. Une fois la tâche terminée, utilisez la commande suivante pour afficher les résultats :
   
        hdfs dfs -cat wasbs:///example/data/wordcountout/*
   
    Vous devez recevoir une liste de mots et de nombres, avec des valeurs similaires à ce qui suit :
   
        zeal    1
        zelus   1
        zenith  2

## <a name="<a-id="nextsteps"></a>next-steps"></a><a id="nextsteps"></a>Étapes suivantes
Dans ce document, vous avez appris à développer une tâche MapReduce Java. Consultez les documents suivants pour découvrir les autres façons de travailler avec HDInsight.

* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations, consultez également le [Centre pour développeurs Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx




<!--HONumber=Oct16_HO2-->


