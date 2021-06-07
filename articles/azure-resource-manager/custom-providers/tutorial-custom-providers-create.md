---
title: Criar e usar um provedor personalizado
description: Este tutorial mostra como criar e usar um provedor personalizado do Azure. Use provedores personalizados para alterar fluxos de trabalho no Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503745"
---
# <a name="create-and-use-a-custom-provider"></a>Criar e usar um provedor personalizado

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure. Este tutorial mostra o processo de criação de um provedor personalizado. Caso não esteja familiarizado com os Provedores Personalizados do Azure, confira [a visão geral dos Provedores de Recursos Personalizados do Azure](overview.md).

## <a name="create-a-custom-provider"></a>Criar um provedor personalizado

> [!NOTE]
> Este tutorial não mostra como criar um ponto de extremidade. Caso não tenha um ponto de extremidade RESTful, siga o [tutorial sobre como criar pontos de extremidade RESTful](./tutorial-custom-providers-function-authoring.md), que é a base para o tutorial atual.

Depois de criar um ponto de extremidade, crie um provedor personalizado para gerar um contrato entre o provedor e o ponto de extremidade. Com um provedor personalizado, você pode especificar uma lista de definições de ponto de extremidade:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriedade | Obrigatório | Descrição
---|---|---
**name** | Sim | O nome da definição do ponto de extremidade. O Azure expõe esse nome por meio de sua API em /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Não | O tipo de contrato do ponto de extremidade. Se o valor não for especificado, ele usará "Proxy" como padrão.
**endpoint** | Sim | O ponto de extremidade para o qual rotear as solicitações. Esse ponto de extremidade manipula a resposta e os efeitos colaterais da solicitação.

O valor do **ponto de extremidade** é a URL do gatilho do aplicativo de funções do Azure. Os espaços reservados `<yourapp>`, `<funcname>` e `<functionkey>` precisam ser substituídos por valores para o aplicativo de funções criado.

## <a name="define-custom-actions-and-resources"></a>Definir ações e recursos personalizados

O provedor personalizado contém uma lista de definições de ponto de extremidade nas propriedades **actions** e **resourceTypes**. A propriedade **actions** é mapeada para as ações personalizadas expostas pelo provedor personalizado e a propriedade **resourceTypes** são os recursos personalizados. Neste tutorial, o provedor personalizado tem uma propriedade **actions** chamada `myCustomAction` e uma propriedade **resourceTypes** chamada `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Implantar o provedor personalizado

> [!NOTE]
> É necessário substituir os valores do **ponto de extremidade** pela URL do gatilho no aplicativo de funções criado no tutorial anterior.

Você pode implantar o provedor personalizado anterior usando um modelo do Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Usar ações e recursos personalizados

Depois de criar um provedor personalizado, use as novas APIs do Azure. As guias a seguir explicam como chamar e usar um provedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> É necessário substituir os espaços reservados `{subscriptionId}` e `{resourceGroupName}` pela assinatura e pelo grupo de recursos nos quais você implantou o provedor personalizado.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
*action* | Sim | O nome da ação definida no provedor personalizado
*ids* | Sim | A ID do recurso do provedor personalizado
*request-body* | Não | O corpo da solicitação que será enviado ao ponto de extremidade

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> É necessário substituir os espaços reservados `{subscriptionId}` e `{resourceGroupName}` pela assinatura e pelo grupo de recursos nos quais você implantou o provedor personalizado.

#### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
*is-full-object* | Sim | Indica se o objeto de propriedades inclui outras opções, como localização, marcas, SKU ou plano.
*id* | Sim | A ID de recurso referente ao recurso personalizado. Essa ID é uma extensão da ID do recurso do provedor personalizado.
*properties* | Sim | O corpo da solicitação que será enviado ao ponto de extremidade.

#### <a name="delete-a-custom-resource"></a>Excluir um recurso personalizado

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | Descrição
---|---|---
*id* | Sim | A ID de recurso referente ao recurso personalizado. Essa ID é uma extensão da ID do recurso do provedor personalizado.

#### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | Descrição
---|---|---
*id* | Sim | A ID de recurso referente ao recurso personalizado. Essa ID é uma extensão da ID do recurso do provedor personalizado.

# <a name="template"></a>[Modelo](#tab/template)

Um modelo do Resource Manager de exemplo:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parâmetro | Obrigatório | Descrição
---|---|---
*resourceTypeName* | Sim | O valor `name` da propriedade **resourceTypes** definido no provedor personalizado.
*resourceProviderName* | Sim | O nome de instância do provedor personalizado.
*customResourceName* | Sim | O nome do recurso personalizado.

---

> [!NOTE]
> Depois de concluir a implantação e o uso do provedor personalizado, lembre-se de limpar todos os recursos criados, incluindo o aplicativo de funções do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Para obter mais informações, consulte:

- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
