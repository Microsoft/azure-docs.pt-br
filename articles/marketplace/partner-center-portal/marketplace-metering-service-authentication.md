---
title: Estratégias de autenticação do serviço de medição do Marketplace | Azure Marketplace
description: Estratégias de autenticação de serviço de medição com suporte no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657066"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Estratégias de autenticação do serviço de medição do Marketplace

O serviço de medição do Marketplace dá suporte a duas estratégias de autenticação:

* [Token de segurança do Azure Active Directory](../../active-directory/develop/access-tokens.md)
* [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md) 

Explicaremos quando e como usar as diferentes estratégias de autenticação para enviar medidores personalizados com segurança usando o serviço de medição do Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Usar o token de segurança do Azure Active Directory

Os tipos de oferta aplicáveis são SaaS e aplicativos do Azure que podem ser acessados com o tipo de plano de aplicativo gerenciado.  

Envie medidores personalizados usando uma ID de aplicativo fixa do Azure AD predefinida para autenticar.

Para ofertas de SaaS, essa é a única opção disponível. É uma etapa obrigatória para a publicação de qualquer oferta de SaaS, conforme descrito em [registrar um aplicativo SaaS](./pc-saas-registration.md).

Para aplicativos do Azure com o plano de aplicativo gerenciado, você deve considerar o uso dessa estratégia nos seguintes casos:

* Você já tem um mecanismo para se comunicar com seus serviços de back-end e deseja estender esse mecanismo para emitir medidores personalizados de um serviço central.
* Você tem uma lógica de medidores personalizados complexa.  Execute essa lógica em um local central, em vez dos recursos do aplicativo gerenciado.

Depois de registrar seu aplicativo, você pode solicitar um token de segurança do Azure Active Directory programaticamente. O publicador deve usar esse token e fazer uma solicitação para resolvê-lo.

Para obter mais informações sobre esses tokens, veja [Tokens de acesso do Azure Active Directory](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base no Aplicativo Azure AD

#### <a name="http-method"></a>Método HTTP

**POST**

#### <a name="request-url"></a>*URL de Solicitação*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parâmetro URI*

|  **Nome do parâmetro** |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID do locatário do aplicativo Azure Active Directory registrado.   |
| | | |

#### <a name="request-header"></a>*Cabeçalho da solicitação*

|  **Nome do cabeçalho**    |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corpo da solicitação*

|  **Nome da propriedade**  |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Tipo de concessão. Use `client_credentials`. |
|  `Client_id`        |   True         | Identificador do cliente/aplicativo associado ao Aplicativo Azure AD.|
|  `client_secret`    |   True         | Segredo associado ao aplicativo do Azure AD.  |
|  `Resource`         |   True         | Recurso de destino para o qual o token é solicitado. Use `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Resposta*

|  **Nome**    |  **Tipo**  |  **Descrição**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Solicitação bem-sucedida.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Token de resposta de exemplo:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Usando o token de identidades gerenciadas pelo Azure

O tipo de oferta aplicável é aplicativos do Azure com tipo de plano de aplicativo gerenciado.

Usar essa abordagem permitirá que a identidade de recursos implantados se autentique para enviar eventos de uso de medidores personalizados.  Você pode inserir o código que emite o uso dentro dos limites de sua implantação.

>[!Note]
>O publicador deve garantir que os recursos que emitem o uso estejam bloqueados, para que não sejam violados.

Seu aplicativo gerenciado pode conter diferentes tipos de recursos, de Máquinas Virtuais até Azure Functions.  Para obter mais informações sobre como autenticar usando identidades gerenciadas para diferentes serviços, consulte [como usar identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)).

Por exemplo, siga as etapas abaixo para autenticar usando uma VM do Windows,

1. Verifique se a identidade gerenciada está configurada usando um dos métodos:
    * [Interface do usuário do portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Obtenha um token de acesso para a ID do aplicativo do serviço de medição do Marketplace (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) usando a identidade do sistema, o RDP para a VM, abra o console do PowerShell e execute o comando a seguir

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Obtenha a ID do aplicativo gerenciado da propriedade “ManagedBy” dos grupos de recursos atual

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. O serviço de medição do Marketplace requer que o relate o uso em um `resourceID` e `resourceUsageId` se for um aplicativo gerenciado.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Use a [API do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para emitir o uso.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma oferta do Aplicativo Azure](../create-new-azure-apps-offer.md)
* [Planejar uma oferta de SaaS](../plan-saas-offer.md)
