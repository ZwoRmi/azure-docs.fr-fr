---
title: Conteneuriser une application .NET pour Service Fabric Mesh | Microsoft Docs
description: Ajouter la prise en charge Mesh à une application .NET existante
services: service-fabric-mesh
keywords: Conteneuriser Service Fabric Mesh
author: tylermsft
ms.author: twhitney
ms.date: 11/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 6f71f45d435b6be3358f79d8b6e72e4636d92ab6
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891860"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Conteneuriser une application .NET pour Service Fabric Mesh

Cet article vous montre comment ajouter la prise en charge de l’orchestration de conteneurs Service Fabric Mesh à une application .NET existante.

Dans Visual Studio 2017, vous pouvez ajouter la prise en charge de la conteneurisation dans les projets ASP.NET et les projets de console qui utilisent le .NET Framework complet.

> [!NOTE]
> Les projets .NET **Core** ne sont pas pris en charge.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Veillez à [configurer votre environnement de développement](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Cela comprend notamment l’installation du runtime Service Fabric, du SDK, de Docker, de Visual Studio 2017 ainsi que la création d’un cluster local.

## <a name="open-an-existing-net-app"></a>Ouvrir une application .NET existante

Ouvrez l’application à laquelle vous souhaitez ajouter la prise en charge de l’orchestration de conteneurs.

Pour tester un exemple, vous pouvez utiliser l’exemple de code [eShop](https://github.com/MikkelHegn/ContainersSFLab). Le reste de cet article suppose que nous utilisons ce projet, cela dit, vous pouvez appliquer ces étapes à votre propre projet.

Obtenez une copie du projet **eShop** :

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Une fois le projet téléchargé, dans Visual Studio 2017, ouvrez **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Ajouter la prise en charge des conteneurs
 
Pour ajouter la prise en charge de l’orchestration de conteneurs dans un projet ASP.NET ou un projet console existant, utilisez les outils Service Fabric Mesh de la façon suivante :

Dans l’Explorateur de solutions Visual Studio, cliquez sur le nom du projet (dans l’exemple, **eShopLegacyWebForms**), puis choisissez **Ajouter** > **Prise en charge des orchestrateurs de conteneurs**.
La boîte de dialogue **Ajouter la prise en charge des orchestrateurs de conteneurs** s’affiche.

![Boîte de dialogue Ajouter la prise en charge des orchestrateurs de conteneurs dans Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Choisissez **Service Fabric Mesh** dans la liste déroulante, puis cliquez sur **OK**.

L’outil vérifie ensuite que Docker est installé, ajoute un fichier Dockerfile à votre projet, puis tire (pull) une image docker pour votre projet.  
Un projet d’application Service Fabric Mesh est ajouté à votre solution. Il contient vos profils de publication et vos fichiers de configuration Mesh. Le nom du projet est le même que celui de votre projet, mais avec le mot « Application » accolé, par exemple : **eShopLegacyWebFormsApplication**. 

Dans le nouveau projet Mesh, vous verrez les deux dossiers suivants :
- **Ressources de l’application**, où se trouvent des fichiers YAML contenant des ressources Mesh supplémentaires, telles que le réseau.
- **Ressources du service**, où se trouve un fichier service.yaml qui décrit la façon dont votre application doit s’exécuter lors du déploiement.

Une fois que la prise en charge de l’orchestration des conteneurs a été ajoutée à votre application, vous pouvez appuyer sur **F5** pour déboguer votre application .NET sur votre cluster local Service Fabric Mesh. Voici l’application ASP.NET eShop exécutée sur un cluster Service Fabric Mesh : 

![Application eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Vous pouvez maintenant publier l’application dans Azure Service Fabric Mesh.

## <a name="next-steps"></a>Étapes suivantes

Pour publier une application dans Service Fabric Mesh : [Tutoriel - Déployer une application Service Fabric Mesh sur Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)