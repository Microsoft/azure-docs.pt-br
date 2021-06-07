---
title: Gerenciar políticas de consentimento do aplicativo no Azure AD
description: Saiba como gerenciar políticas internas e personalizadas de consentimento de aplicativo para controlar quando o consentimento pode ser concedido.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9c269e2ab37a08e48eedd3ee468080a382f9a8e3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558722"
---
# <a name="manage-app-consent-policies"></a>Gerenciar as políticas de consentimento do aplicativo

Com o PowerShell do Azure AD, você pode exibir e gerenciar políticas de consentimento do aplicativo.

Uma política de consentimento de aplicativo consiste em um ou mais conjuntos de condição "inclui" e zero ou mais conjuntos de condição "exclusões". Para que um evento seja considerado em uma política de consentimento de aplicativo, ele deve corresponder *a pelo menos* um conjunto de condições de "inclusões" e não deve corresponder a *qualquer* condição de "exclusão" definida.

Cada conjunto de condições consiste em várias condições. Para que um evento corresponda a um conjunto de condições, *todas as* condições no conjunto de condições devem ser atendidas.

As políticas de consentimento do aplicativo em que a ID começa com "Microsoft-" são políticas internas. Algumas dessas políticas internas são usadas em funções de diretório internas existentes. Por exemplo, a `microsoft-application-admin` política de consentimento do aplicativo descreve as condições sob as quais as funções administrador do aplicativo e administrador do aplicativo de nuvem têm permissão para conceder o consentimento do administrador em todo o locatário. As políticas internas podem ser usadas em funções de diretório personalizadas e para definir as configurações de consentimento do usuário, mas não podem ser editadas ou excluídas.

## <a name="pre-requisites"></a>Pré-requisitos

1. Verifique se você está usando o módulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview). Esta etapa será importante se você tiver instalado os módulos [AzureAD](/powershell/module/azuread/) e [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Conecte-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Listar políticas de consentimento de aplicativo existentes

É uma boa ideia começar a se familiarizar com as políticas de consentimento do aplicativo existentes em sua organização:

1. Listar todas as políticas de consentimento do aplicativo:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Exibir os conjuntos de condição "inclui" de uma política:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Exibir os conjuntos de condições de "exclusões":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Criar uma política de consentimento de aplicativo personalizada

Siga estas etapas para criar uma política de consentimento de aplicativo Personalizada:

1. Crie uma nova política de consentimento de aplicativo vazia.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Adicione conjuntos de condição "inclui".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Repita essa etapa para adicionar conjuntos de condições adicionais de "inclusão".

1. Opcionalmente, adicione os conjuntos de condição "exclusões".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Repita essa etapa para adicionar outros conjuntos de condição de "exclusão".

Depois que a política de consentimento do aplicativo tiver sido criada, você poderá [permitir o consentimento do usuário](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) sujeito a essa política.

## <a name="delete-a-custom-app-consent-policy"></a>Excluir uma política de consentimento de aplicativo personalizada

1. O seguinte mostra como você pode excluir uma política de consentimento de aplicativo personalizada. **Essa ação não pode ser desfeita.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> As políticas de consentimento de aplicativo excluídas não podem ser restauradas. Se você excluir acidentalmente uma política de consentimento de aplicativo personalizada, será necessário recriar a política.

---

### <a name="supported-conditions"></a>Condições com suporte

A tabela a seguir fornece a lista de condições com suporte para políticas de consentimento do aplicativo.

| Condição | Descrição|
|:---------------|:----------|
| PermissionClassification | A [classificação de permissão](configure-permission-classifications.md) para a permissão que está sendo concedida ou "All" para corresponder a qualquer classificação de permissão (incluindo permissões que não são classificadas). O padrão é "All". |
| PermissionType | O tipo de permissão da permissão que está sendo concedida. Use "Application" para permissões de aplicativo (por exemplo, funções de aplicativo) ou "delegadas" para permissões delegadas. <br><br>**Observação**: o valor "delegatedUserConsentable" indica permissões delegadas que não foram configuradas pelo editor de API para exigir o consentimento do administrador — esse valor pode ser usado em políticas de concessão de permissão interna, mas não pode ser usado em políticas de concessão de permissão personalizada. Obrigatórios. |
| ResourceApplication | A **AppID** do aplicativo de recurso (por exemplo, a API) para o qual uma permissão está sendo concedida, ou "any" para corresponder a qualquer aplicativo de recurso ou API. O padrão é "any". |
| Permissões | A lista de IDs de permissão para as permissões específicas a serem correspondidas ou uma lista com o valor único "All" para corresponder a qualquer permissão. O padrão é o único valor "All". <ul><li>As IDs de permissão delegadas podem ser encontradas na propriedade **OAuth2Permissions** do objeto de entidade de segurança da API.</li><li>As IDs de permissão de aplicativo podem ser encontradas na propriedade **AppRoles** do objeto SERVICEPRINCIPALNAME da API.</li></ol> |
| ClientApplicationIds | Uma lista de valores de **AppID** para os aplicativos cliente a serem correspondidos, ou uma lista com o valor único "All" para corresponder a qualquer aplicativo cliente. O padrão é o único valor "All". |
| ClientApplicationTenantIds | Uma lista de Azure Active Directory IDs de locatário em que o aplicativo cliente está registrado ou uma lista com o valor único "All" para corresponder aos aplicativos cliente registrados em qualquer locatário. O padrão é o único valor "All". |
| ClientApplicationPublisherIds | Uma lista de IDs de Microsoft Partner Network (MPN) para os [Publicadores verificados](../develop/publisher-verification-overview.md) do aplicativo cliente ou uma lista com o valor único "All" para corresponder aos aplicativos cliente de qualquer Publicador. O padrão é o único valor "All". |
| ClientApplicationsFromVerifiedPublisherOnly | Defina como `$true` para corresponder somente em aplicativos cliente com [Publicadores verificados](../develop/publisher-verification-overview.md). Defina como `$false` para corresponder a qualquer aplicativo cliente, mesmo que ele não tenha um Publicador verificado. O padrão é `$false`. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais:

* [Definir configurações de consentimento do usuário](configure-user-consent.md)
* [Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
* [Saiba como gerenciar o consentimento em aplicativos e avaliar solicitações de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no Microsoft Q&A](/answers/products/)