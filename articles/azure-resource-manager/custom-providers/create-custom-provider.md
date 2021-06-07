---
title: Criar um provedor de recursos
description: Descreve como criar um provedor de recursos e como implantar os tipos de recurso personalizados dele.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 721fe31f514cb948c93dc2a779a92ef3b2775ef9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779229"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Início Rápido: Criar um provedor personalizado e implantar recursos personalizados

Neste início rápido, você cria seu próprio provedor de recursos e implanta tipos de recurso personalizados para o provedor de recursos. Para saber mais sobre provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para concluir as etapas deste início rápido, você precisará chamar operações `REST`. Há [diferentes maneiras de enviar solicitações REST](/rest/api/azure/).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Prepare seu ambiente para a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Exemplos da CLI do Azure usam `az rest` para solicitações de `REST`. Para obter mais informações, confira [az rest](/cli/azure/reference-index#az_rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Os comandos do PowerShell são executados usando o PowerShell 7 ou posterior e os módulos do Azure PowerShell. Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).
- Se você ainda não tiver uma ferramenta para operações `REST`, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É a ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager.
- Depois que o **ARMClient** for instalado, você poderá exibir informações de uso de um prompt de comando do PowerShell digitando: `armclient.exe`. Ou acesse o [wiki do ARMClient](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Implantar provedor personalizado

Para configurar o provedor personalizado, implante um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) em sua assinatura do Azure.

Depois de implantar o modelo, sua assinatura terá os seguintes recursos:

- O Aplicativo de funções com as operações para recursos e ações.
- A Conta de Armazenamento para armazenar os usuários criados por meio do provedor personalizado.
- O Provedor Personalizado, que define os tipos de recurso personalizado e as ações. Ele usa o ponto de extremidade do aplicativo de funções para enviar solicitações.
- O recurso personalizado do provedor personalizado.

Para implantar o provedor personalizado, use a CLI do Azure, o PowerShell ou o portal do Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Este exemplo solicita que você insira um grupo de recursos, uma localização e um nome de aplicativo de funções do provedor. Os nomes são armazenados em variáveis que são usadas em outros comandos. Os comandos [az group create](/cli/azure/group#az_group_create) e [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) implantam os recursos.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este exemplo solicita que você insira um grupo de recursos, uma localização e um nome de aplicativo de funções do provedor. Os nomes são armazenados em variáveis que são usadas em outros comandos. Os comandos [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) e [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) implantam os recursos.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Você também pode implantar a solução do portal do Azure. Selecione o botão **Implantar no Azure** para abrir o modelo no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Exibir o provedor e o recurso personalizados

No portal, o provedor personalizado é um tipo de recurso oculto. Para confirmar se o provedor de recursos foi implantado, navegue até o grupo de recursos. Selecione a opção para **Mostrar tipos ocultos**.

![Mostrar tipos de recurso ocultos](./media/create-custom-provider/show-hidden.png)

Para ver o tipo de recurso personalizado que você implantou, use a operação `GET` em seu tipo de recurso.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Você recebe a resposta:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Você recebe a resposta:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Ação de chamada

O provedor personalizado também tem uma ação chamada `ping`. O código que processa a solicitação é implementado no aplicativo de funções. A ação `ping` responde com uma saudação.

Para enviar uma solicitação `ping`, use a operação `POST` no seu provedor personalizado.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Você recebe a resposta:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Você recebe a resposta:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Criar um tipo de recurso

Para criar o tipo de recurso personalizado, você poderá implantar o recurso em um modelo. Essa abordagem é mostrada no modelo implantado neste início rápido. Você também pode enviar uma solicitação `PUT` para o tipo de recurso.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Você recebe a resposta:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Você recebe a resposta:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Comandos do provedor de recursos personalizados

Use os comandos [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) para trabalhar com seu provedor de recursos personalizado.

### <a name="list-custom-resource-providers"></a>Listar provedores de recursos personalizados

Use o comando `list` para exibir todos os provedores de recursos personalizados em uma assinatura. O padrão lista os provedores de recursos personalizados da assinatura atual ou você pode especificar o parâmetro `--subscription`. Para listar para um grupo de recursos, use o parâmetro `--resource-group`.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Mostrar as propriedades

Use o comando `show` para exibir as propriedades do provedor de recursos personalizado. O formato de saída é semelhante à saída de `list`.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Criar um novo recurso

Use o comando `create` para criar ou atualizar um provedor de recursos personalizado. Este exemplo atualiza o `actions` e `resourceTypes`.

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Atualizar as marcas do provedor

O comando `update` atualiza apenas as marcas para um provedor de recursos personalizado. No portal do Azure, o serviço de aplicativo do provedor de recursos personalizado mostra a marca.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Excluir um provedor de recursos personalizado

O comando `delete` lhe faz uma solicitação e exclui somente o provedor de recursos personalizado. A conta de armazenamento, o serviço de aplicativo e o plano do serviço de aplicativo não são excluídos. Depois que o provedor for excluído, você será retornado para um prompt de comando.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Próximas etapas

Para uma introdução aos provedores personalizados, confira o seguinte artigo:

> [!div class="nextstepaction"]
> [Visão geral da Versão prévia de Provedores Personalizados do Azure](overview.md)
