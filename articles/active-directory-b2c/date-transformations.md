---
title: Exemples de transformations de revendications Date pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications Date pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ff96b9a63e7340788ef2474ce9934145c184e1e1
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542767"
---
# <a name="date-claims-transformations"></a>Transformations de revendications Date

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications Date du schéma Infrastructure d’expérience d’identité dans Azure Active Directory (Azure AD) B2C. Pour plus d’informations, consultez [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Vérifie qu’une revendication de date et d’heure (type de données string) est supérieure à une deuxième revendication de date et d’heure (type de données string) et lève une exception.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | chaîne | Type de la première revendication, qui doit être supérieure à la deuxième revendication. |
| inputClaim | rightOperand | chaîne | Type de la deuxième revendication, qui doit être inférieure à la première revendication. |
| InputParameter | AssertIfEqualTo | booléenne | Spécifie si cette assertion doit passer si l’opérande gauche est égal à l’opérande droit. |
| InputParameter | AssertIfRightOperandIsNotPresent | booléenne | Spécifie si cette assertion doit passer si l’opérande droit est manquante. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Spécifie le nombre de millisecondes autorisées entre les deux dates pour considérer les heures comme égales (par exemple, pour tenir compte du décalage d’horloge). |

La transformation de revendication **AssertDateTimeIsGreaterThan** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). Les métadonnées de profil technique autodéclaré **DateTimeGreaterThan** contrôlent le message d’erreur présenté à l’utilisateur par le profil technique.

![Exécution de AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

L’exemple suivant compare la revendication `currentDateTime` à la revendication `approvedDateTime`. Une erreur est générée si `currentDateTime` est supérieur à `approvedDateTime`. La transformation traite les valeurs comme étant égales si elles ont une différence de 5 minutes (30 000 millisecondes).

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Le profil technique de validation `login-NonInteractive` appelle la transformation de revendication `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Le profil technique autodéclaré appelle le profil technique de validation **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Résultat : Erreur levée


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Convertit un ClaimType **Date** en un ClaimType **DateTime**. La transformation des revendications convertit le format d’heure et ajoute 12:00:00 AM à la date.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | Le ClaimType à convertir. |
| OutputClaim | outputClaim | dateTime | ClaimType généré après l’appel de cette ClaimsTransformation. |

L’exemple suivant illustre la conversion de la revendication `dateOfBirth` (type de données de date) en une autre revendication `dateOfBirthWithTime` (type de données date/heure).

```XML
<ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim**: 2019-06-01
- Revendications de sortie :
    - **outputClaim**: 1559347200 (June 1, 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtient la date et l’heure UTC actuelles et ajoute la valeur à un ClaimType.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | ClaimType généré après l’appel de cette ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

* Revendications de sortie :
    * **currentDateTime**: 1534418820 (August 16, 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Détermine si un dateTime est supérieur, inférieur ou égal à un autre. Le résultat est un nouveau ClaimType booléen avec une valeur True ou False.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | Le premier dateTime à comparer. Une valeur null lève une exception. |
| InputClaim | secondDateTime | dateTime | Le deuxième dateTime à comparer. Traite la valeur null comme le dateTime. |
| InputParameter | operator | chaîne | Une des valeurs suivantes : identique, plus tard ou antérieur. |
| InputParameter | timeSpanInSeconds | int | Ajoute l’intervalle de temps au premier dateTime. |
| OutputClaim | result | booléenne | ClaimType généré après l’appel de cette ClaimsTransformation. |

Utilisez cette transformation de revendication pour déterminer si deux ClaimTypes sont égales, ou bien si l’une est supérieure ou inférieure à l’autre. Par exemple, vous pouvez stocker la dernière fois qu’un utilisateur a accepté vos conditions d’utilisation du service. Après 3 mois, vous pouvez de nouveau lui demander d’accepter les conditions d’utilisation.
Pour exécuter la transformation de revendication,vous devez d’abord obtenir le dateTime actuel et la dernière fois que l’utilisateur a accepté les conditions d’utilisation.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="greater than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Paramètres d’entrée :
    - **opérateur** : supérieur à
    - **timeSpanInSeconds**: 7776000 (90 jours)
- Revendications de sortie : 
    - **résultat** : true
