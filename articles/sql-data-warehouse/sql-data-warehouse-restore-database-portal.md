---
title: Restauration d’un Azure SQL Data Warehouse (Portail) | Microsoft Docs
description: Tâches du portail Azure permettant de restaurer un Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/21/2016
ms.author: lakshmir;barbkess;sonyama

---
# <a name="restore-an-azure-sql-data-warehouse-(portal)"></a>Restauration d’un Azure SQL Data Warehouse (Portail)
> [!div class="op_single_selector"]
> * [Vue d'ensemble][Vue d'ensemble]
> * [Portail][Portail]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
**Vérifiez votre capacité de DTU.**  Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un SQL Data Warehouse, vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change][Request a DTU quota change](Demander une modification du quota DTU).

## <a name="restore-an-active-or-paused-database"></a>Restauration d’une base de données active ou en pause
Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][portail Azure]
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir**, puis choisissez **Serveurs SQL Server**.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Accédez à votre serveur et sélectionnez-le.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Rechercher l’entrepôt SQL Data Warehouse à partir duquel effectuer la restauration et sélectionnez-le.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En haut du panneau de l’entrepôt de données, cliquez sur **Restaurer**
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Spécifiez un nouveau **nom pour la base de données**
7. Sélectionnez le dernier **point de restauration**
   
   1. Assurez-vous de sélectionner le dernier point de restauration.  Étant donné que les points de restauration sont affichés au format UTC, parfois, l’option par défaut affichée n’est pas le dernier point de restauration.
      
      ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Cliquez sur **OK**
9. Le processus de restauration de la base de données commence. Vous pouvez le surveiller dans **NOTIFICATIONS**.

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][Configurer votre base de données après récupération].
> 
> 

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.
Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][portail Azure]
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir**, puis choisissez **Serveurs SQL Server**.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Accédez à votre serveur et sélectionnez-le.
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Faites défiler jusqu’à la section Opérations dans le panneau de votre serveur.
5. Cliquez sur la vignette **Bases de données supprimées** .
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Spécifiez un nouveau **nom pour la base de données**
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Cliquez sur **OK**
9. Le processus de restauration de la base de données commence. Vous pouvez le surveiller dans **NOTIFICATIONS**.

> [!NOTE]
> Pour configurer votre base de données une fois la restauration terminée, consultez la page [Configurer votre base de données après récupération][Configurer votre base de données après récupération]. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][Vue d’ensemble de la continuité des activités de la base de données SQL Azure].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurer votre base de données après récupération]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/



<!--HONumber=Oct16_HO2-->


