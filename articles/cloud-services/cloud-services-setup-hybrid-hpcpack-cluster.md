---
title: Configuration d’un cluster HPC hybride avec Microsoft HPC Pack | Microsoft Docs
description: Découvrez comment utiliser Microsoft HPC Pack et Azure pour configurer un petit cluster de calculs complexes (HPC) hybride
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-service-management,hpc-pack

ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: danlep

---
# Configuration d’un cluster de calcul haute performance (HPC) hybride avec Microsoft HPC Pack et les nœuds de calcul Azure à la demande
Utilisez Microsoft HPC Pack 2012 R2 et Azure pour configurer un petit cluster de calcul haute performance (HPC) hybride. Le cluster se compose d'un nœud principal local (ordinateur exécutant le système d'exploitation Windows Server et HPC Pack) et de plusieurs nœuds de calcul que vous déployez comme instances de rôle de travail dans un service cloud Azure. Vous pouvez ensuite exécuter des tâches de calcul sur le cluster hybride.

![Cluster HPC hybride][Overview]

Ce didacticiel présente une approche, parfois nommée « cloudburst », visant à utiliser des ressources évolutives à la demande dans Azure afin d’exécuter des applications nécessitant beaucoup de calculs.

Ce didacticiel ne requiert pas d'expérience préalable avec les clusters de calcul ou HPC Pack. Il vise uniquement à vous aider à déployer rapidement un cluster de calcul hybride à des fins de démonstration. Pour connaître les éléments à prendre en compte ainsi que la procédure à suivre pour déployer un HPC Pack hybride à plus grande échelle dans un environnement de production, consultez les [instructions détaillées](http://go.microsoft.com/fwlink/p/?LinkID=200493). Pour d’autres scénarios avec HPC Pack, notamment le déploiement automatisé de clusters dans des machines virtuelles Azure, consultez [Options pour créer et gérer un cluster de calcul haute performance dans Azure avec Microsoft HPC Pack](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md).

## Composants requis
* **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* **Un ordinateur local sous Windows Server 2012 R2 ou Windows Server 2012**, et qui sera le nœud principal du cluster HPC. Si vous n'utilisez pas déjà Windows Server, vous pouvez télécharger et installer une [version d'évaluation](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Cet ordinateur doit être associé à un domaine Active Directory. Pour un scénario de test avec une nouvelle installation de Windows Server, vous pouvez ajouter le rôle de serveur Services de domaine Active Directory et promouvoir l’ordinateur servant de nœud principal comme contrôleur de domaine dans une forêt de domaines (voir la documentation de Windows Server).
  * Le système d'exploitation doit être installé dans l'une des langues suivantes pour prendre en charge HPC Pack : anglais, japonais ou chinois (simplifié).
  * Vérifiez que les mises à jour importantes et critiques sont installées.
* **HPC Pack 2012 R2** - [Téléchargez](http://go.microsoft.com/fwlink/p/?linkid=328024) le package d’installation pour obtenir gratuitement la dernière version et copiez les fichiers sur l’ordinateur servant de nœud principal ou vers un emplacement sur le réseau. Sélectionnez les fichiers d'installation de la même langue que votre serveur Windows Server.
* **Compte de domaine** : ce compte doit disposer d’autorisations d’administrateur local sur le nœud principal pour installer HPC Pack.
* **Connectivité TCP sur le port 443** entre le nœud principal et Azure.

## Installation du HPC Pack sur le nœud principal
Vous devez tout d'abord installer Microsoft HPC Pack sur votre ordinateur local qui exécute Windows Server pour en faire le nœud principal du cluster.

1. Connectez-vous au nœud principal en utilisant un compte de domaine disposant des autorisations d'administrateur.
2. Dans les fichiers d'installation HPC Pack, lancez l'Assistant Installation en exécutant Setup.exe.
3. Sur l'écran **HPC Pack 2012 R2 Setup**, cliquez sur **New installation or add new features to an existing installation**.
   
    ![Configuration de HPC Pack 2012][install_hpc1]
4. Sur la page **Microsoft Software User Agreement page**, cliquez sur **Suivant**.
5. Sur la page **Sélectionner le type d’installation**, cliquez sur **Create a new HPC cluster by creating a head node**, puis sur **Suivant**.
   
    ![Sélectionner le type d'installation][install_hpc2]
6. L'Assistant exécute plusieurs tests préalablement à l'installation. Cliquez sur **Suivant** sur la page **Règles d'installation** si tous les tests ont réussi. Sinon, consultez les informations fournies et apportez les modifications nécessaires à votre environnement. Réexécutez le test ou, si nécessaire, relancez l'Assistant Installation.
   
    ![Règles d'installation][install_hpc3]
7. Sur la page **HPC DB Configuration**, veillez à ce que **Head Node** soit sélectionné pour toutes les bases de données HPC, puis cliquez sur **Suivant**.
   
    ![Configuration de base de données][install_hpc4]
8. Acceptez les sélections par défaut sur les pages suivantes de l'Assistant. Sur la page **Installer les composants nécessaires**, cliquez sur **Installer**.
   
    ![Installer][install_hpc6]
9. Une fois l'installation terminée, désactivez **Start HPC Cluster Manager**, puis cliquez sur **Finish**. (Vous pourrez démarrer HPC Cluster Manager à une étape ultérieure.)
   
    ![Terminer][install_hpc7]

## Préparation de l'abonnement à Azure
Utilisez le [portail Azure Classic](https://manage.windowsazure.com) pour réaliser les étapes suivantes avec votre abonnement Azure. Ces instructions doivent être suivies afin de pouvoir par la suite déployer les nœuds Azure à partir du nœud principal local. Vous trouverez des procédures détaillées dans les sections suivantes.

* Téléchargement d'un certificat de gestion (requis pour les connexions sécurisées entre le nœud principal et les services Azure)
* Création d'un service cloud Azure dans lequel les nœuds Azure (instances de rôle de travail) seront exécutés
* Création d'un compte de stockage Azure
  
  > [!NOTE]
  > Notez votre ID d'abonnement Azure. Il vous sera utile par la suite. Vous trouverez ces informations dans le portail Classic en cliquant sur **Paramètres** > **Abonnements**.
  > 
  > 

### Téléchargement du certificat de gestion par défaut
HPC Pack installe un certificat auto-signé sur le nœud principal, nommé « Default Microsoft HPC Azure Management certificate ». Vous pouvez le télécharger en tant que certificat de gestion Azure. Ce certificat est fourni à des fins de test et pour les déploiements pour validation technique.

1. Sur l'ordinateur faisant office de nœud principal, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Cliquez sur **Paramètres** > **Certificats de gestion**.
3. Dans la barre de commandes, cliquez sur **Télécharger**.
   
    ![Paramètres de certificat][upload_cert1]
4. Sur le nœud principal, accédez au fichier C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer. Cliquez ensuite sur le bouton représentant une **coche**.
   
    ![Télécharger le certificat][install_hpc10]

**Default HPC Azure Management** s'affiche dans la liste des certificats de gestion.

### Création d'un service cloud Azure
> [!NOTE]
> Pour des performances optimales, créez le service cloud et le compte de stockage (dans une étape ultérieure) dans la même région.
> 
> 

1. Dans la barre de commandes du portail Classic, cliquez sur **Nouveau**.
2. Cliquez sur **Calculer** > **Service cloud** > **Création rapide**.
3. Saisissez l'URL du service cloud, puis cliquez sur **Créer un service de cloud computing**.
   
    ![Créer le service][createservice1]

### Création d'un compte de stockage Azure
1. Dans la barre de commandes du portail Classic, cliquez sur **Nouveau**.
2. Cliquez sur **Services de données** > **Stockage** > **Création rapide**.
3. Saisissez l'URL du compte, puis cliquez sur **Créer un Compte de stockage**.
   
    ![Créer le stockage][createstorage1]

## Configuration du nœud principal
Effectuez quelques unes des étapes de configuration de cluster requises afin que HPC Cluster Manager puisse déployer les nœuds Azure et soumettre des tâches.

1. Lancez HPC Cluster Manager sur le nœud principal. Si la boîte de dialogue **Select Head Node** s'affiche, cliquez sur **Local Computer**. La liste **Deployment To-do List** s'affiche.
2. Sous **Required deployment tasks**, cliquez sur **Configure your network**.
   
    ![Configurer le réseau][config_hpc2]
3. Dans l'Assistant Configuration du réseau, sélectionnez **All nodes only on an enterprise network** (Topologie 5).
   
    ![Topologie 5][config_hpc3]
   
   > [!NOTE]
   > Pour cette démonstration, il s'agit de la configuration la plus simple, car le nœud principal ne nécessite qu'une carte réseau pour se connecter à Active Directory et à Internet. Ce didacticiel ne couvre pas les scénarios de clusters nécessitant plusieurs réseaux.
   > 
   > 
4. Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, sous l'onglet **Review**, cliquez sur **Configure** pour terminer la configuration réseau.
5. Dans la liste **Deployment To-do List**, cliquez sur **Provide installation credentials**.
6. Dans la boîte de dialogue **Installation Credentials**, entrez les informations d'identification du compte de domaine utilisé pour installer HPC Pack. Cliquez ensuite sur **OK**.
   
    ![Informations d'identification pour l'installation][config_hpc6]
   
   > [!NOTE]
   > Les services HPC Pack utilisent les informations d'identification d'installation pour déployer les nœuds de calcul joints au domaine. Les nœuds Azure que vous ajoutez dans ce didacticiel ne sont pas joints au domaine.
   > 
   > 
7. Dans la liste **Deployment To-do List**, cliquez sur **Configure the naming of new nodes**.
8. Dans la boîte de dialogue **Specify Node Naming Series**, acceptez la série de noms par défaut, puis cliquez sur **OK**.
   
    ![Noms de nœuds][config_hpc8]
   
   > [!NOTE]
   > La série de noms génère des noms uniquement pour les nœuds de calcul joints au domaine. Les nœuds de travail Azure sont nommés de façon automatique.
   > 
   > 
9. Dans la liste **Deployment To-do List**, cliquez sur **Create a node template**. Le modèle de nœud vous sert d'exemple pour ajouter des nœuds Azure au cluster.
10. Procédez comme suit dans l'Assistant Create Node Template :
    
    a. Sur la page **Choisir un type de modèle de nœud**, cliquez sur **Modèle de nœud Azure**, puis cliquez sur **Suivant**.
    
    ![Modèle de nœud][config_hpc10]
    
    b. Cliquez sur **Suivant** pour accepter le nom de modèle par défaut.
    
    c. Sur la page **Provide Subscription Information** (Fournir les informations sur l’abonnement), entrez votre ID d’abonnement Azure (disponible dans les informations de compte Azure). Ensuite, dans **Management certificate**, cliquez sur **Browse**, puis sélectionnez **Default HPC Azure Management.** Cliquez ensuite sur **Suivant**.
    
    ![Modèle de nœud][config_hpc12]
    
    d. Sur la page **Fournir des informations sur le service**, sélectionnez le service cloud et le compte de stockage que vous avez créés à l’étape précédente. Cliquez ensuite sur **Suivant**.
    
    ![Modèle de nœud][config_hpc13]
    
    e. Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, sous l'onglet **Review**, cliquez sur **Create** pour créer le modèle de nœud.
    
    > [!NOTE]
    > Par défaut, le modèle de nœud Azure inclut des paramètres vous permettant de lancer (d'approvisionner) et d'arrêter manuellement les nœuds, avec HPC Cluster Manager. Vous pouvez également, si vous le souhaitez, programmer le démarrage et l’arrêt automatiques de nœuds Azure.
    > 
    > 

## Ajout de nœuds Azure au cluster
Le modèle de nœud vous sert d'exemple pour ajouter des nœuds Azure au cluster. L'ajout de nœuds au cluster stocke les informations de configuration de ces nœuds afin que vous puissiez les démarrer (approvisionner) à tout moment en tant qu'instances de rôle dans le service cloud. Votre compte est uniquement facturé pour les nœuds Azure une fois que les instances de rôle sont exécutées dans le service cloud.

Dans le cadre de ce didacticiel, vous allez ajouter deux petits nœuds.

1. Dans HPC Cluster Manager, sous **Node Management** (ou **Resource Management** dans des versions récentes de HPC Pack), dans le volet **Actions**, cliquez sur **Add Node**.
   
    ![Ajouter un nœud][add_node1]
2. Dans l'Assistant Add Node, sur la page **Select Deployment Method**, cliquez sur **Add Azure nodes**, puis sur **Suivant**.
   
    ![Add Azure Node][add\_node1_1]
3. Sur la page **Specify New Nodes**, sélectionnez le modèle de nœud Azure créé précédemment (par défaut, son nom est **Default AzureNode Template**). Indiquez ensuite **2** nœuds de format « **Small** », puis cliquez sur **Suivant**.
   
    ![Spécifier les nœuds][add_node2]
   
    Pour plus d’informations sur les tailles disponibles, consultez la page [Tailles de services cloud](cloud-services-sizes-specs.md).
4. Sur la page **Completing the Add Node Wizard**, cliquez sur **Finish**.
   
     Deux nœuds Azure, nommés **AzureCN-0001** et **AzureCN-0002**, sont désormais affichés dans HPC Cluster Manager. Leur état est **Not-Deployed** (Non déployé).
   
    ![Nœuds ajoutés][add_node3]

## Démarrage des nœuds Azure
Lorsque vous souhaitez utiliser les ressources de cluster dans Azure, utilisez HPC Cluster Manager pour démarrer (approvisionner) les nœuds Azure et les mettre en ligne.

1. Dans HPC Cluster Manager, sous **Node Management** (ou **Resource Management** dans des versions récentes de HPC Pack), cliquez sur un ou plusieurs nœuds, puis, dans le volet **Actions**, cliquez sur **Start**.
   
   ![Démarrer les nœuds][add_node4]
2. Dans la boîte de dialogue **Start Azure Nodes**, cliquez sur **Start**.
   
    ![Démarrer les nœuds][add_node5]
   
    Le nœud passe à l'état **Provisioning**. Consultez le journal d’approvisionnement pour suivre l’avancée de l’approvisionnement.
   
    ![Nœuds d'approvisionnement][add_node6]
3. Après quelques minutes, les nœuds Azure terminent leur approvisionnement et passent à l'état **Offline**. À cette étape, les instances de rôle sont exécutées mais n'acceptent pas encore les tâches de cluster.
4. Pour confirmer l’exécution des instances de rôle, cliquez dans le [portail Classic](https://manage.windowsazure.com) sur **Cloud Services** > *le\_nom\_de\_votre\_service\_cloud* > **Instances**.
   
    ![Instances en cours d'exécution][view_instances1]
   
    Deux instances de rôle de travail sont affichées comme étant exécutées dans le service. HPC Pack déploie également deux instances **HpcProxy** (taille moyenne) pour gérer la communication entre le nœud principal et Azure.
5. Pour faire passer les nœuds Azure en ligne afin qu'ils exécutent des tâches de cluster, sélectionnez-les, cliquez avec le bouton droit, puis cliquez sur **Bring Online**.
   
    ![Nœuds hors ligne][add_node7]
   
    HPC Cluster Manager indique que les nœuds sont dans l'état **Online**.

## Exécution d'une commande sur le cluster
Pour vérifier l’installation, utilisez la commande **clusrun** de HPC Pack pour exécuter une commande ou une application sur un ou plusieurs nœuds de cluster. Par exemple, utilisez **clusrun** pour obtenir la configuration IP des nœuds Azure.

1. Ouvrez une invite de commandes sur le nœud principal.
2. Tapez la commande suivante :
   
    `clusrun /nodes:azurecn* ipconfig`
3. Vous devez voir des informations similaires à ce qui suit.
   
    ![Clusrun][clusrun1]

## Exécution d'une tâche de test
Envoyez maintenant une tâche de test à exécuter sur le cluster hybride. Cet exemple est une simple tâche de « balayage paramétrique » (un type de calcul intrinsèquement parallèle). Cet exemple exécute des sous-tâches qui ajoutent un entier à lui-même à l'aide de la commande **set /a**. Tous les nœuds du cluster contribuent à terminer les sous-tâches pour les entiers de 1 à 100.

1. Dans HPC Cluster Manager, sous **Job Management**, dans le volet **Actions**, cliquez sur **New Parametric Sweep Job**.
   
    ![Nouvelle tâche][test_job1]
2. Dans la boîte de dialogue **Nouvelle tâche de balayage paramétrique**, sous **Ligne de commande**, tapez `set /a *+*` (en remplaçant la ligne de commande par défaut qui s'affiche). Ne modifiez pas les autres paramètres par défaut, puis cliquez sur **Submit** pour envoyer la tâche.
   
    ![Balayage paramétrique][param_sweep1]
3. Une fois la tâche terminée, cliquez deux fois sur la tâche **My Sweep Task**.
4. Cliquez sur **View Tasks**, puis sur une sous-tâche pour afficher la sortie calculée associée.
   
    ![Résultats de la tâche][view_job361]
5. Pour connaître le nœud ayant effectué le calcul pour cette sous-tâche, cliquez sur **Allocated Nodes** (votre cluster peut indiquer un nom de nœud différent).
   
    ![Résultats de la tâche][view_job362]

## Arrêt des nœuds Azure
Après avoir testé le cluster, arrêtez les nœuds Azure pour éviter toute facturation inutile de votre compte. Ceci arrête le service cloud et supprime les instances de rôle Azure.

1. Dans HPC Cluster Manager, sous **Node Management** (ou **Resource Management** dans des versions récentes de HPC Pack), sélectionnez les deux nœuds Azure. Ensuite, dans le volet **Actions**, cliquez sur **Stop**.
   
    ![Arrêter les nœuds][stop_node1]
2. Dans la boîte de dialogue **Stop Azure Nodes**, cliquez sur **Stop**.
   
    ![Arrêter les nœuds][stop_node2]
3. Le nœud passe à l'état **Stopping**. Après quelques minutes, HPC Cluster Manager signale les nœuds comme **Not-Deployed**.
   
    ![Nœuds non déployés][stop_node4]
4. Pour confirmer la non-exécution des instances de rôle, cliquez dans le [portail Classic](https://manage.windowsazure.com) sur **Cloud Services** > *le\_nom\_de\_votre\_service\_cloud* > **Instances**. Aucune instance n'est déployée dans l'environnement de production.
   
    ![Aucune instance][view_instances2]
   
    C'est ici que s'achève ce didacticiel.

## Étapes suivantes
* Consultez la documentation de [HPC Pack 2012 R2 et HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697).
* Pour configurer un déploiement de cluster HPC Pack hybride à plus grande échelle, consultez [Burst to Azure Worker Role Instances with Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493) (Débordement vers des instances de rôle de travail Azure avec Microsoft HPC Pack).
* Pour accéder à d'autres méthodes de création d’un cluster HPC Pack dans Azure, notamment avec des modèles Azure Resource Manager, consultez [Options de cluster HPC avec Microsoft HPC Pack dans Azure](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md).
* Consultez [Big Compute in Azure: Technical Resources for Batch and High Performance Computing (HPC)](../batch/big-compute-resources.md) (Big Compute dans Azure : ressources techniques pour Batch et HPC (calcul haute performance) pour en savoir plus sur la gamme de solutions cloud Big Compute et HPC dans Azure).

[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add\_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

<!---HONumber=AcomDC_0720_2016-->