---
title: Visão geral da definição da exibição
description: Descreve o conceito de criação de definição de exibição para aplicativos gerenciados do Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 55263d3c742d18cf03303f96f08fb9aa370c7af8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100592059"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Exibir artefato de definição em aplicativos gerenciados do Azure

A definição de exibição é um artefato opcional em aplicativos gerenciados do Azure. Ele permite personalizar a página de visão geral e adicionar mais modos de exibição, como métricas e recursos personalizados.

Este artigo fornece uma visão geral do artefato de definição de exibição e seus recursos.

## <a name="view-definition-artifact"></a>Exibir artefato de definição

O artefato da definição de exibição deve ser nomeado **viewDefinition.js** e colocado no mesmo nível que **createUiDefinition.js** e **mainTemplate.js** no pacote. zip que cria uma definição de aplicativo gerenciado. Para saber como criar o pacote. zip e publicar uma definição de aplicativo gerenciado, consulte [publicar uma definição de aplicativo gerenciado do Azure](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Exibir esquema de definição

O **viewDefinition.jsno** arquivo tem apenas uma `views` propriedade de nível superior, que é uma matriz de exibições. Cada exibição é mostrada na interface do usuário do aplicativo gerenciado como um item de menu separado no sumário. Cada exibição tem uma `kind` propriedade que define o tipo da exibição. Ele deve ser definido como um dos seguintes valores: [visão geral](#overview), [métricas](#metrics), [CustomResources](#custom-resources), [associações](#associations). Para obter mais informações, consulte [esquema JSON atual para viewDefinition.jsem](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

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
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Visão geral

`"kind": "Overview"`

Quando você fornece esse modo de exibição no **viewDefinition.jsno**, ele substitui a página de visão geral padrão em seu aplicativo gerenciado.

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

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|header|Não|O cabeçalho da página de visão geral.|
|descrição|Não|A descrição do seu aplicativo gerenciado.|
|comandos|Não|A matriz de botões adicionais da barra de ferramentas da página Visão geral, consulte [comandos](#commands).|

![Captura de tela mostra a visão geral de um aplicativo gerenciado com um controle de ação de teste para executar um aplicativo de demonstração.](./media/view-definition/overview.png)

## <a name="metrics"></a>Métricas

`"kind": "Metrics"`

A exibição de métricas permite coletar e agregar dados de seus recursos de aplicativo gerenciado em [métricas de Azure monitor](../../azure-monitor/essentials/data-platform-metrics.md).

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

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Não|O título exibido da exibição.|
|version|Não|A versão da plataforma usada para renderizar a exibição.|
|gráficos|Sim|A matriz de gráficos da página de métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido do gráfico.|
|tipo de gráfico|Não|A visualização a ser usada para este gráfico. Por padrão, ele usa um gráfico de linhas. Tipos de gráfico com suporte: `Bar, Line, Area, Scatter` .|
|Métricas|Sim|A matriz de métricas a ser plotada neste gráfico. Para saber mais sobre as métricas com suporte no portal do Azure, consulte [métricas com suporte com Azure monitor](../../azure-monitor/essentials/metrics-supported.md)|

### <a name="metric"></a>Métrica

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|name|Sim|O nome da métrica.|
|aggregationType|Sim|O tipo de agregação a ser usado para essa métrica. Tipos de agregação com suporte: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|Não|Informações adicionais a serem usadas ao determinar o provedor de métricas correto.|
|resourceTagFilter|Não|A matriz de marcas de recurso (será separada com `or` o Word) para a qual as métricas seriam exibidas. Aplica-se na parte superior do filtro de tipo de recurso.|
|resourceType|Sim|O tipo de recurso para o qual as métricas seriam exibidas.|

![A captura de tela mostra uma página de monitoramento chamada esta é a exibição de métricas para um aplicativo gerenciado.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Você pode definir várias exibições deste tipo. Cada exibição representa um tipo de recurso personalizado **exclusivo** do provedor personalizado que você definiu no **mainTemplate.jsem**. Para obter uma introdução aos provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](../custom-providers/overview.md).

Nesta exibição, você pode executar operações GET, PUT, DELETE e POST para o tipo de recurso personalizado. Operações POST podem ser ações personalizadas globais ou ações personalizadas em um contexto de seu tipo de recurso personalizado.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
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

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da exibição. O título deve ser **exclusivo** para cada exibição de CustomResources no seu **viewDefinition.jsem**.|
|version|Não|A versão da plataforma usada para renderizar a exibição.|
|resourceType|Sim|O tipo de recurso personalizado. Deve ser um tipo de recurso personalizado **exclusivo** do seu provedor personalizado.|
|ícone|Não|O ícone da exibição. A lista de ícones de exemplo é definida no [esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Não|Crie um esquema de definição de interface do usuário para o comando criar recurso personalizado. Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md)|
|comandos|Não|A matriz de botões adicionais da barra de ferramentas da exibição CustomResources, consulte [comandos](#commands).|
|colunas|Não|A matriz de colunas do recurso personalizado. Se não estiver definido `name` , a coluna será mostrada por padrão. A coluna deve ter `"key"` e `"displayName"` . Para chave, forneça a chave da propriedade a ser exibida em uma exibição. Se estiver aninhado, use ponto como delimitador, por exemplo, `"key": "name"` ou `"key": "properties.property1"` . Para nome de exibição, forneça o nome de exibição da propriedade a ser exibida em uma exibição. Você também pode fornecer uma `"optional"` propriedade. Quando definido como true, a coluna é ocultada em uma exibição por padrão.|

![Captura de tela mostra uma página de recursos chamada tipo de recurso de teste personalizado e a ação de contexto personalizado de controle.](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandos

Os comandos são uma matriz de botões adicionais da barra de ferramentas que são exibidos na página. Cada comando representa uma ação POST de seu provedor personalizado do Azure definido no **mainTemplate.jsem**. Para obter uma introdução aos provedores personalizados, consulte [visão geral de provedores personalizados do Azure](../custom-providers/overview.md).

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

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O nome exibido do botão de comando.|
|caminho|Sim|O nome da ação do provedor personalizado. A ação deve ser definida no **mainTemplate.jsem**.|
|ícone|Não|O ícone do botão de comando. A lista de ícones de exemplo é definida no [esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Não|Crie o esquema de definição da interface do usuário para o comando. Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Associações

`"kind": "Associations"`

Você pode definir várias exibições deste tipo. Essa exibição permite vincular recursos existentes ao aplicativo gerenciado por meio do provedor personalizado que você definiu no **mainTemplate.jsem**. Para obter uma introdução aos provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](../custom-providers/overview.md).

Nessa exibição, você pode estender os recursos existentes do Azure com base no `targetResourceType` . Quando um recurso é selecionado, ele cria uma solicitação de integração ao provedor personalizado **público** , que pode aplicar um efeito colateral ao recurso. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da exibição. O título deve ser **exclusivo** para cada exibição de associações no seu **viewDefinition.jsem**.|
|version|Não|A versão da plataforma usada para renderizar a exibição.|
|targetResourceType|Sim|O tipo de recurso de destino. Esse é o tipo de recurso que será exibido para integração de recursos.|
|createUIDefinition|Não|Criar esquema de definição de interface do usuário para comando criar recurso de associação. Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre os Aplicativos Gerenciados do Azure, tente perguntar no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida. Portanto, verifique primeiro antes de postar. Adicione a marca `azure-managedapps` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](overview.md).
- Para obter uma introdução aos provedores personalizados, consulte [visão geral de provedores personalizados do Azure](../custom-providers/overview.md).
- Para criar um aplicativo gerenciado do Azure com provedores personalizados do Azure, consulte [tutorial: criar aplicativo gerenciado com ações de provedor personalizado e tipos de recursos](tutorial-create-managed-app-with-custom-provider.md)
