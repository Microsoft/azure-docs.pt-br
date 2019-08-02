---
title: Criar e utilizar um Provedor Personalizado do Azure
description: Este tutorial apresentará como criar e utilizar um provedor personalizado.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799125"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Como criar um ponto de extremidade RESTful para provedores personalizados

Provedores personalizados permitem que você personalize os fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Este tutorial apresentará o processo de criar um provedor personalizado. Se você não estiver familiarizado com os Provedores Personalizados do Azure, confira a [visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

Este tutorial é dividido nas seguintes etapas:

- O que é um provedor personalizado
- Como definir recursos e ações personalizados
- Como implantar o provedor personalizado

Este tutorial criará os seguintes tutoriais:

- [Como criar um ponto de extremidade RESTful para provedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Como criar um provedor personalizado

> [!NOTE]
> Este tutorial não apresentará a criação de um ponto de extremidade. Siga o [tutorial sobre como criar pontos de extremidade RESTful](./tutorial-custom-providers-function-authoring.md) se você não tiver um ponto de extremidade RESTful.

Depois que o `endpoint` foi criado, você pode criar um provedor personalizado para gerar um contrato entre ele e o `endpoint`. Um provedor personalizado permite que você especifique uma lista de definições de ponto de extremidade.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriedade | Obrigatório | DESCRIÇÃO
---|---|---
Nome | *sim* | O nome da definição do ponto de extremidade. O Azure exporá esse nome por meio de sua API em '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *não* | Determina o tipo de contrato com o `endpoint`. Se não for especificado, o padrão será "Proxy".
endpoint | *sim* | O ponto de extremidade para o qual rotear as solicitações. Isso processará a resposta, bem como os efeitos colaterais, da solicitação.

Nesse caso, o `endpoint` é a URL do gatilho da Função do Azure. O `<yourapp>`, o `<funcname>` e o `<functionkey>` devem ser substituídos por valores para sua função criada.

## <a name="defining-custom-actions-and-resources"></a>Como definir recursos e ações personalizados

O provedor personalizado contém uma lista de definições de ponto de extremidade modeladas em `actions` e `resourceTypes`. As `actions` são mapeadas para as ações personalizadas expostas pelo provedor personalizado, enquanto os `resourceTypes` são os recursos personalizados. Para este tutorial, definiremos um provedor personalizado com uma `action` chamada `myCustomAction` e um `resourceType` chamado `myCustomResources`.

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

Substitua `endpoint` pela URL do gatilho da função criada no tutorial anterior.

## <a name="deploying-the-custom-provider"></a>Como implantar o provedor personalizado

> [!NOTE]
> O `endpoint` deve ser substituído pela URL da função.

O provedor personalizado acima pode ser implantado usando um Modelo do Azure Resource Manager.

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

## <a name="using-custom-actions-and-resources"></a>Como usar ações e recursos personalizados

Depois de termos criado um provedor personalizado, podemos utilizar as novas APIs do Azure. A próxima seção explicará como chamar e utilizar um provedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> O `{subscriptionId}` e o `{resourceGroupName}` devem ser substituídos pela assinatura e pelo grupo de recursos em que o provedor personalizado foi implantado.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
ação | *sim* | O nome da ação definido no provedor personalizado criado.
ids | *sim* | A ID de recurso do provedor personalizado criado.
corpo da solicitação | *não* | O corpo da solicitação que será enviado ao `endpoint`.

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhuma.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> O `{subscriptionId}` e o `{resourceGroupName}` devem ser substituídos pela assinatura e pelo grupo de recursos em que o provedor personalizado foi implantado.

Criar um recurso personalizado:

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

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
is-full-object | *sim* | Indica que o objeto de propriedades inclui outras opções, como localização, tags, SKU e/ou plano.
ID | *sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora o provedor personalizado criado.
properties | *sim* | O corpo da solicitação que será enviado ao `endpoint`.

Excluir um Recurso Personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
ID | *sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora o provedor personalizado criado.

Recuperar um Recurso Personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
ID | *sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora o provedor personalizado criado.

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Modelo de exemplo do Azure Resource Manager:

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

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
resourceTypeName | *sim* | O `name` do *resourceType* definido no provedor personalizado.
resourceProviderName | *sim* | O nome de instância do provedor personalizado.
customResourceName | *sim* | O nome do recurso personalizado.

---

> [!NOTE]
> Depois de concluir a implantação e uso do provedor personalizado, limpe os recursos criados, incluindo a Função do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

- [Como: adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
