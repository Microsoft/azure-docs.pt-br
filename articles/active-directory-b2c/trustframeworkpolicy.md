---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: Especifica o elemento TrustFrameworkPolicy de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470730"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem os elementos da política, como o esquema de declarações, as transformações de declarações, as definições de conteúdo, os provedores de declarações, os perfis técnicos, o percurso do usuário e as etapas de orquestração. Cada arquivo de política é definido dentro de elemento **TrustFrameworkPolicy** de nível superior de um arquivo de política.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


O elemento **TrustFrameworkPolicy** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sim | A versão do esquema a ser usado para executar a política. O valor deve ser `0.3.0.0` |
| TenantObjectId | Não | O identificador de objeto exclusivo do locatário Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Sim | O identificador exclusivo do locatário ao qual essa política pertence. |
| PolicyId | Sim | O identificador exclusivo da política. Esse identificador deve ter o *B2C_1A_* como prefixo |
| PublicPolicyUri | Sim | O URI para a política, o que é uma combinação de ID do locatário e ID da política. |
| DeploymentMode | Não | Valores possíveis: `Production` , ou `Development` . `Production` é o padrão. Use essa propriedade para depurar sua política. Para obter mais informações, veja [Coleta de Logs](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Não | O ponto de extremidade usado para registro em log. O valor deve ser definido como `urn:journeyrecorder:applicationinsights` se o atributo existir. Para obter mais informações, veja [Coleta de Logs](troubleshoot-with-application-insights.md). |


O exemplo a seguir mostra como especificar o elemento **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| O identificador de uma política de base. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Os blocos de construção da sua política. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Uma coleção de provedores de declarações. |
| [UserJourneys](userjourneys.md) | 0:1 | Uma coleção de percursos do usuário. |
| [RelyingParty](relyingparty.md) | 0:1 | Uma definição de uma política de terceira parte confiável. |

Para herdar de uma política de outra política, um elemento **BasePolicy** deve ser declarado no elemento **TrustFrameworkPolicy** do arquivo de política. O elemento **BasePolicy** é uma referência à política de base da qual essa política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do locatário do Azure AD B2C. |
| PolicyId | 1:1 | O identificador da política pai. |


O exemplo a seguir mostra como especificar uma política de base. Essa política **B2C_1A_TrustFrameworkExtensions** é derivada da política **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

