---
title: Appel téléphonique à partir de Twilio (PHP) | Microsoft Docs
description: Découvrez comment passer un appel téléphonique et envoyer un message texte avec le service d'API Twilio sur Azure. Les exemples concernent une applications PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com

---
# Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure
L'exemple qui suit montre comment utiliser Twilio pour passer un appel depuis une page web PHP hébergée sur Azure. L'application finale demande à l'utilisateur les valeurs de l'appel téléphonique, comme illustré dans la capture d'écran qui suit.

![Formulaire d'appel Azure avec Twilio et PHP][twilio_php]

Pour utiliser le code de cette rubrique, vous devrez effectuer les opérations suivantes :

1. Obtenir un compte Twilio et un jeton d'authentification. Pour démarrer avec Twilio, évaluez les tarifs sur la page [http://www.twilio.com/pricing][twilio_pricing]. Vous pouvez demander un compte d'évaluation sur la page [https://www.twilio.com/try-twilio][try_twilio]. Pour plus d'informations sur l'API fournie par Twilio, consultez la page [http://www.twilio.com/api][twilio_api].
2. Obtenez la [bibliothèque Twilio pour PHP](https://github.com/twilio/twilio-php) ou installez-la sous forme de package PEAR. Pour plus d’informations, consultez le fichier [Lisezmoi](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installer le Kit de développement logiciel (SDK) Azure pour PHP. Pour une présentation du Kit de développement logiciel et des instructions sur son installation, consultez la page [Installation du SDK Azure pour PHP][setup_php_sdk].

## Création d'un formulaire Web pour passer un appel
Le code HTML qui suit présente la conception d'une page Web (**callform.html**) qui extrait les données des utilisateurs pour passer un appel :

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
      <form action="makecall.php" method="post">
       <table>
         <tr>
               <td>To:</td>
               <td><input type="text" size=50 name="callTo" value=""></td>
         </tr>
         <tr>
               <td>From:</td>
               <td><input type="text" size=50 name="callFrom" value=""></td>
         </tr>
         <tr>
               <td>Call message:</td>
               <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
         </tr>
         <tr>
               <td colspan=2><input type="submit" value="Make this call"></td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Création du code pour passer l'appel
Le code qui suit montre comment créer une page Web (**makecall.php**) qui est appelée lorsque l'utilisateur envoie le formulaire affiché par **callform.html**. Le code présenté ici crée le message d'appel et passe l'appel. Utilisez votre compte Twilio et votre jeton d'authentification plutôt que les valeurs par défaut utilisées dans **$sid** et **$token** dans le code qui suit.

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
          'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

En plus de passer l'appel, **makecall.php** affiche des métadonnées sur l'appel (comme dans l'exemple de capture d'écran ci-dessous). Pour plus d'informations sur les métadonnées de l'appel, consultez la page [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Réponse d'appel Azure avec Twilio et PHP][twilio_php_response]

## Exécution de l'application
L'étape suivante consiste à déployer votre application sur Sites Web Azure. Les articles suivants contiennent les informations relatives à la création du site web et au déploiement de votre code avec Git, FTP ou WebMatrix (même si toutes les informations de ces articles ne sont pas pertinentes) :

* [Création et déploiement d’un site web Azure PHP-MySQL avec Git][website-git]
* [Création et déploiement d’un site web Azure PHP-MySQL avec FTP][website-ftp]

## Étapes suivantes
Ce code vous est fourni afin de vous présenter les fonctions de base de l'utilisation de Twilio dans PHP sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

* Au lieu d'utiliser un formulaire Web, vous pouvez utiliser des objets blob de stockage Azure ou une base de données SQL pour stocker les numéros de téléphone et le texte des appels. Pour plus d'informations sur l'utilisation d'objets blob de stockage Azure dans PHP, consultez la page [Utilisation du stockage Azure dans les applications PHP][howto_blob_storage_php]. Pour plus d'informations sur l'utilisation de la base de données SQL dans PHP, consultez la page [Utilisation de la base de données SQL avec les applications PHP][howto_sql_azure_php].
* Le code **makecall.php** utilise une URL Twilio ([http://twimlets.com/message][twimlet_message_url]) pour fournir une réponse TwiML (Twilio Markup Language) qui précise à Twilio comment traiter l'appel. Par exemple, le code TwiML renvoyé peut contenir un verbe `<Say>` qui provoque la lecture audible du texte pour le destinataire de l'appel. Au lieu d'utiliser l'URL fournie par Twilio, vous pouvez concevoir votre propre service pour répondre à la demande de Twilio. Pour plus d'informations, consultez la page [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP][howto_twilio_voice_sms_php]. Pour plus d’informations sur TwiML, voir la page [http://www.twilio.com/docs/api/twiml][twiml], et pour plus d’informations sur `<Say>` et sur les autres verbes Twilio, voir la page [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Consultez les instructions de sécurité Twilio sur la page [https://www.twilio.com/docs/security][twilio_docs_security].

Pour plus d'informations sur Twilio, consultez la page [https://www.twilio.com/docs/][twilio_docs].

## Voir aussi
* [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php

<!---HONumber=AcomDC_0413_2016-->