---
title: Criar ambientes de serviço de integração (ISEs) com a API REST de aplicativos lógicos
description: Criar um ISE (ambiente do serviço de integração) usando a API REST dos aplicativos lógicos para que você possa acessar redes virtuais do Azure (VNETs) de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550650"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Criar um ambiente do serviço de integração (ISE) usando a API REST de Aplicativos Lógicos

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md), você pode criar um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) usando a API REST dos aplicativos lógicos. Para saber mais sobre ISEs, confira [Acesso aos recursos da Rede Virtual do Azure dos Aplicativos Lógicos do Azure](connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como criar um ISE usando a API REST de aplicativos lógicos em geral. Opcionalmente, você também pode habilitar uma [identidade gerenciada atribuída pelo sistema ou](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) atribuída pelo usuário em seu ISE, mas somente usando a API REST de aplicativos lógicos no momento. Essa identidade permite que o ISE autentique o acesso a recursos protegidos, como máquinas virtuais e outros sistemas ou serviços, que estão no ou estão conectados a uma rede virtual do Azure. Dessa forma, você não precisa entrar com suas credenciais.

Para obter mais informações sobre outras maneiras de criar um ISE, consulte estes artigos:

* [Criar um ISE usando o portal do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Criar um ISE usando o modelo de início rápido de Azure Resource Manager de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Criar um ISE que ofereça suporte ao uso de chaves gerenciadas pelo cliente para criptografar dados em repouso](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Os mesmos [pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos de acesso](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) quando você cria um ISE no portal do Azure

* Todos os recursos adicionais que você deseja usar com o ISE para que você possa incluir suas informações na definição do ISE, por exemplo: 

  * Para habilitar o suporte a certificados autoassinados, você precisa incluir informações sobre esse certificado na definição do ISE.

  * Para habilitar a identidade gerenciada atribuída pelo usuário, você precisa criar essa identidade com antecedência e incluir as `objectId` `principalId` Propriedades, e `clientId` e seus valores na definição do ISE. Para obter mais informações, consulte [criar uma identidade gerenciada atribuída pelo usuário no portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Uma ferramenta que você pode usar para criar seu ISE chamando a API REST de aplicativos lógicos com uma solicitação HTTPS PUT. Por exemplo, você pode usar o [postmaster](https://www.getpostman.com/downloads/)ou pode criar um aplicativo lógico que executa essa tarefa.

## <a name="create-the-ise"></a>Criar o ISE

Para criar seu ISE chamando a API REST dos aplicativos lógicos, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão 2019-05-01 da API REST dos aplicativos lógicos requer que você faça sua própria solicitação HTTP PUT para conectores do ISE.

A implantação geralmente leva dentro de duas horas para ser concluída. Em alguns casos, a implantação pode levar até quatro horas. Para verificar o status da implantação, na [portal do Azure](https://portal.azure.com), na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel notificações.

> [!NOTE]
> Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Esse atraso significa que você precisará esperar antes de reutilizar essas sub-redes em outro ISE.
>
> Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
> Ao excluir redes virtuais, verifique se algum recurso ainda está conectado. 
> Confira [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Cabeçalho da solicitação

No cabeçalho da solicitação, inclua estas propriedades:

* `Content-type`: Defina esse valor de propriedade como `application/json` .

* `Authorization`: Defina esse valor de propriedade para o token de portador para o cliente que tem acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

<a name="request-body"></a>

## <a name="request-body"></a>Corpo da solicitação

No corpo da solicitação, forneça a definição de recurso a ser usada para criar seu ISE, incluindo informações para recursos adicionais que você deseja habilitar no ISE, por exemplo:

* Para criar um ISE que permita o uso de um certificado autoassinado e um certificado emitido pela autoridade de certificação corporativa que está instalada no `TrustedRoot` local, inclua o `certificates` objeto dentro da seção de definição do ISE `properties` , como este artigo descreve posteriormente.

* Para criar um ISE que usa uma identidade gerenciada atribuída pelo sistema ou atribuída pelo usuário, inclua o `identity` objeto com o tipo de identidade gerenciada e outras informações necessárias na definição do ISE, como este artigo descreve posteriormente.

* Para criar um ISE que usa chaves gerenciadas pelo cliente e Azure Key Vault para criptografar dados em repouso, inclua as [informações que habilitam o suporte a chaves gerenciadas pelo cliente](customer-managed-keys-integration-service-environment.md). Você pode configurar chaves gerenciadas pelo cliente *somente na criação*, não posteriormente.

### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação

Aqui está a sintaxe do corpo da solicitação, que descreve as propriedades a serem usadas ao criar o ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Exemplo de corpo de solicitação

Este exemplo de corpo de solicitação mostra os valores de exemplo:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Adicionar certificados raiz personalizados

Você geralmente usa um ISE para se conectar a serviços personalizados em sua rede virtual ou local. Esses serviços personalizados geralmente são protegidos por um certificado emitido pela autoridade de certificação raiz Personalizada, como uma autoridade de certificação corporativa ou um certificado autoassinado. Para obter mais informações sobre o uso de certificados autoassinados, consulte [acesso seguro e acesso a dados para chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Para que o ISE se conecte com êxito a esses serviços por meio do protocolo TLS, o ISE precisa de acesso a esses certificados raiz.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Considerações para adicionar certificados raiz personalizados

Antes de atualizar o ISE com um certificado raiz confiável personalizado, examine estas considerações:

* Certifique-se de carregar o certificado raiz *e* todos os certificados intermediários. O número máximo de certificados é 20.

* O carregamento de certificados raiz é uma operação de substituição em que o upload mais recente substitui os carregamentos anteriores. Por exemplo, se você enviar uma solicitação que carregue um certificado e, em seguida, enviar outra solicitação para carregar outro certificado, o ISE usará apenas o segundo certificado. Se você precisar usar ambos os certificados, adicione-os juntos na mesma solicitação.  

* O carregamento de certificados raiz é uma operação assíncrona que pode levar algum tempo. Para verificar o status ou o resultado, você pode enviar uma `GET` solicitação usando o mesmo URI. A mensagem de resposta tem um `provisioningState` campo que retorna o `InProgress` valor quando a operação de carregamento ainda está funcionando. Quando `provisioningState` o valor for `Succeeded` , a operação de carregamento será concluída.

#### <a name="request-syntax"></a>Sintaxe da solicitação

Para atualizar o ISE com um certificado raiz confiável personalizado, envie a seguinte solicitação de PATCH HTTPS para a [URL de Azure Resource Manager, que difere com base em seu ambiente do Azure](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane), por exemplo:

| Ambiente | URL de Azure Resource Manager |
|-------------|----------------------------|
| Global do Azure (multilocatário) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure na China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Sintaxe do corpo da solicitação para adicionar certificados raiz personalizados

Aqui está a sintaxe do corpo da solicitação, que descreve as propriedades a serem usadas quando você adiciona certificados raiz:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
