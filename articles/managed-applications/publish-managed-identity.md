---
title: Aplicativo gerenciado do Azure com identidade gerenciada
description: Saiba como configurar um aplicativo gerenciado com uma identidade gerenciada. Identidade gerenciado pode ser usada para implantar aplicativos gerenciados vinculados aos recursos existentes, conceda os aplicativos gerenciados para gerenciar recursos do Azure fora do grupo de recursos gerenciado e fornecer uma identidade operacional dos aplicativos gerenciados para o Log de atividades e outros serviços dentro do Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 9fb5f7a4a62c2d323059f7c0b879482e93feef2f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434855"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicativo gerenciado do Azure com identidade gerenciada

> [!NOTE]
> Suporte de identidade gerenciada para aplicativos gerenciados está atualmente em visualização. Use a versão da api 2018-09-01-preview para utilizar a identidade gerenciada.

Saiba como configurar um aplicativo gerenciado para conter uma identidade gerenciada. Identidade gerenciado pode ser usada para permitir que o cliente conceder acesso de aplicativo gerenciado a recursos adicionais. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou alternância de nenhum segredo. Para obter mais informações sobre identidades gerenciadas no Azure Active Directory (AAD), consulte [identidades para recursos do Azure gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se o seu aplicativo for excluído. Um aplicativo só pode ter uma identidade atribuída pelo sistema.
- Um **identidade atribuída pelo usuário** é um recurso autônomo do Azure que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário.

## <a name="how-to-use-managed-identity"></a>Como usar a identidade gerenciada

Identidade gerenciado habilita muitos cenários para aplicativos gerenciados. Alguns cenários comuns que podem ser resolvidos são:

- Implantando um aplicativo gerenciado vinculado a recursos existentes do Azure. Um exemplo é implantar uma máquina virtual do Azure (VM) dentro do aplicativo gerenciado que está anexado a um [adaptador de rede existente](../virtual-network/virtual-network-network-interface-vm.md).
- Concedendo o acesso de aplicativo gerenciado e o publicador aos recursos do Azure fora de **grupo de recursos gerenciado**.
- Fornecendo uma identidade operacional dos aplicativos gerenciados para o Log de atividades e outros serviços no Azure.

## <a name="adding-managed-identity"></a>Adicionar identidade gerenciada

Criando um aplicativo gerenciado com uma identidade gerenciada requer uma propriedade adicional a ser definido no recurso do Azure. O exemplo a seguir mostra um exemplo **identidade** propriedade:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Há duas maneiras comuns para criar um aplicativo gerenciado com **identidade**: [Createuidefinition](./create-uidefinition-overview.md) e [Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md). Para que o único simple criar cenários, CreateUIDefinition deve ser usado para habilitar a identidade gerenciada, pois ele fornece uma experiência mais rica. No entanto, ao lidar com complexas ou avançada automatizada de sistemas que exigem ou várias implantações de aplicativo gerenciado, modelos podem ser usadas.

### <a name="using-createuidefinition"></a>Usando CreateUIDefinition

Um aplicativo gerenciado pode ser configurado com identidade gerenciada por meio de [Createuidefinition](./create-uidefinition-overview.md). No [seção saídas](./create-uidefinition-overview.md#outputs), a chave `managedIdentity` pode ser usado para substituir a propriedade de identidade do modelo de aplicativo gerenciado. Permitirá que o sino de amostra **atribuído pelo sistema** identidade do aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados por meio de elementos de CreateUIDefinition para pedir o consumidor entradas. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidade atribuída pelo usuário**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando usar CreateUIDefinition para identidade gerenciada

Abaixo estão algumas recomendações sobre quando usar CreateUIDefinition para habilitar a identidade gerenciado em aplicativos gerenciados.

- A criação de aplicativo gerenciado vai por meio do portal do Azure ou no marketplace.
- A identidade gerenciada requer entrada do consumidor complexas.
- A identidade gerenciada é necessário na criação do aplicativo gerenciado.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Um CreateUIDefinition básico que habilita a identidade SystemAssigned para o aplicativo gerenciado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Um CreateUIDefinition básico que usa um **identidade atribuída pelo usuário** recursos como entrada e permite que a identidade UserAssigned para o aplicativo gerenciado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Createuidefinition. JSON acima gera uma experiência de usuário de criação que tem uma caixa de texto para um consumidor inserir o **identidade atribuída pelo usuário** ID de recurso do Azure. A experiência gerada pareceria com:

![Exemplo de identidade atribuída pelo usuário CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

> [!NOTE]
> Modelos de aplicativo gerenciado do Marketplace são gerados automaticamente para clientes que estão passando por meio do portal do Azure crie a experiência.
> Para esses cenários, o `managedIdentity` chave de saída no CreateUIDefinition deve ser usada para habilitado de identidade.

A identidade gerenciada também pode ser habilitada por meio de modelos do Azure Resource Manager. Permitirá que o sino de amostra **atribuído pelo sistema** identidade do aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados por meio de parâmetros de modelo do Azure Resource Manager para fornecer entradas. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidade atribuída pelo usuário**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usar modelos do Azure Resource Manager para identidade gerenciada

Abaixo estão algumas recomendações sobre quando usar modelos do Azure Resource Manager para habilitar a identidade gerenciado em aplicativos gerenciados.

- Aplicativos gerenciados podem ser implantados por meio de programação baseado em um modelo.
- Atribuições de função personalizadas para a identidade gerenciada são necessários para provisionar o aplicativo gerenciado.
- O aplicativo gerenciado não é necessário para o fluxo de criação de portal e o marketplace do Azure.

#### <a name="systemassigned-template"></a>Modelo de SystemAssigned

Um modelo básico do Azure Resource Manager que implanta um aplicativo gerenciado com **atribuído pelo sistema** identidade.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Modelo de UserAssigned

Um modelo básico do Azure Resource Manager que implanta um aplicativo gerenciado com um **identidade atribuída pelo usuário**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Conceder acesso aos recursos do Azure

Depois que um aplicativo gerenciado é concedido a uma identidade, ela pode ser concedida acesso aos recursos do azure existentes. Esse processo pode ser feito por meio da interface IAM (controle) de acesso no portal do Azure. O nome do aplicativo gerenciado ou **identidade atribuída pelo usuário** podem ser pesquisados para adicionar uma atribuição de função.

![Adicionar atribuição de função de aplicativo gerenciado](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Vinculação de recursos do Azure existentes

> [!NOTE]
> Um **identidade atribuída pelo usuário** deve ser [configurados](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes de implantar o aplicativo gerenciado. Além disso, o recurso vinculado de implantação de aplicativos gerenciado tem suporte apenas para o **marketplace** tipo.

Identidade de gerenciado também pode ser usada para implantar um aplicativo gerenciado que requer acesso aos recursos existentes durante a sua implantação. Quando o aplicativo gerenciado é provisionado pelo cliente, **identidades atribuídas** podem ser adicionados para fornecer autorizações adicionais para o **mainTemplate** implantação.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Criação de CreateUIDefinition com um recurso vinculado

Ao vincular a implantação do aplicativo gerenciado para recursos existentes, os dois o recurso do Azure existente e um **identidade atribuída pelo usuário** com a função aplicável atribuição no que recurso deve ser fornecida.

 Um exemplo de CreateUIDefinition que requer duas entradas: uma ID de recurso de interface de rede e uma id de recurso de identidade atribuída pelo usuário.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Este Createuidefinition gera uma experiência de usuário de criação que tem dois campos. O primeiro campo permite que o usuário insira a ID do recurso do Azure para o recurso que está sendo vinculado à implantação de aplicativo gerenciado. A segunda é para um consumidor inserir o **identidade atribuída pelo usuário** ID de recurso do Azure, que tem acesso ao recurso do Azure vinculado. A experiência gerada pareceria com:

![Exemplo de CreateUIDefinition com duas entradas: uma interface de rede, ID de recurso e uma ID de recurso de identidade atribuída pelo usuário](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Criando o mainTemplate com um recurso vinculado

Além de atualizar o CreateUIDefinition, o modelo principal também precisará ser atualizada para aceitar a ID de recurso vinculado passado. O modelo principal pode ser atualizado para aceitar a nova saída adicionando um novo parâmetro. Uma vez que o `managedIdentity` saída substitui o valor no modelo de aplicativo gerenciado gerado, ele não é passado para o modelo principal e não deve ser incluído na seção de parâmetros.

Um modelo de exemplo principal que define o perfil de rede a um adaptador de rede existente fornecido pelo CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumir o aplicativo gerenciado com um recurso vinculado

Depois de criar o pacote de aplicativo gerenciado, o aplicativo gerenciado pode ser consumido por meio do portal do Azure. Antes que ele possa ser consumido, há várias etapas de pré-requisito.

- Uma instância do recurso do Azure vinculado obrigatório deve ser criada.
- O **identidade atribuída pelo usuário** deve ser [criado e recebe as atribuições de função](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para o recurso vinculado.
- Existente vinculado a ID de recurso e o **identidade atribuída pelo usuário** ID são fornecidos para o CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Acessando o token de identidade gerenciada

O token do aplicativo gerenciado agora pode ser acessado por meio de `listTokens` api do locatário do publicador. Um exemplo de solicitação pode parecer com:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parâmetros do corpo da solicitação:

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
authorizationAudience | *no* | O URI de ID do aplicativo de recurso de destino. Também é o `aud` declaração (público) do token emitido. O valor padrão é "https://management.azure.com/"
userAssignedIdentities | *no* | A lista de identidades gerenciadas atribuídas para recuperar um token para. Se não for especificado, `listTokens` retornará o token para a identidade gerenciada atribuído pelo sistema.


Uma resposta de exemplo pode parecer com:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

A resposta conterá uma matriz de tokens sob o `value` propriedade:

Parâmetro | DESCRIÇÃO
---|---
access_token | O token de acesso solicitado.
expires_in | O número de segundos que o token de acesso serão válido.
expires_on | O período de expiração do token de acesso. Isso é representado como o número de segundos de época.
not_before | O período de tempo quando o token de acesso entra em vigor. Isso é representado como o número de segundos de época.
authorizationAudience | O `aud` (público) o token de acesso foi a solicitação. Isso é o mesmo que o que foi fornecido no `listTokens` solicitação.
ResourceId | A ID de recurso do Azure para o token emitido. Isso é a ID do aplicativo gerenciado ou a ID da identidade atribuída pelo usuário.
token_type | O tipo do token.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como configurar um aplicativo gerenciado com um provedor personalizado](./custom-providers-overview.md)
