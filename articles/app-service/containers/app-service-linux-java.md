---
title: Prise en charge du langage Java pour Azure App Service sur Linux | Microsoft Docs
description: Guide du développeur pour déployer des applications Java avec Azure App Service sur Linux.
keywords: azure app service, application web, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 2b2256ef5802160dbaa66e2a098a798fcdc653d2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064493"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guide du développeur Java pour App Service sur Linux

Azure App Service sur Linux permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Linux entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les concepts et instructions clés aux développeurs Java qui utilisent App Service pour Linux. Si vous n’avez jamais utilisé Azure App Service pour Linux, commencez par lire [Démarrage rapide avec Java](quickstart-java.md). Des questions générales sur l’utilisation d’App Service pour Linux qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Journalisation et débogage des applications

Vous trouverez des rapports de performances, des visualisations de trafic et des contrôles d’intégrité pour chaque application dans le portail Azure. Consultez [Vue d’ensemble des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics) pour plus d’informations sur la façon d’accéder à ces outils de diagnostic et des les utiliser.

### <a name="ssh-console-access"></a>Accès à la console SSH 

La connectivité SSH à l’environnement Linux exécutant votre application est disponible. Consultez [Prise en charge SSH pour Azure App Service sur Linux](/azure/app-service/containers/app-service-linux-ssh-support) pour obtenir des instructions complètes sur la connexion au système Linux via votre navigateur web ou terminal local.

### <a name="streaming-logs"></a>Envoi de journaux

Pour un débogage et un dépannage rapides, vous pouvez envoyer des journaux à votre console en utilisant l’interface Azure CLI. Configurez l’interface CLI avec `az webapp log config` pour activer la journalisation :

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Ensuite, envoyez les journaux à votre console en utilisant `az webapp log tail` :

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Pour plus d’informations, consultez [Envoi de journaux avec l’interface Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>Journalisation des applications

Activez [Journal des applications](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob.

Si votre application utilise [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) pour le traçage, vous pouvez transférer ces traces pour révision vers Azure Application Insights en suivant les instructions de configuration des frameworks de journalisation dans [Exploration des journaux de traces Java dans Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Personnalisation et réglage

Azure App Service pour Linux prend en charge le réglage et la personnalisation prêts à l’emploi par le biais du portail Azure et de l’interface CLI. Consultez les articles suivants pour configurer des applications web spécifiques non-Java :

- [Configurer les paramètres App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer un nom de domaine personnalisé](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Activer le protocole SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ajouter un CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou d’autres options de runtime JVM dans les environnements Tomcat et Java SE, définissez JAVA_OPTS comme indiqué ci-dessous en tant que [paramètre d’application](/azure/app-service/web-sites-configure#app-settings). App Service Linux transmet ce paramètre comme variable d’environnement au runtime Java quand celui-ci démarre.

Dans le portail Azure, sous **Paramètres d’application** de l’application web, créez un paramètre d’application appelé `JAVA_OPTS` qui contient les paramètres supplémentaires tels que `$JAVA_OPTS -Xms512m -Xmx1204m`.

Pour configurer le paramètre d’application à partir du plug-in Maven Azure App Service Linux, ajoutez des étiquettes paramètre/valeur dans la section du plug-in Azure. L’exemple suivant définit une taille de segment de mémoire Java minimale et maximale spécifique :

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Les développeurs exécutant une seule application avec un seul emplacement de déploiement dans leur plan App Service peuvent utiliser les options suivantes :

- Instances B1 et S1 : -Xms1024m -Xmx1024m
- Instances B2 et S2 : -Xms3072m -Xmx3072m
- Instances B3 et S3 : -Xms6144m -Xmx6144m


Lors du réglage des paramètres de segment de mémoire de l’application, consultez les détails de votre plan App Service et prenez en compte qu’avec plusieurs applications et emplacements de déploiement, vous devez trouver l’allocation de mémoire optimale.

### <a name="turn-on-web-sockets"></a>Activer les sockets web

Activez la prise en charge des sockets web dans le portail Azure dans les **Paramètres d’application** de l’application. Vous devrez redémarrer l’application pour que le paramètre prenne effet.

Activez la prise en charge des sockets web via l’interface Azure CLI avec la commande suivante :

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Redémarrez ensuite votre application :

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Définir l’encodage de caractères par défaut 

Dans le portail Azure, sous **Paramètres d’application** de l’application web, créez un paramètre d’application appelé `JAVA_OPTS` avec la valeur `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Vous pouvez aussi configurer le paramètre d’application à l’aide du plug-in Maven App Service. Ajoutez le nom du paramètre et les étiquettes des valeurs dans la configuration du plug-in : 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-application"></a>Sécuriser l’application

Les applications Java exécutées dans App Service pour Linux présentent les mêmes [bonnes pratiques de sécurité](/azure/security/security-paas-applications-using-app-services) que les autres applications. 

### <a name="authenticate-users"></a>Authentifier les utilisateurs

Configurez l’authentification de l’application dans le portail Azure avec l’option **Authentification et autorisation**. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification.  Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) et les articles connexes sur d’autres fournisseurs d’identités.

Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

 Les développeurs Spring Boot peuvent utiliser le [démarreur Spring Boot Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) pour sécuriser les applications à l’aide d’API et d’annotations Spring Security familières.

### <a name="configure-tlsssl"></a>Configurer TLS/SSL

Suivez les instructions dans [Lier un certificat SSL personnalisé existant](/azure/app-service/app-service-web-tutorial-custom-ssl) pour charger un certificat SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Connexion aux sources de données

>[!NOTE]
> Si votre application utilise Spring Framework ou Spring Boot, vous pouvez définir les informations de connexion de base de données pour Spring Data JPA en tant que variables d’environnement [dans le fichier de propriétés de votre application]. Utilisez ensuite les [paramètres de l’application](/azure/app-service/web-sites-configure#app-settings) afin de définir ces valeurs pour votre application dans le portail Azure ou l’interface CLI.

Pour configurer Tomcat de sorte à utiliser des connexions managées aux bases de données à l’aide de Java Database Connectivity (JDBC) ou de l’API Java Persistence (JPA), commencez par personnaliser la variable d’environnement CATALINA_OPTS lu par Tomcat au démarrage. Définissez ces valeurs via un paramètre d’application dans le plug-in Maven App Service :

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Ou un paramètre App Service équivalent dans le portail Azure.

Ensuite, déterminez si la source de données doit être mise à la disposition d’une seule application ou de toutes les applications exécutées sur le plan App Service.

Pour les sources de données au niveau de l’application : 

1. Ajoutez un fichier `context.xml` s’il n’existe pas dans votre application web et ajoutez le répertoire `META-INF` de votre fichier WAR une fois que le projet est généré.

2. Dans ce fichier, ajoutez une entrée de chemin `Context` pour lier la source de données à une adresse JNDI. The

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Mettez à jour le fichier `web.xml` de votre application pour utiliser la source de données dans votre application.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

Pour les ressources au niveau du serveur partagées :

1. Copiez le contenu de `/usr/local/tomcat/conf` dans `/home/tomcat` sur votre instance App Service Linux à l’aide de SSH, si vous n’y avez pas déjà une configuration.

2. Ajoutez le contexte dans votre fichier `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Mettez à jour le fichier `web.xml` de votre application pour utiliser la source de données dans votre application.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Veillez à ce que les fichiers du pilote JDBC soient disponibles pour le chargeur de classes Tomcat en les mettant dans le répertoire `/home/tomcat/lib`. Pour charger ces fichiers sur votre instance App Service, procédez comme suit :  
    1. Installez l’extension webpp Azure App Service :
      ```azurecli-interactive
      az extension add –name webapp
      ```
    2. Exécutez la commande CLI suivante pour créer un tunnel SSH de votre système local vers App Service :
      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```
    3. Connectez-vous au port de tunneling local avec votre client SFTP et chargez les fichiers dans `/home/tomcat/lib`.

5. Redémarrez l’application App Service Linux. Tomcat rétablit `CATALINA_HOME` sur `/home/tomcat` et utilise les classes et la configuration mises à jour.

## <a name="docker-containers"></a>Conteneurs Docker

Pour utiliser le JDK Zulu pris en charge par Azure qui s’exécute sur App Service dans vos conteneurs, vérifiez que le `Dockerfile` de votre application utilise les images du [référentiel d’images Docker App Service Java](https://github.com/Azure-App-Service/java).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilité des runtimes et informations de prise en charge

App Service pour Linux prend en charge deux runtimes pour l’hébergement managé des application web Java :

- Le [conteneur servlet Tomcat](http://tomcat.apache.org/) pour exécuter les applications empaquetées sous forme de fichiers WAR (web archive). Les versions prises en charge sont les versions 8.5 et 9.0.
- L’environnement de runtime Java SE pour exécuter les applications empaquetées sous forme de fichiers JAR (Java archive). La seule version majeure prise en charge est Java 8.

## <a name="java-runtime-statement-of-support"></a>Informations de prise en charge du runtime Java 

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/products/zulu-and-zulu-enterprise/) fourni par [Azul Systems](https://www.azul.com/).

Les mises à jour de la version majeure sont fournies via de nouvelles options de runtime dans Azure App Service pour Linux. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

### <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/zulu/) pour développer en local.

### <a name="development-support"></a>Prise en charge du développement

La prise en charge produit du kit Azul Zulu Enterprise JDK est disponible si vous développez pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [plan de support Azure éligible](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-supportability/how-to-create-azure-support-request) avec le runtime Java App Service Linux via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Visitez le centre [Azure pour les développeurs Java](/java/azure/) pour trouver des guides de démarrage rapide Azure, des tutoriels et la documentation de référence Java.