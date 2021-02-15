---
title: Aplicativo gerenciado com identidade gerenciada
description: Configure o aplicativo gerenciado com identidade gerenciada para vincular a recursos existentes, gerenciar recursos do Azure e fornecer a identidade operacional para o log de atividades.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82508115"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicativo gerenciado do Azure com identidade gerenciada

> [!NOTE]
> O suporte de identidade gerenciada para aplicativos gerenciados está atualmente em versão prévia. Use a versão da API 2018-09-01-Preview para utilizar a identidade gerenciada.

Saiba como configurar um aplicativo gerenciado para conter uma identidade gerenciada. A identidade gerenciada pode ser usada para permitir que o cliente conceda acesso de aplicativo gerenciado a recursos adicionais existentes. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou giro de nenhum segredo. Para obter mais informações sobre identidades gerenciadas no Azure Active Directory (AAD), consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se o seu aplicativo for excluído. Um aplicativo só pode ter uma identidade atribuída pelo sistema.
- Uma **identidade atribuída pelo usuário** é um recurso independente do Azure que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário.

## <a name="how-to-use-managed-identity"></a>Como usar a identidade gerenciada

A identidade gerenciada permite muitos cenários para aplicativos gerenciados. Alguns cenários comuns que podem ser resolvidos são:

- Implantando um aplicativo gerenciado vinculado aos recursos existentes do Azure. Um exemplo é implantar uma VM (máquina virtual) do Azure dentro do aplicativo gerenciado que está anexado a uma [interface de rede existente](../../virtual-network/virtual-network-network-interface-vm.md).
- Concedendo o acesso do aplicativo gerenciado e do Publicador aos recursos do Azure fora do **grupo de recursos gerenciado**.
- Fornecer uma identidade operacional de aplicativos gerenciados para o log de atividades e outros serviços no Azure.

## <a name="adding-managed-identity"></a>Adicionando identidade gerenciada

A criação de um aplicativo gerenciado com uma identidade gerenciada requer que uma propriedade adicional seja definida no recurso do Azure. O exemplo a seguir mostra uma propriedade de **identidade** de exemplo:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Há duas maneiras comuns de criar um aplicativo gerenciado com **identidade**: [CreateUIDefinition.js](./create-uidefinition-overview.md) e [Azure Resource Manager modelos](../templates/template-syntax.md). Para cenários simples de criação única, o CreateUIDefinition deve ser usado para habilitar a identidade gerenciada, pois fornece uma experiência mais rica. No entanto, ao lidar com sistemas avançados ou complexos que exigem implantações automatizadas de aplicativos gerenciados ou múltiplas, os modelos podem ser usados.

### <a name="using-createuidefinition"></a>Usando CreateUIDefinition

Um aplicativo gerenciado pode ser configurado com identidade gerenciada por meio do [CreateUIDefinition.jsem](./create-uidefinition-overview.md). Na [seção de saídas](./create-uidefinition-overview.md#outputs), a chave `managedIdentity` pode ser usada para substituir a propriedade Identity do modelo de aplicativo gerenciado. O abaixo de exemplo permitirá a identidade **atribuída pelo sistema** no aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados usando elementos CreateUIDefinition para solicitar entradas ao consumidor. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidades atribuídas pelo usuário**.

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando usar o CreateUIDefinition para identidade gerenciada

Abaixo estão algumas recomendações sobre quando usar o CreateUIDefinition para habilitar a identidade gerenciada em aplicativos gerenciados.

- A criação do aplicativo gerenciado passa pelo portal do Azure ou pelo Marketplace.
- A identidade gerenciada requer uma entrada de consumidor complexa.
- A identidade gerenciada é necessária na criação do aplicativo gerenciado.

#### <a name="managed-identity-createuidefinition-control"></a>Controle CreateUIDefinition de identidade gerenciada

O CreateUIDefinition dá suporte a um [controle de identidade gerenciado](./microsoft-managedidentity-identityselector.md)interno.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![CreateUIDefinition de identidade gerenciada](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Usar modelos do Azure Resource Manager

> [!NOTE]
> Os modelos de aplicativo gerenciado do Marketplace são gerados automaticamente para clientes que passam pela experiência de criação de portal do Azure.
> Para esses cenários, a `managedIdentity` chave de saída no CreateUIDefinition deve ser usada para habilitar a identidade.

A identidade gerenciada também pode ser habilitada por meio de modelos de Azure Resource Manager. O abaixo de exemplo permitirá a identidade **atribuída pelo sistema** no aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados usando Azure Resource Manager parâmetros de modelo para fornecer entradas. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidades atribuídas pelo usuário**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usar modelos de Azure Resource Manager para identidade gerenciada

Abaixo estão algumas recomendações sobre quando usar modelos de Azure Resource Manager para habilitar a identidade gerenciada em aplicativos gerenciados.

- Os aplicativos gerenciados podem ser implantados programaticamente com base em um modelo.
- As atribuições de função personalizadas para a identidade gerenciada são necessárias para provisionar o aplicativo gerenciado.
- O aplicativo gerenciado não precisa do portal do Azure e do fluxo de criação do Marketplace.

#### <a name="systemassigned-template"></a>Modelo SystemAssigned

Um modelo de Azure Resource Manager básico que implanta um aplicativo gerenciado com identidade **atribuída pelo sistema** .

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

### <a name="userassigned-template"></a>Modelo userassigned

Um modelo de Azure Resource Manager básico que implanta um aplicativo gerenciado com uma **identidade atribuída pelo usuário**.

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

## <a name="granting-access-to-azure-resources"></a>Concedendo acesso aos recursos do Azure

Depois que um aplicativo gerenciado recebe uma identidade, ele pode receber acesso a recursos existentes do Azure. Esse processo pode ser feito por meio da interface de controle de acesso (IAM) no portal do Azure. O nome do aplicativo gerenciado ou da **identidade atribuída pelo usuário** pode ser pesquisado para adicionar uma atribuição de função.

![Adicionar atribuição de função para aplicativo gerenciado](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Vinculando recursos existentes do Azure

> [!NOTE]
> Uma **identidade atribuída pelo usuário** deve ser [configurada](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes da implantação do aplicativo gerenciado. Além disso, a implantação de recursos vinculados de aplicativos gerenciados tem suporte apenas para o tipo **Marketplace** .

A identidade gerenciada também pode ser usada para implantar um aplicativo gerenciado que requer acesso a recursos existentes durante sua implantação. Quando o aplicativo gerenciado é provisionado pelo cliente, as **identidades atribuídas pelo usuário** podem ser adicionadas para fornecer autorizações adicionais à implantação do **MainTemplate** .

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Criando o CreateUIDefinition com um recurso vinculado

Ao vincular a implantação do aplicativo gerenciado aos recursos existentes, o recurso do Azure existente e uma **identidade atribuída pelo usuário** com a atribuição de função aplicável nesse recurso devem ser fornecidos.

 Um CreateUIDefinition de exemplo que requer duas entradas: uma ID de recurso de interface de rede e uma ID de recurso de identidade atribuída pelo usuário.

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

Esse CreateUIDefinition.jsem gera uma experiência de criação de usuário que tem dois campos. O primeiro campo permite que o usuário insira na ID de recurso do Azure para o recurso que está sendo vinculado à implantação de aplicativo gerenciado. O segundo é que um consumidor Insira a ID de recurso do Azure **atribuída pelo usuário** , que tem acesso ao recurso vinculado do Azure. A experiência gerada teria a seguinte aparência:

![Exemplo de CreateUIDefinition com duas entradas: uma ID de recurso de interface de rede e uma ID de recurso de identidade atribuída pelo usuário](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Criando o MainTemplate com um recurso vinculado

Além de atualizar o CreateUIDefinition, o modelo principal também precisa ser atualizado para aceitar o passado na ID de recurso vinculada. O modelo principal pode ser atualizado para aceitar a nova saída adicionando um novo parâmetro. Como a `managedIdentity` saída substitui o valor no modelo de aplicativo gerenciado gerado, ele não é passado para o modelo principal e não deve ser incluído na seção de parâmetros.

Um modelo principal de exemplo que define o perfil de rede para uma interface de rede existente fornecida pelo CreateUIDefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumindo o aplicativo gerenciado com um recurso vinculado

Depois que o pacote de aplicativos gerenciados é criado, o aplicativo gerenciado pode ser consumido por meio do portal do Azure. Antes que possa ser consumido, há várias etapas de pré-requisito.

- Uma instância do recurso vinculado do Azure necessário deve ser criada.
- A **identidade atribuída pelo usuário** deve ser [criada e receber atribuições de função](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para o recurso vinculado.
- A ID de recurso vinculada existente e a ID de **identidade atribuída pelo usuário** são fornecidas para o CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Acessando o token de identidade gerenciado

O token do aplicativo gerenciado agora pode ser acessado por meio da `listTokens` API do locatário do Publicador. Uma solicitação de exemplo pode ser semelhante a:

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

Parâmetro | Obrigatório | Descrição
---|---|---
authorizationAudience | *Não* | O URI da ID do aplicativo do recurso de destino. Também é a `aud` declaração (público) do token emitido. O valor padrão é " https://management.azure.com/ "
userAssignedIdentities | *Não* | A lista de identidades gerenciadas atribuídas pelo usuário para a qual recuperar um token. Se não for especificado, `listTokens` retornará o token para a identidade gerenciada atribuída pelo sistema.


Uma resposta de exemplo pode ser semelhante A:

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

A resposta conterá uma matriz de tokens na `value` Propriedade:

Parâmetro | Descrição
---|---
access_token | O token de acesso solicitado.
expires_in | O número de segundos que o token de acesso será válido.
expires_on | O período de expiração do token de acesso. Isso é representado como o número de segundos de época.
not_before | O TimeSpan quando o token de acesso entra em vigor. Isso é representado como o número de segundos de época.
authorizationAudience | O `aud` (público) para o qual o token de acesso foi solicitado. Isso é o mesmo que foi fornecido na `listTokens` solicitação.
resourceId | A ID de recurso do Azure para o token emitido. Essa é a ID do aplicativo gerenciado ou a ID de identidade atribuída pelo usuário.
token_type | O tipo do token.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como configurar um aplicativo gerenciado com um provedor personalizado](../custom-providers/overview.md)
