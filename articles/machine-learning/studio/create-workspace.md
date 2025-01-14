---
title: Créer un espace de travail Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Pour utiliser Azure Machine Learning Studio, vous devez disposer d’un espace de travail Machine Learning Studio. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109346"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Créer et partager un espace de travail Azure Machine Learning Studio

Pour utiliser Azure Machine Learning Studio, vous devez disposer d’un espace de travail Machine Learning Studio. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.

## <a name="create-a-studio-workspace"></a>Créer un espace de travail Studio

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

    > [!NOTE]
    > Pour vous connecter et créer un espace de travail Studio, vous devez être administrateur d'abonnements Azure. 
    >
    > 

2. Cliquez sur **+Nouveau**.

3. Dans la zone de recherche, tapez **espace de travail Machine Learning Studio** et sélectionnez l’élément correspondant. Ensuite, cliquez sur **Créer** en bas de la page.

4. Entrez les informations sur votre espace de travail :

   - Le *Nom de l’espace de travail* peut contenir jusqu’à 260 caractères. Il ne doit pas se terminer par un espace. Le nom ne peut pas contenir ces caractères : `< > * % & : \ ? + /`
   - Le *plan de service web* que vous choisissez (ou créez), ainsi que le *niveau tarifaire* associé que vous sélectionnez, est utilisé si vous déployez des services web à partir de cet espace de travail.

     ![Créer un espace de travail Studio](./media/create-workspace/create-new-workspace.png)

5. Cliquez sur **Créer**.

> [!NOTE]
> Machine Learning Studio s'appuie sur un compte de stockage Azure fourni par vous pour enregistrer les données intermédiaires au moment de l'exécution du flux de travail. Une fois l'espace de travail créé, si le compte de stockage est supprimé ou si les clés d'accès sont modifiées, l'espace de travail cesse de fonctionner et toutes ses expériences échouent.
Si vous supprimez accidentellement le compte de stockage, recréez-le avec le même nom dans la même région que celui que vous avez supprimé et resynchronisez la clé d'accès. Si vous avez modifié les clés d’accès du compte de stockage, resynchronisez-les dans l’espace de travail à l’aide du portail Azure.

Une fois l’espace de travail déployé, vous pouvez l’ouvrir dans Machine Learning Studio.

1. Accédez à Machine Learning Studio à l’adresse [https://studio.azureml.net/](https://studio.azureml.net/).

2. Sélectionnez votre espace de travail dans le coin supérieur droit.

    ![Sélectionner un espace de travail](./media/create-workspace/open-workspace.png)

3. Cliquez sur **my experiments (mes expérimentations)** .

    ![Ouvrir des expérimentations](./media/create-workspace/my-experiments.png)

Pour plus d'informations sur la gestion de votre espace de travail Studio, consultez [Gestion d'un espace de travail Azure Machine Learning Studio](manage-workspace.md).
Si vous rencontrez un problème lors de la création de votre espace de travail, consultez [Guide de résolution des problèmes : Créer un espace de travail Machine Learning Studio et s'y connecter](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Partager un espace de travail Azure Machine Learning Studio
Une fois l'espace de travail Machine Learning Studio créé, vous pouvez inviter des utilisateurs à partager l'accès à celui-ci et à l'ensemble de ses expérimentations, jeux de données, bloc-notes, etc. Vous pouvez ajouter des utilisateurs dans l’un des deux rôles :

* **Utilisateur** : un utilisateur de l’espace de travail peut créer, ouvrir, modifier et supprimer des expérimentations, jeux de données, etc. dans l’espace de travail.
* **Propriétaire** : un propriétaire peut inviter et supprimer des utilisateurs dans l’espace de travail, ainsi que les actions qu’ils sont autorisés à effectuer.

> [!NOTE]
> Le compte d’administrateur qui crée l’espace de travail y est automatiquement ajouté en tant que propriétaire de l’espace de travail. Toutefois, les autres administrateurs ou utilisateurs de cet abonnement ne bénéficient pas automatiquement d’un accès à l’espace de travail : vous devez les inviter explicitement.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Pour partager un espace de travail Studio

1. Connectez-vous à Machine Learning Studio à l’adresse [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Dans le panneau gauche, cliquez sur **SETTINGS (PARAMÈTRES)** .

3. Cliquez sur l’onglet **USERS (UTILISATEURS)** .

4. En bas de la page, cliquez sur **INVITE MORE USERS (INVITER D’AUTRES UTILISATEURS)** .

    ![Paramètres Studio](./media/create-workspace/settings.png)

5. Entrez une ou plusieurs adresses e-mail. Les utilisateurs ont besoin d’un compte Microsoft valide ou d’un compte d’entreprise (issu d’Azure Active Directory).

6. Indiquez si vous voulez ajouter des utilisateurs en tant que propriétaire ou utilisateur.

7. Cliquez sur la coche **OK** .

Chaque utilisateur ajouté reçoit un e-mail contenant des instructions de connexion à l’espace de travail partagé.

> [!NOTE]
> Pour que les utilisateurs puissent déployer ou gérer des services web dans cet espace de travail, ils doivent être collaborateurs ou administrateurs dans l’abonnement Azure. 



