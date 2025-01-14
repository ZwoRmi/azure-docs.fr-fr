---
title: Questions fréquentes (FAQ) sur la fonctionnalité Proxy d’application Azure Active Directory | Microsoft Docs
description: Découvrez les réponses aux questions fréquentes (FAQ) sur l’utilisation de la fonctionnalité Proxy d’application Azure AD pour publier des applications locales internes destinées à des utilisateurs distants.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 9743f25d89bed4e54b3deed815d1cf29030caff6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955405"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur la fonctionnalité Proxy d’application Azure Active Directory

Cette page répond aux questions fréquemment posées sur la fonctionnalité Proxy d’application Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Activation du proxy d’application Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Quelle est la licence exigée pour utiliser le proxy d’application Azure AD ?

Pour utiliser le proxy d’application Azure AD, vous devez posséder une licence Azure AD Premium P1 ou P2. Pour plus d’informations sur les licences, consultez [Tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Pourquoi le bouton « Activer le proxy d’application » est-il grisé ?

Vérifiez que vous avez au moins une licence Azure AD Premium P1 ou P2 et qu’un connecteur de proxy d’application Azure AD est installé. Une fois votre premier connecteur correctement installé, le service de proxy d’application Azure AD s’active automatiquement.

## <a name="connector-configuration"></a>Configuration des connecteurs

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Les services du connecteur de proxy d’application peuvent-ils s’exécuter dans un contexte utilisateur différent de celui par défaut ?

Non, ce scénario n’est pas pris en charge. Les paramètres par défaut sont :

- Connecteur de proxy d’application Microsoft AAD - WAPCSvc - Network Service
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Mon application back-end est hébergée sur plusieurs serveurs web et exige une persistance de session utilisateur (adhérence). Comment puis-je obtenir celle-ci ? 

Pour obtenir des recommandations, consultez [Haute disponibilité et équilibrage de charge de vos applications et connecteurs de proxy d’application](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Puis-je placer un appareil proxy de transfert entre le ou les serveurs du connecteur et le serveur d’applications back-end ?

Non, ce scénario n’est pas pris en charge. Seuls le connecteur et les services de mise à jour peuvent être configurés pour utiliser un proxy de transfert pour le trafic sortant vers Azure. Consultez [Travailler avec des serveurs proxy locaux existants](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>L’arrêt SSL (inspection ou accélération SSL/HHTPS) sur le trafic entre les serveurs du connecteur et Azure est-il pris en charge ?

Le connecteur de proxy d’application effectue une authentification par certificat auprès d’Azure. L’arrêt SSL (inspection ou accélération SSL/HHTPS) interrompt cette méthode d’authentification et n’est pas pris en charge. Le trafic entre le connecteur et Azure doit ignorer tous les appareils qui effectuent un arrêt SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Dois-je créer un compte dédié pour inscrire le connecteur auprès du proxy d’application Azure AD ?

Vous n’avez aucune raison de le faire. Tout compte d’administrateur général ou d’administrateur d’application peut fonctionner. Les informations d’identification entrées pendant l’installation ne sont pas utilisées après le processus d’inscription. En revanche, un certificat est émis pour le connecteur, puis utilisé à des fins d’authentification par la suite.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Comment puis-je superviser les performances du connecteur de proxy d’application Azure AD ?

Des compteurs Analyseur de performances sont installés de concert avec le connecteur. Pour les voir :  

1. Sélectionnez **Démarrer**, tapez « Perfmon », puis appuyez sur Entrée.
2. Sélectionnez **Analyseur de performances**, puis cliquez sur l’icône **+** verte.
3. Ajoutez les compteurs **Connecteur de proxy d’application Microsoft AAD** à superviser.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Le connecteur de proxy d’application Azure AD doit-il se trouver sur le même sous-réseau que la ressource ?

Le connecteur ne doit pas obligatoirement se trouver sur le même sous-réseau. En revanche, il a besoin d’une résolution de noms (DNS, fichier hosts) pour la ressource et de la connectivité réseau appropriée (routage vers la ressource, ports ouverts sur la ressource, etc.). Pour obtenir des recommandations, consultez [Considérations sur la topologie de réseau lors de l’utilisation du proxy d’application Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Configuration de l’application

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Quelle est la durée du délai d’expiration back-end par défaut « long » ? Peut-il être prolongé ?

La durée par défaut s’élève à 85 secondes. Le paramètre « long » s’élève à 180 secondes. Il n’est pas possible d’augmenter la limite du délai d’expiration.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Comment puis-je modifier la page d’arrivée chargée par mon application ?

À partir de la page des inscriptions d’applications, vous pouvez remplacer l’URL de la page d’accueil par l’URL externe souhaitée pour la page d’arrivée. La page spécifiée est chargée quand l’application est lancée à partir de Mes applications ou du portail Office 365. Pour connaître les étapes de configuration, consultez [Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page).

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Est-il possible de publier uniquement des applications IIS ? Qu’en est-il des applications web s’exécutant sur des serveurs web non-Windows ? Le connecteur doit-il être installé sur un serveur sur lequel IIS est installé ?

Non, aucune configuration IIS n’est requise pour les applications publiées. Vous pouvez publier des applications web qui s’exécutent sur des serveurs autres que Windows Server. En revanche, vous ne pourrez peut-être pas utiliser la préauthentification avec un serveur autre que Windows Server, selon que le serveur web prend en charge ou non Negotiate (authentification Kerberos). IIS n’est pas exigé sur le serveur sur lequel le connecteur est installé.

## <a name="integrated-windows-authentication"></a>Authentification Windows intégrée

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quand dois-je utiliser la méthode PrincipalsAllowedToDelegateToAccount lors de la configuration de la délégation contrainte Kerberos (KCD) ?

La méthode PrincipalsAllowedToDelegateToAccount est utilisée lorsque les serveurs du connecteur se trouvent dans un domaine différent de celui du compte de service de l’application web. Elle exige l’utilisation d’une délégation contrainte basée sur les ressources.
Si les serveurs du connecteur et le compte de service de l’application web se trouvent dans le même domaine, vous pouvez utiliser Utilisateurs et ordinateurs Active Directory pour configurer les paramètres de délégation sur chacun des comptes d’ordinateur du connecteur, ce qui leur permet de déléguer au SPN cible.

Si les serveurs du connecteur et le compte de service de l’application web se trouvent dans des domaines différents, la délégation basée sur les ressources est utilisée. Les autorisations de délégation sont configurées sur le serveur web cible et le compte de service de l’application web. Cette méthode de délégation contrainte est relativement nouvelle. Elle a été introduite dans Windows Server 2012, qui prend en charge la délégation interdomaines en permettant au propriétaire de la ressource (service web) de contrôler la machine et les comptes de service qui peuvent déléguer. Il n’y a pas d’interface utilisateur pour faciliter cette configuration. Vous devez donc utiliser PowerShell.
Pour plus d’informations, consultez le livre blanc [Understanding Kerberos Constrained Delegation with Application Proxy](http://aka.ms/kcdpaper) (Présentation de la délégation contrainte Kerberos avec le proxy d’application).

## <a name="pass-through-authentication"></a>Authentification directe

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Puis-je utiliser des stratégies d’accès conditionnel pour les applications publiées avec authentification directe ?

Les stratégies d’accès conditionnel s’appliquent uniquement aux utilisateurs correctement préauthentifiés dans Azure AD. L’authentification directe ne déclenche pas l’authentification Azure AD, donc les stratégies d’accès conditionnel ne peuvent pas être appliquées. Avec l’authentification directe, des stratégies MFA doivent être implémentées sur le serveur local, si possible, ou en activant la préauthentification auprès du proxy d’application Azure AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Puis-je publier une application web en exigeant une authentification par certificat client ?

Non, ce scénario n’est pas pris en charge, car le proxy d’application arrête le trafic TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publication de la passerelle des services Bureau à distance

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Comment puis-je publier la passerelle des services Bureau à distance sur le proxy d’application Azure AD ?

Reportez-vous à [Publier le Bureau à distance avec le proxy d’application Azure AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Puis-je utiliser la délégation contrainte Kerberos dans le scénario de publication de la passerelle des services Bureau à distance ?

Non, ce scénario n’est pas pris en charge.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Mes utilisateurs n’utilisent pas Internet Explorer 11 et le scénario de préauthentification ne fonctionne pas pour eux. Est-ce normal ?

Oui, c’est normal. Le scénario de préauthentification exige un contrôle ActiveX, qui n’est pas pris en charge dans les navigateurs tiers.

### <a name="is-the-remote-desktop-web-client-supported"></a>Le client web Bureau à distance est-il pris en charge ?

Non, ce scénario n’est pas pris en charge actuellement. Suivez notre forum de commentaires [UserVoice](https://aka.ms/aadapuservoice) pour être tenu informé de l’évolution de cette fonctionnalité.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Après avoir configuré le scénario de préauthentification, je me suis rendu compte que l’utilisateur doit s’authentifier deux fois : tout d’abord dans le formulaire de connexion Azure AD, puis dans le formulaire de connexion RDWeb. Est-ce normal ? Comment puis-je réduire le nombre de connexions à une seule ?

Oui, c’est normal. Si l’ordinateur de l’utilisateur est joint à Azure AD, l’utilisateur se connecte à Azure AD automatiquement. Il doit fournir ses informations d’identification uniquement dans le formulaire de connexion RDWeb.

## <a name="sharepoint-publishing"></a>Publication SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Comment puis-je publier SharePoint sur le proxy d’application Azure AD ?

Reportez-vous à [Activer l’accès distant à SharePoint avec le proxy d’application Azure AD](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publication des services de fédération Active Directory (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Puis-je utiliser le proxy d’application Azure AD en tant que proxy AD FS (comme le proxy d’application web) ?

Non. Le proxy d’application Azure AD est conçu pour fonctionner avec Azure AD et ne répond pas aux exigences d’un rôle de proxy AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket prend-il en charge des applications autres que QlikSense ?

Actuellement, la prise en charge du protocole WebSocket est toujours en préversion publique et peut ne pas fonctionner pour d’autres applications. Malgré tout, certains clients ont tant bien que mal réussi à utiliser le protocole WebSocket avec d’autres applications. Si vous testez de tels scénarios, nous serions ravis de connaître vos résultats. Envoyez-nous vos commentaires à l’adresse aadapfeedback@microsoft.com.

## <a name="link-translation"></a>Traduction de liens

### <a name="does-using-link-translation-affect-performance"></a>L’utilisation de la traduction de liens affecte-t-elle les performances ?

Oui. La traduction de liens affecte les performances. Le service de proxy d’application analyse l’application à la recherche de liens codés en dur, puis les remplace par leurs URL externes publiées respectives avant de les présenter à l’utilisateur. 

Pour optimiser les performances, nous vous recommandons d’utiliser des URL internes et externes identiques en configurant des [domaines personnalisés](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Si vous n’avez pas la possibilité d’utiliser des domaines personnalisés, vous pouvez améliorer les performances de la traduction des liens à l’aide de l’extension de connexion sécurisée Mes applications ou du navigateur Microsoft Edge sur mobile. Consultez [Rediriger les liens codés en dur pour les applications publiées avec le proxy d’application Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Caractères génériques

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Comment puis-je utiliser des caractères génériques pour publier deux applications portant le même nom de domaine personnalisé, mais avec des protocoles différents, un pour HTTP et un pour HTTPS ?

Ce scénario n’est pas pris en charge directement. Vos possibilités pour ce scénario sont les suivantes :

1. Publiez les URL HTTP et HTTPS en tant qu’applications distinctes avec un caractère générique, mais donnez à chacune un nom de domaine personnalisé différent. Cette configuration fonctionne, car elles ont des URL externes différentes.

2. Publiez l’URL HTTPS par le biais d’une application générique. Publiez les applications HTTP séparément à l’aide de ces cmdlets PowerShell de proxy d’application :
   - [Gestion des applications de proxy d’application](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gestion des connecteurs de proxy d’application](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
