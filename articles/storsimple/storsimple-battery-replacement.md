---
title: Remplacer la batterie sur un appareil StorSimple | Microsoft Docs
description: Décrit comment retirer, remplacer et entretenir le module de batterie de secours sur votre appareil StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# Remplacer le module de batterie de secours sur votre appareil StorSimple
## Vue d'ensemble
Le module d’alimentation et de refroidissement (PCM) du boîtier principal de votre appareil Microsoft Azure StorSimple dispose d’une batterie supplémentaire. Celle-ci fournit l’alimentation pour que l’appareil StorSimple puisse enregistrer des données en cas de perte d’alimentation au niveau du boîtier principal. Cette batterie est appelée *module de batterie de secours*. Le module de batterie de secours existe uniquement pour le boîtier principal de votre appareil StorSimple (le boîtier EBOD ne contient pas de module de batterie de secours).

Ce didacticiel explique comment :

* Retirer le module de batterie de secours
* Installer un nouveau module de batterie de secours
* Entretenir le module de batterie de secours

> [!IMPORTANT]
> Avant le retrait et le remplacement d’un module de batterie de secours, passez en revue les informations de sécurité dans l’[Introduction au remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).
> 
> 

## Retirer le module de batterie de secours
Le module de batterie de secours pour votre appareil StorSimple est une unité remplaçable sur site. Avant son installation dans le module PCM, le module de batterie doit être conservé dans son emballage d’origine. Procédez comme suit pour supprimer la batterie de secours.

#### Pour retirer le module de batterie de secours
1. Dans le portail Azure Classic, accédez à **Appareils** > **Maintenance** > **État du matériel**. Sous **Composants partagés**, regardez l’état de la batterie.
2. Identifiez le PCM dans lequel la batterie est défectueuse. La figure 1 illustre l’arrière de l’appareil StorSimple.
   
    ![Fond du panier des modules du boîtier principal de l’appareil](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figure 1** Arrière de l’appareil principal avec les modules PCM et de contrôleur
   
   | Étiquette | Description |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Contrôleur 0 |
   | 4 |Contrôleur 1 |
   
    Comme l’illustre le numéro 3 dans la figure 2, le voyant LED de surveillance sur PCM 0 qui correspond à **Panne de batterie** doit être allumé.
   
    ![Fond du panier du module PCM de l’appareil avec voyants LED de surveillance](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figure 2** Arrière du PCM avec les voyants LED de surveillance
   
   | Étiquette | Description |
   |:--- |:--- |
   | 1 |Panne d’alimentation secteur |
   | 2 |Panne de ventilateur |
   | 3 |Panne de batterie |
   | 4 |PCM OK |
   | 5 |Panne d’alimentation secteur |
   | 6 |Batterie saine |
3. Pour retirer le PCM dont la batterie est défectueuse, suivez les étapes de la procédure [Retrait d’un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).
4. Une fois le PCM retiré, soulevez la poignée du module de batterie et faites-la pivoter vers le haut, comme l’illustre la figure ci-après, puis tirez dessus pour extraire la batterie.
   
    ![Retrait de la batterie du module PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figure 3** Retrait de la batterie du PCM
5. Placez le module dans l’emballage de l’unité remplaçable sur site.
6. Renvoyez l’unité défectueuse à Microsoft qui en assurera l’entretien et la manutention.

## Installer un nouveau module de batterie de secours
Procédez comme suit pour installer le module de batterie de remplacement dans le PCM du boîtier principal de votre appareil StorSimple.

#### Pour installer le module de batterie
1. Orientez correctement le module de batterie de secours dans le PCM.
2. Appuyez sur la poignée du module de batterie jusqu’en bas pour placer le connecteur.
3. Remplacez le PCM dans le boîtier principal en procédant de la manière décrite dans [Remplacement d’un module d’alimentation et de refroidissement (PCM) sur votre appareil StorSimple](storsimple-power-cooling-module-replacement.md).
4. Une fois le remplacement terminé, accédez à **Appareils** > **Maintenance** > **État du matériel** dans le portail Azure Classic. Vérifiez l'état de la batterie pour vous assurer que l'installation a réussi. Un état vert indique que la batterie est saine.

## Entretenir le module de batterie de secours
Dans votre appareil StorSimple, le module de batterie de secours de votre appareil alimente le contrôleur en cas de panne de courant. Il permet à l’appareil StorSimple d’enregistrer des données critiques avant de s’arrêter de façon contrôlée. Ce système, équipé de deux batteries entièrement chargées dans les PCM, peut faire face à deux pannes de courant consécutives.

Dans le portail Azure Classic, l’option **Statut matériel** dans la page **Maintenance** indique si la batterie ne fonctionne pas correctement ou si elle approche de la fin de sa durée de vie. L’état de la batterie est indiqué par **Batterie dans PCM 0** ou **Batterie dans PCM 1** sous **Composants partagés**. Cette page indique l’état **DÉTÉRIORÉ** si la fin de durée de vie approche et l’état **ÉCHEC** si elle est atteinte.

> [!NOTE]
> La batterie peut signaler **ÉCHEC** quand elle a juste besoin d’être chargée.
> 
> 

Si l’état **DÉTÉRIORÉ** s’affiche, nous vous recommandons de procéder ainsi :

* Le système a peut-être récemment subi une panne ou des opérations de maintenance peuvent être en cours sur les batteries. Observez le système pendant 12 heures avant de continuer.
  
  * Si l’état est toujours **DÉTÉRIORÉ** après 12 heures de branchement sur secteur alors que les contrôleurs et les PCM sont en cours d’exécution, vous devez changer la batterie. Veuillez [contacter le support Microsoft](storsimple-contact-microsoft-support.md) pour obtenir un module de batterie de secours de remplacement.
  * Si l’état redevient opérationnel après 12 heures, cela veut dire que la batterie fonctionne et qu’elle avait juste besoin d’être chargée dans le cadre de la maintenance.
* Si aucune panne n’est survenue et que le PCM est activé et branché sur secteur, la batterie doit être remplacée. Veuillez [contacter le support Microsoft](storsimple-contact-microsoft-support.md) pour commander un module de batterie de secours de remplacement.

> [!IMPORTANT]
> Jetez la batterie en panne en respectant les réglementations nationales et locales applicables.
> 
> 

## Étapes suivantes
En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0817_2016-->