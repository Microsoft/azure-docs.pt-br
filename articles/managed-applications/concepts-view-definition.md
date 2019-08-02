---
title: Visão geral da definição de exibição em aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve o conceito de criação de definição de exibição para aplicativos gerenciados do Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: ff96bddef1b34f5a8bf743ccaaccba2da01534dc
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335108"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Exibir artefato de definição em aplicativos gerenciados do Azure

A definição de exibição é um artefato opcional em aplicativos gerenciados do Azure. Ele permite personalizar a página de visão geral e adicionar mais modos de exibição, como métricas e recursos personalizados.

Este artigo fornece uma visão geral do artefato de definição de exibição e seus recursos.

## <a name="view-definition-artifact"></a>Exibir artefato de definição

O artefato da definição de exibição deve ser nomeado **viewDefinition. JSON** e colocado no mesmo nível que **createUiDefinition. JSON** e **MainTemplate. JSON** no pacote. zip que cria uma definição de aplicativo gerenciado. Para saber como criar o pacote. zip e publicar uma definição de aplicativo gerenciado, consulte [publicar uma definição de aplicativo gerenciado do Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Exibir esquema de definição

O arquivo **viewDefinition. JSON** tem apenas uma propriedade de `views` nível superior, que é uma matriz de exibições. Cada exibição é mostrada na interface do usuário do aplicativo gerenciado como um item de menu separado no sumário. Cada exibição tem uma `kind` propriedade que define o tipo da exibição. Ele deve ser definido como um dos seguintes valores: [Visão geral](#overview), [métricas](#metrics), [CustomResources](#custom-resources). Para obter mais informações, consulte [esquema JSON atual para viewDefinition. JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exemplo de JSON para definição de exibição:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Visão geral

`"kind": "Overview"`

Quando você fornece essa exibição em **viewDefinition. JSON**, ela substitui a página de visão geral padrão em seu aplicativo gerenciado.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|cabeçalho|Não|O cabeçalho da página de visão geral.|
|description|Não|A descrição do seu aplicativo gerenciado.|
|comandos|Não|A matriz de botões adicionais da barra de ferramentas da página Visão geral, consulte [comandos](#commands).|

![Visão geral](./media/view-definition/overview.png)

## <a name="metrics"></a>metrics

`"kind": "Metrics"`

A exibição de métricas permite coletar e agregar dados de seus recursos de aplicativo gerenciado em métricas de [Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Não|O título exibido da exibição.|
|versão|Não|A versão da plataforma usada para renderizar a exibição.|
|spersão|Sim|A matriz de gráficos da página de métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido do gráfico.|
|tipo de gráfico|Não|A visualização a ser usada para este gráfico. Por padrão, ele usa um gráfico de linhas. Tipos de gráfico com `Bar, Line, Area, Scatter`suporte:.|
|metrics|Sim|A matriz de métricas a ser plotada neste gráfico. Para saber mais sobre as métricas com suporte no portal do Azure, consulte métricas com [suporte com Azure monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Métrica

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|name|Sim|O nome da métrica.|
|aggregationType|Sim|O tipo de agregação a ser usado para essa métrica. Tipos de agregação com suporte:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|Não|Informações adicionais a serem usadas ao determinar o provedor de métricas correto.|
|resourceTagFilter|Não|A matriz de marcas de recurso (será separada `or` com o Word) para a qual as métricas seriam exibidas. Aplica-se na parte superior do filtro de tipo de recurso.|
|resourceType|Sim|O tipo de recurso para o qual as métricas seriam exibidas.|

![metrics](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Você pode definir várias exibições deste tipo. Cada exibição representa um tipo de recurso personalizado **exclusivo** do provedor personalizado que você definiu em **MainTemplate. JSON**. Para obter uma introdução aos provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](custom-providers-overview.md).

Nesta exibição, você pode executar operações GET, PUT, DELETE e POST para o tipo de recurso personalizado. Operações POST podem ser ações personalizadas globais ou ações personalizadas em um contexto de seu tipo de recurso personalizado.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da exibição. O título deve ser **exclusivo** para cada exibição de CustomResources em seu **viewDefinition. JSON**.|
|versão|Não|A versão da plataforma usada para renderizar a exibição.|
|resourceType|Sim|O tipo de recurso personalizado. Deve ser um tipo de recurso personalizado **exclusivo** do seu provedor personalizado.|
|createUIDefinition|Não|Crie um esquema de definição de interface do usuário para o comando criar recurso personalizado. Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md)|
|comandos|Não|A matriz de botões adicionais da barra de ferramentas da exibição CustomResources, consulte [comandos](#commands).|
|colunas|Não|A matriz de colunas do recurso personalizado. Se não estiver definido `name` , a coluna será mostrada por padrão. A coluna deve ter `"key"` e `"displayName"`. Para chave, forneça a chave da propriedade a ser exibida em uma exibição. Se estiver aninhado, use ponto como delimitador `"key": "name"` , `"key": "properties.property1"`por exemplo, ou. Para nome de exibição, forneça o nome de exibição da propriedade a ser exibida em uma exibição. Você também pode fornecer uma `"optional"` propriedade. Quando definido como true, a coluna é ocultada em uma exibição por padrão.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandos

Os comandos são uma matriz de botões adicionais da barra de ferramentas que são exibidos na página. Cada comando representa uma ação POST de seu provedor personalizado do Azure definido em **MainTemplate. JSON**. Para obter uma introdução aos provedores personalizados, consulte [visão geral de provedores personalizados do Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Sim|O nome exibido do botão de comando.|
|path|Sim|O nome da ação do provedor personalizado. A ação deve ser definida em **MainTemplate. JSON**.|
|ícone|Não|O ícone do botão de comando. A lista de ícones com suporte é definida no [esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Não|Crie o esquema de definição da interface do usuário para o comando. Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre os aplicativos gerenciados do Azure, tente solicitar [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido solicitada e respondida, portanto, verifique primeiro antes de lançar. Adicione a marca `azure-managedapps` para obter uma resposta rápida!

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](overview.md).
- Para obter uma introdução aos provedores personalizados, consulte [visão geral de provedores personalizados do Azure](custom-providers-overview.md).
- Para criar um aplicativo gerenciado do Azure com provedores personalizados do Azure [, consulte o tutorial: Criar aplicativo gerenciado com ações de provedor personalizado e tipos de recursos](tutorial-create-managed-app-with-custom-provider.md)
