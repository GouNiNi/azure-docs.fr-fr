
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Obtenir la chaîne de connexion sur le portail Azure
Utilisez le [portail Azure](https://portal.azure.com/) pour obtenir la chaîne de connexion nécessaire pour que votre programme client interagisse avec la base de données SQL Azure :

1. Cliquez sur **PARCOURIR** > **Bases de données SQL**.
2. Entrez le nom de votre base de données dans la zone de texte de filtre, près de l’angle supérieur gauche du panneau **Bases de données SQL**.
3. Cliquez sur la ligne qui correspond à votre base de données.
4. Une fois que le panneau de votre base de données s’affiche, pour un meilleur confort visuel, vous pouvez cliquer sur les commandes standard de réduction afin de réduire les panneaux que vous avez utilisés pour la navigation et le filtrage de la base de données.
   
    ![Filtrer pour isoler votre base de données][10-FilterDatabase]
5. Dans le panneau de votre base de données, cliquez sur **Afficher les chaînes de connexion de la base de données**.
6. Si vous prévoyez d’utiliser la bibliothèque de connexions ADO.NET, copiez la chaîne intitulée **ADO**.
   
    ![Copier la chaîne de connexion ADO pour votre base de données][20-CopyAdoConnectionString]
7. Dans le format de votre choix, collez les informations de chaîne de connexion dans votre code de programme client.

Pour plus d’informations, consultez :<br/>[Chaînes de connexion et fichiers de configuration](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0406_2016-->