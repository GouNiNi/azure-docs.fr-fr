### Ouverture de ports TCP dans le pare-feu Windows pour l'instance par défaut du moteur de base de données
1. Connectez-vous à la machine virtuelle avec le Bureau à distance. Pour obtenir des instructions détaillées sur la connexion à la machine virtuelle, consultez [Ouvrir une machine virtuelle SQL avec le Bureau à distance](../articles/virtual-machines/virtual-machines-windows-portal-sql-server-provision.md#open-the-vm-with-remote-desktop).
2. Une fois connecté, sur l’écran d’accueil, tapez **WF.msc**, puis appuyez sur ENTRÉE.
   
    ![Démarrer le programme du pare-feu](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. Dans le **Pare-feu Windows avec fonctions avancées de sécurité**, dans le volet gauche, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis sur **Nouvelle règle** dans le volet d'action.
   
    ![Nouvelle règle](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Dans la boîte de dialogue de **Assistant Nouvelle règle de trafic entrant**, sous **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.
5. Dans la boîte de dialogue **Protocole et ports**, utilisez le paramètre par défaut **TCP**. Dans la boîte de dialogue **Ports locaux spécifiques**, entrez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut, ou le numéro que vous avez choisi pour le port privé à l’étape du point de terminaison).
   
    ![Port TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Cliquez sur **Suivant**.
7. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.
   
    **Remarque relative à la sécurité** : la sélection de l’option **Autoriser la connexion si elle est sécurisée** peut renforcer la sécurité. Sélectionnez cette option si vous voulez configurer des options de sécurité supplémentaires dans votre environnement.
   
    ![Autoriser les connexions](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Dans la boîte de dialogue **Profil**, sélectionnez **Public**, **Privé** et **Domaine**. Cliquez ensuite sur **Suivant**.
   
    **Remarque relative à la sécurité** : la sélection de l’option **Public** autorise l’accès via Internet. Lorsque cela est possible, sélectionnez un profil plus restrictif.
   
    ![Profil public](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Dans la boîte de dialogue **Nom**, entrez un nom et une description pour cette règle, puis cliquez sur **Terminer**.
   
    ![Nom de la règle](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Le cas échéant, ouvrez des ports supplémentaires pour les autres composants. Pour plus d'informations, consultez la page [Configurer le Pare-feu Windows pour autoriser l'accès à SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).

### Configuration de SQL Server pour écouter le protocole TCP
1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **Gestionnaire de configuration SQL Server** et appuyez sur ENTRÉE.
   
    ![Ouvrir le gestionnaire de configuration SQL Server](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)
2. Dans le Gestionnaire de configuration SQL Server, dans le volet de la console, développez **Configuration du réseau SQL Server**.
3. Dans le volet de la console, cliquez sur **Protocoles pour MSSQLSERVER** (le nom d’instance par défaut). Dans le volet Détails, faites un clic droit sur **TCP** et cliquez sur **Activer** s’il n’est pas déjà activé.
   
    ![Activer TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)
4. Dans le volet de la console, cliquez sur **Services SQL Server** Dans le volet d'informations, cliquez avec le bouton droit sur **SQL Server (*nom de l'instance*)** (l'instance par défaut est **SQL Server (MSSQLSERVER)**), puis cliquez sur **Redémarrer**, pour arrêter et redémarrer l'instance de SQL Server.
   
    ![Redémarrer le moteur de base de données](./media/virtual-machines-sql-server-connection-steps/11Restart.png)
5. Fermez le Gestionnaire de configuration de SQL Server.

Pour plus d'informations sur l'activation des protocoles pour le moteur de base de données SQL Server, consultez la page [Activer ou désactiver un protocole réseau de serveur](http://msdn.microsoft.com/library/ms191294.aspx).

### Configuration de SQL Server pour l'authentification en mode mixte
Le moteur de base de données de SQL Server ne peut pas utiliser l'authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, configurez SQL Server pour l'authentification en mode mixte qui permet l’authentification SQL Server et l’authentification Windows

> [!NOTE]
> il n’est pas nécessaire de configurer l’authentification en mode mixte si vous avez configuré un réseau virtuel Azure avec un environnement de domaine configuré.
> 
> 

1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **SQL Server Management Studio** et cliquez sur l’icône sélectionnée.
   
    Lorsque vous ouvrez Management Studio pour la première fois, il doit créer l’environnement Management Studio pour les utilisateurs. Cette opération peut prendre du temps.
2. Management Studio affiche la boîte de dialogue **Se connecter au serveur**. Dans la zone **Nom du serveur**, tapez le nom de la machine virtuelle pour vous connecter au moteur de base de données avec l’explorateur d’objets (au lieu du nom de machine virtuelle, vous pouvez également utiliser **(local)** ou un simple point unique comme **nom du serveur**). Sélectionnez **Authentification Windows** et laissez ***your\_VM\_name*\\your\_local\_administrator** dans la zone **Nom d'utilisateur**. Cliquez sur **Connecter**.
   
    ![Se connecter au serveur](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. Dans SQL Server Management Studio Object Explorer, cliquez avec le bouton droit sur le nom de l'instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.
   
    ![Propriétés de serveur](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Sur la page **Sécurité**, sous **Authentification du serveur**, sélectionnez **Mode d'authentification SQL Server et Windows**, puis cliquez sur **OK**.
   
    ![Sélectionner le mode d'authentification](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **OK** pour confirmer l'obligation de redémarrer SQL Server.
6. Dans Object Explorer, cliquez avec le bouton droit sur votre serveur, puis cliquez sur **Redémarrer** (si SQL Server Agent est exécuté, il doit également être redémarré).
   
    ![Redémarrer](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **Oui** pour confirmer que vous voulez redémarrer SQL Server.

### Création de connexions d'authentification SQL Server
Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, vous devez créer au moins une connexion d'authentification SQL Server.

1. Dans SQL Server Management Studio Object Explorer, développez le dossier de l'instance de serveur dans laquelle vous voulez créer la connexion.
2. Cliquez avec le bouton droit sur le dossier **Sécurité**, pointez sur **Nouveau**, puis sélectionnez **Connexion...**.
   
    ![Nouvelle connexion](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Dans la boîte de dialogue **Nouvelle connexion**, sur la page **Général**, entrez le nom du nouvel utilisateur dans la zone **Nom de connexion**.
4. Sélectionnez **Authentification SQL Server**.
5. Dans la zone **Mot de passe**, entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe**.
6. Sélectionnez les options de mise en œuvre de mot de passe nécessaires (**Appliquer la stratégie de mot de passe**, **Appliquer l’expiration du mot de passe**, et **L’utilisateur doit changer de mot de passe à la prochaine connexion**). Si vous utilisez cette connexion pour vous-même, il est inutile d’exiger un mot de passe à la prochaine connexion.
7. Dans la liste **Base de données par défaut**, sélectionnez une base de données par défaut pour la connexion. Par défaut, cette option est réglée sur **principale**. Si vous n'avez pas encore créé de base de données d'utilisateur, gardez la valeur par défaut **master**.
   
    ![Propriétés de connexion](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. S’il s’agit de votre première connexion, vous pouvez désigner cette connexion en tant qu’administrateur SQL Server. Si cela est le cas, sur la page **Rôles du serveur**, activez la case à cocher **administrateur système**.
   
   > [!NOTE]
   > les membres du rôle serveur administrateur système fixe contrôlent complètement le moteur de base de données. Vous devez limiter soigneusement l'appartenance à ce rôle.
   > 
   > 
   
   ![administrateur système](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Cliquez sur OK.

Pour plus d'informations sur les connexions SQL Server, consultez la page [Créer un compte de connexion](http://msdn.microsoft.com/library/aa337562.aspx).

<!---HONumber=AcomDC_0629_2016-->