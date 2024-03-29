---
title: Gérer les images personnalisées Azure DevTest Labs pour créer des machines virtuelles | Microsoft Docs
description: Apprenez à créer une image personnalisée à partir d’un fichier de disque dur virtuel ou à partir d’une machine virtuelle existante dans Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: tarcher

---
# Gérer les images personnalisées Azure DevTest Labs pour créer des machines virtuelles
Dans Azure DevTest Labs, les images personnalisées vous permettent de créer rapidement des machines virtuelles sans attendre que tous les logiciels requis soient installés sur la machine cible. Les images personnalisées vous permettent de pré-installer tous les logiciels dont vous avez besoin dans un fichier de disque dur virtuel et d’utiliser ensuite le fichier de disque dur virtuel pour créer une machine virtuelle. Étant donné que les logiciels sont déjà installés, le temps de création de machines virtuelles est beaucoup plus court. En outre, les images personnalisées sont utilisées pour cloner des machines virtuelles en créant une image personnalisée à partir d’une machine virtuelle, puis en créant des machines virtuelles à partir de cette image personnalisée.

Dans cet article, vous apprendrez comment :

* [Créer une image personnalisée à partir d’un fichier de disque dur virtuel](#create-a-custom-image-from-a-vhd-file) afin de pouvoir ensuite créer une machine virtuelle à partir de cette image personnalisée.
* [Créer une image personnalisée à partir d’une machine virtuelle](#create-a-custom-image-from-a-vm) pour le clonage rapide de machines virtuelles.

## Création d’une image personnalisée à partir d’un fichier VHD
Dans cette section, vous allez apprendre à créer une image personnalisée à partir d’un fichier VHD. Vous devez avoir accès à un fichier VHD valide pour effectuer toutes les étapes de cette section.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau du laboratoire, sélectionnez **Configuration**.
5. Dans le panneau **Configuration** du laboratoire, sélectionnez **Images personnalisées**.
6. Dans le panneau **Images personnalisées**, sélectionnez **+ Image personnalisée**.
   
    ![Ajouter une image personnalisée](./media/devtest-lab-create-template/add-custom-image.png)
7. Entrez le nom de l’image personnalisée. Ce nom s’affiche dans la liste des images de base quand vous créez une machine virtuelle.
8. Entrez la description de l’image personnalisée. Cette description s’affiche dans la liste des images de base quand vous créez une machine virtuelle.
9. Sélectionnez **Fichier VHD**.
10. Si vous avez accès à un fichier VHD qui n’est pas répertorié, ajoutez-le en suivant les instructions de la section [Télécharger un fichier VHD](#upload-a-vhd-file) et revenez ici après avoir terminé.
11. Sélectionnez le fichier VHD souhaité.
12. Cliquez sur **OK** pour fermer le panneau **Fichier VHD**.
13. Sélectionnez **Configuration du système d’exploitation**.
14. Dans l’onglet **Configuration du système d’exploitation**, sélectionnez **Windows** ou **Linux**.
15. Si vous sélectionnez **Windows**, cochez la case pour indiquer si *Sysprep* a été exécuté sur la machine.
16. Cliquez sur **OK** pour fermer le panneau **Configuration du système d’exploitation**.
17. Cliquez sur **OK** pour créer l’image personnalisée.
18. Accédez à la section [Étapes suivantes](#next-steps).

### Téléchargement d’un fichier VHD
Pour ajouter une image personnalisée, vous devez avoir accès à un fichier VHD.

1. Dans le panneau **Fichier VHD**, sur **Télécharger un fichier VHD à l’aide de PowerShell**.
   
    ![Télécharger une image](./media/devtest-lab-create-template/upload-image-using-psh.png)
2. Le panneau suivant affiche des instructions pour modifier et exécuter un script PowerShell qui télécharge un fichier VHD sur votre abonnement Azure. **Remarque :** la taille du fichier VHD et votre vitesse de connexion influent sur la durée de ce processus.

## Créer une image personnalisée à partir d’une machine virtuelle
Si vous disposez d’une machine virtuelle qui est déjà configurée, vous pouvez créer une image personnalisée à partir de cette machine virtuelle et ensuite utiliser cette image personnalisée pour créer d’autres machines virtuelles identiques. Les étapes suivantes expliquent comment créer une image personnalisée à partir d’une machine virtuelle :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Sur le panneau du laboratoire, sélectionnez **Mes machines virtuelles**.
5. Dans le panneau **Mes machines virtuelles** du laboratoire, sélectionnez la machine virtuelle à partir de laquelle vous souhaitez créer l’image personnalisée.
6. Sur le panneau de la machine virtuelle, sélectionnez **Créer une image personnalisée (VHD)**.
   
    ![Élément de menu Créer une image personnalisée](./media/devtest-lab-create-template/create-custom-image.png)
7. Dans le panneau **Créer une image**, entrez un nom et une description pour votre image personnalisée. Ces informations s’affichent dans la liste de bases lorsque vous créez une machine virtuelle.
   
    ![Panneau Créer une image personnalisée](./media/devtest-lab-create-template/create-custom-image-blade.png)
8. Choisissez si sysprep a été exécuté sur la machine virtuelle. Si sysprep n’a pas été exécuté sur la machine virtuelle, sélectionnez si vous souhaitez exécuter sysprep lorsqu’une machine virtuelle est créée à partir de cette image personnalisée.
9. Cliquez sur **OK** lorsque vous avez terminé de créer l’image personnalisée.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Billets de blog connexes
* [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
* [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## Étapes suivantes
Une fois que vous avez ajouté une image personnalisée à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->