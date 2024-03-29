---
title: Configurer un nom de domaine pour un point de terminaison Blob Storage | Microsoft Docs
description: Découvrez comment mapper un domaine d’utilisateur personnalisé au point de terminaison Blob Storage pour un compte Azure Storage dans le Portail Azure Classic.
services: storage
documentationcenter: ''
author: tamram
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: tamram

---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurer un nom de domaine personnalisé pour un point de terminaison Blob Storage
## <a name="overview"></a>Vue d'ensemble
Vous pouvez configurer un domaine personnalisé pour accéder à des données d’objets blob dans votre compte de stockage Azure. Le point de terminaison par défaut de Blob Storage est `<storage-account-name>.blob.core.windows.net`. Si vous mappez un domaine et un sous-domaine personnalisés, tels que **www.contoso.com** au point de terminaison des objets blob de votre compte de stockage, vos utilisateurs peuvent également accéder aux données d’objets blob de votre compte de stockage à l’aide de ce domaine.

> [!IMPORTANT]
> Azure Storage ne prend pas encore en charge HTTPS avec des domaines personnalisés. Nous sommes conscients que les clients sont intéressés par cette fonctionnalité ; elle sera disponible dans une version ultérieure.
> 
> 

Il existe deux méthodes pour mapper votre domaine personnalisé au point de terminaison de service BLOB de votre compte de stockage. La plus simple consiste à créer un mappage d'enregistrement CNAME entre votre domaine personnalisé et son sous-domaine et le point de terminaison de service BLOB. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source à un domaine cible. Dans cet exemple, le domaine source est votre domaine personnalisé et son sous-domaine. Notez que le sous-domaine est dans tous les cas indispensable. Le domaine cible est le point de terminaison de votre service BLOB.

Le processus consistant à mapper votre domaine personnalisé à votre point de terminaison de service BLOB peut entraîner un problème d’indisponibilité de courte durée du service au moment où vous enregistrez le domaine dans le [portail Azure Classic](https://manage.windowsazure.com). Si votre domaine personnalisé prend en charge, à ce moment-là, une application visée par un contrat de niveau de service (SLA) interdisant toute interruption de service, vous pouvez utiliser le sous-domaine Azure **asverify** en tant qu'étape d'enregistrement intermédiaire pour permettre aux utilisateurs d'accéder à votre domaine pendant que vous procédez au mappage DNS.

Le tableau suivant contient des exemples d'URL permettant d'accéder aux données d'objets blob dans un compte de stockage nommé **mystorageaccount**. Le domaine personnalisé enregistré pour le compte de stockage est **www.contoso.com**:

| Type de ressource | Formats d'URL |
| --- | --- |
| Compte de stockage |**URL par défaut :** http://mystorageaccount.blob.core.windows.net<p>**URL du domaine personnalisé :** http://www.contoso.com</td> |
| Blob |**URL par défaut :** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL du domaine personnalisé :** http://www.contoso.com/mycontainer/myblob |
| Conteneur racine |**URL par défaut :** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**URL du domaine personnalisé :** http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |

## <a name="register-a-custom-domain-for-your-storage-account"></a>Enregistrement d’un domaine personnalisé pour votre compte de stockage
Cette procédure permet d’enregistrer un domaine personnalisé si vous n’êtes pas inquiet à l’idée que les utilisateurs ne puissent pas accéder au domaine pendant un court instant, ou si votre domaine personnalisé n’héberge actuellement aucune application.

Si votre domaine personnalisé prend en charge une application qui ne peut supporter une interruption de service, utilisez la procédure décrite à la rubrique <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">Enregistrement d’un domaine personnalisé pour votre compte de stockage à l’aide du sous-domaine intermédiaire asverify</a>.

Pour configurer un nom de domaine personnalisé, vous devez créer un enregistrement CNAME auprès de votre bureau d’enregistrement de domaine. L’enregistrement CNAME spécifie un alias pour un nom de domaine ; dans ce cas, il mappe l’adresse de votre domaine personnalisé au point de terminaison Blob Storage de votre compte de stockage.

Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire. Notez que de nombreuses formules de base pour l'enregistrement de domaine n'offrent pas la configuration DNS. Avant de pouvoir créer l'enregistrement CNAME, vous devrez peut-être mettre à niveau votre formule d'enregistrement de domaine.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), accédez à l’onglet **Stockage** .
2. Dans cet** **onglet, cliquez sur le nom du compte de stockage dont vous souhaitez mapper le domaine personnalisé.
3. Cliquez sur l'onglet **Configurer** .
4. Au bas de l’écran, cliquez sur **Gérer les domaines** pour afficher la boîte de dialogue **Gérer un domaine personnalisé**. Le texte figurant en haut de la boîte de dialogue contient des informations sur la manière de créer l'enregistrement CNAME. Pour cette procédure, ignorez le texte qui fait référence au sous-domaine **asverify** .
5. Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.
6. Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias** ou **Sous-domaines**.
7. Créez un enregistrement CNAME et indiquez un alias de sous-domaine tel que **www** ou **photos**. Indiquez ensuite un nom d’hôte, à savoir votre point de terminaison de service BLOB, au format **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Le nom d'hôte à utiliser est indiqué dans le texte de la boîte de dialogue **Manage Custom Domain** .
8. Après avoir créé un enregistrement CNAME, retournez dans la boîte de dialogue **Gérer un domaine personnalisé** et entrez le nom de votre domaine personnalisé, en incluant le sous-domaine, dans le champ **Nom du domaine personnalisé**. Par exemple, si votre domaine est **contoso.com** et votre sous-domaine **www**, entrez **www.contoso.com** ; si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est obligatoire.
9. Cliquez sur le bouton **Register** pour enregistrer votre domaine personnalisé.
   
    Si l'enregistrement est réussi, le message **Your custom domain is active**apparaît. Les utilisateurs peuvent désormais afficher les données d’objet blob sur votre domaine personnalisé, à condition de disposer des autorisations nécessaires.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Enregistrement d’un domaine personnalisé pour votre compte de stockage à l’aide du sous-domaine intermédiaire asverify
Cette procédure permet d’enregistrer votre domaine personnalisé si ce dernier prend en charge une application visée par un contrat SLA qui interdit toute interruption de service. En créant un enregistrement CNAME qui pointe du sous-domaine asverify.&lt;subdomain&gt;.&lt;customdomain&gt; vers le compte de stockage asverify.&lt;storageaccount&gt;.blob.core.windows.net, vous pouvez pré-enregistrer votre domaine auprès d’Azure. Vous pouvez ensuite créer un second enregistrement CNAME qui pointe du sous-domaine &lt;subdomain&gt;.&lt;customdomain&gt; vers le compte de stockage &lt;storageaccount&gt;.blob.core.windows.net, afin de diriger le trafic allant vers votre domaine personnalisé vers votre point de terminaison de service BLOB.

Le sous-domaine asverify est un sous-domaine spécial reconnu par Azure. En ajoutant le sous-domaine **asverify** à votre propre sous-domaine, vous permettez à Azure de reconnaître votre domaine personnalisé sans modifier l'enregistrement DNS du domaine. Une fois l'enregistrement DNS pour le domaine modifié, il sera mappé au point de terminaison de service BLOB sans aucune interruption de service.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), accédez à l’onglet **Stockage** .
2. Dans cet** **onglet, cliquez sur le nom du compte de stockage dont vous souhaitez mapper le domaine personnalisé.
3. Cliquez sur l'onglet **Configurer** .
4. Au bas de l’écran, cliquez sur **Gérer les domaines** pour afficher la boîte de dialogue **Gérer un domaine personnalisé**. Le texte figurant en haut de la boîte de dialogue contient des informations sur la manière de créer l'enregistrement CNAME à l'aide du sous-domaine **asverify** .
5. Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.
6. Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias** ou **Sous-domaines**.
7. Créez un enregistrement CNAME et indiquez un alias de sous-domaine qui inclut le sous-domaine asverify. Par exemple, le sous-domaine que vous spécifiez sera au format **asverify.www** ou **asverify.photos**. Indiquez ensuite un nom d’hôte, à savoir votre point de terminaison de service BLOB, au format **asverify.mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Le nom d'hôte à utiliser est indiqué dans le texte de la boîte de dialogue **Manage Custom Domain** .
8. Après avoir créé un enregistrement CNAME, retournez dans la boîte de dialogue **Gérer un domaine personnalisé** et entrez le nom de votre domaine personnalisé dans le champ **Nom du domaine personnalisé**. Par exemple, si votre domaine est **contoso.com** et votre sous-domaine **www**, entrez **www.contoso.com** ; si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est obligatoire.
9. Cochez la case **Avancé : utilisez le sous-domaine Microsoft Azure ’asverify’ pour préenregistrer votre domaine personnalisé**.
10. Cliquez sur le bouton **Register** pour pré-enregistrer votre domaine personnalisé.
    
    Si le pré-enregistrement est réussi, le message **Your custom domain is active**apparaît.
11. À ce stade, votre domaine personnalisé a été vérifié par Azure, mais le trafic en direction de votre domaine n'est pas encore dirigé vers votre compte de stockage. Pour terminer le processus, retournez sur le site web du bureau d’enregistrement de votre DNS et créez un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison de service BLOB. Par exemple, spécifiez le sous-domaine sous la forme **www** ou **photos** et le nom d’hôte sous la forme **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Cette étape marque la fin de l'enregistrement de votre domaine personnalisé.
12. Pour finir, vous pouvez supprimer l'enregistrement CNAME que vous avez créé à l'aide du sous-domaine **asverify**, car il n'a plus d'utilité.

Les utilisateurs peuvent désormais afficher les données d’objet blob sur votre domaine personnalisé, à condition de disposer des autorisations nécessaires.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>vérifier que le domaine personnalisé pointe vers le point de terminaison du service BLOB.
Pour vérifier que votre domaine personnalisé est correctement mappé à votre point de terminaison de service BLOB, créez un objet blob dans un conteneur public au sein de votre compte de stockage. Dans un navigateur Web, utilisez un URI pour accéder à l’objet blob :

* http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Vous pouvez par exemple utiliser l’URI suivant pour accéder à un formulaire web via un domaine personnalisé **photos.contoso.com** qui renvoie à un objet blob dans votre conteneur **myforms** :

* http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Annuler l’inscription d’un domaine personnalisé de votre compte de stockage
Pour annuler l’inscription d’un domaine personnalisé, procédez comme suit : 

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com). 
2. Dans le volet de navigation, cliquez sur **Stockage**. 
3. Sur la page **Stockage** , cliquez sur le nom du compte de stockage pour afficher le tableau de bord. 
4. Dans le ruban, cliquez sur **Gérer le domaine**. 
5. Dans la boîte de dialogue **Gérer un domaine personnalisé**, cliquez sur **Annuler l’inscription**. 

## <a name="additional-resources"></a>Ressources supplémentaires
* [Comment mapper un domaine personnalisé à un point de terminaison de réseau de distribution de contenu (CDN)](../cdn/cdn-map-content-to-custom-domain.md)

<!--HONumber=Oct16_HO2-->


