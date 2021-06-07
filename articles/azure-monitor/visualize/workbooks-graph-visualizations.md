---
title: Azure Monitor visualizações de grafo de pasta de trabalho
description: Saiba mais sobre todas as visualizações de grafo de pasta de trabalho Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100606793"
---
# <a name="graph-visualizations"></a>Visualizações de grafo

As pastas de trabalho dão suporte à visualização de grafos arbitrários com base em dados de logs para mostrar as relações entre entidades de monitoramento.

O gráfico abaixo mostra dados que fluem para dentro/para fora de um computador por meio de várias portas de/para computadores externos. Ele é colorido por tipo (computador versus porta x IP externo) e os tamanhos de borda correspondem à quantidade de dados que fluem entre eles. Os dados subjacentes vêm da consulta KQL direcionamento a conexões de VM.

[![Captura de tela da exibição de resumo do bloco](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Adicionando um grafo
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para sua análise.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Definir a visualização como **grafo**
6. Selecione o botão **configurações de gráfico** para abrir o painel configurações
7. Em _campos de layout_ na parte inferior, defina:
    * ID do nó: `Id`
    * ID da origem: `SourceId`
    * ID de destino: `TargetId`
    * Rótulo de borda: `None`
    * Tamanho da borda: `Calls`
    * Tamanho do nó: `None`
    * Tipo de cores: `Categorical`
    * Campo de cor do nó: `Kind`
    * Paleta de cores: `Pastel`
8. Em _configurações de formato de nó_ na parte superior, defina:
    * _Conteúdo principal_-use a coluna: `Name` , renderizador de coluna: `Text`
    * _Conteúdo do centro_-usar coluna: `Calls` , renderizador de coluna: `Big Number` , paleta de cores: `None`
    * _Conteúdo inferior_-use a coluna: `Kind` , renderizador de coluna: `Text`
9. Selecione o botão _salvar e fechar_ na parte inferior do painel.

[![Captura de tela da exibição de Resumo de bloco com a consulta e as configurações acima.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Configurações de grafo

| Configuração         | Explicação                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Seleciona uma coluna que fornece a ID exclusiva de nós no grafo. O valor da coluna pode ser uma cadeia de caracteres ou um número. |
| `Source Id`     | Seleciona uma coluna que fornece as IDs de nós de origem para bordas no grafo. Os valores devem ser mapeados para um valor na coluna _ID do nó_ . |
| `Target Id`     | Seleciona uma coluna que fornece as IDs dos nós de destino para as bordas no grafo. Os valores devem ser mapeados para um valor na coluna _ID do nó_ . |
| `Edge Label`    | Seleciona uma coluna que fornece rótulos de borda no grafo.                                                            |
| `Edge Size`     | Seleciona uma coluna que fornece a métrica na qual as larguras de borda serão baseadas.                                |
| `Node Size`     | Seleciona uma coluna que fornece a métrica na qual as áreas de nó serão baseadas.                                 |
| `Coloring Type` | Usado para escolher o esquema de cores do nó.                                                                            |

## <a name="node-coloring-types"></a>Tipos de cores de nó

| Tipo de cores | Explicação |
|:------------- |:------------|
| `None`        | Todos os nós têm a mesma cor. |
| `Categorical` | Os nós recebem cores com base no valor ou na categoria de uma coluna no conjunto de resultados. No exemplo acima, a cor é baseada no _tipo_ de coluna do conjunto de resultados. As paletas com suporte são `Default` , `Pastel` e `Cool tone` .  |
| `Field Based` | Nesse tipo, uma coluna fornece valores RGB específicos a serem usados para o nó. Fornece mais flexibilidade, mas geralmente requer mais trabalho a ser habilitado.  |

## <a name="node-format-settings"></a>Configurações de formato de nó

Os autores de grafos podem especificar qual conteúdo vai para as diferentes partes de um nó: superior, esquerda, centro, direita e inferior. Os grafos podem usar qualquer pasta de trabalho de renderizadores com suporte (texto, número grande, linhas do Spark, ícone, etc.).

## <a name="field-based-node-coloring"></a>Cores do nó baseado em campo

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para sua análise.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Definir a visualização como *grafo*
6. Selecione o botão **configurações de gráfico**  para abrir o painel configurações.
7. Em *campos de layout* na parte inferior, defina:
    * ID do nó:`Id`
    * ID da origem: `SourceId`
    * ID de destino: `TargetId`
    * Rótulo de borda: `None`
    * Tamanho da borda: `Calls`
    * Tamanho do nó: `Node`
    * Tipo de cores: `Field Based`
    * Campo de cor do nó: `Color`
8. Em *configurações de formato de nó* na parte superior, insira o seguinte.
    * No *conteúdo principal*, defina:
        * Usar coluna: `Name` .
        * Renderizador de coluna: `Text` .
    * Em *conteúdo do centro*, defina:
        * Usar coluna: `Calls`
        * Renderizador de coluna: `Big Number`
        * Paleta de cores: `None`
    * Em *conteúdo inferior*, defina:
        * Usar coluna: `Kind`
        * Renderizador de coluna: `Text` .
9. Selecione o **botão salvar e fechar** na parte inferior do painel.

[![Captura de tela mostrando a criação de uma visualização de gráfico com cores de nó base de campo.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Os grafos também dão suporte ao renderizador de barra de composição. Para saber mais, visite a [documentação da barra de composição](workbooks-composite-bar.md).
* Saiba mais sobre as [fontes de dados](workbooks-data-sources.md) que você pode usar em pastas de trabalho.
