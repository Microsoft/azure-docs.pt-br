---
title: Azure Monitor visualizações de árvore da pasta de trabalho
description: Saiba mais sobre as visualizações de árvore da pasta de trabalho do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100606654"
---
# <a name="tree-visualizations"></a>Visualizações de árvore

As pastas de trabalho dão suporte a exibições hierárquicas por meio de grades de árvore. As árvores permitem que algumas linhas sejam expansíveis no próximo nível para uma experiência de busca detalhada.

O exemplo a seguir mostra as métricas de integridade do contêiner (tamanho do conjunto de trabalho) visualizado como uma grade de árvore. Os nós de nível superior aqui são os nós do AKS (serviço kubernetes do Azure), o próximo nível são pods e o nível final são contêineres. Observe que você ainda pode formatar suas colunas como em uma grade (calor, ícones, link). Nesse caso, a fonte de dados subjacente é um espaço de trabalho Log Analytics com logs AKS.

[![Captura de tela da exibição de resumo do bloco](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Adicionando uma grade de árvore
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar consulta* para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Definir a visualização como **grade**
6. Selecione o botão **configurações de coluna** para abrir o painel configurações
7. Na seção **árvore/grupo por configurações** na parte inferior, defina:
    * Tipo de árvore: `Parent/Child`
    * Campo de ID: `Id`
    * Campo de ID pai: `ParentId`
    * Mostrar o expansor em: `Name`
    * Selecione *expandir o nível superior da caixa de seleção de árvore* .
8. Na seção _colunas_ na parte superior, defina:
    * Renderizador de _ID_ -coluna:`Hidden`
    * _ID pai_ -renderizador de coluna: `Hidden`
    * _Solicitações_ -renderizador de coluna: `Bar` , cor: `Blue` , valor mínimo: `0`
9. Selecione o botão **salvar e fechar** na parte inferior do painel.

[![Captura de tela da exibição de Resumo de bloco com a consulta e as configurações acima.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Configurações de árvore

| Configuração | Explicação |
|:------------- |:-------------|
| `Id Field` | A ID exclusiva de cada linha na grade. |
| `Parent Id Field` | A ID do pai da linha atual. |
| `Show the expander on` | A coluna na qual mostrar o expansor de árvore. É comum que as grades de árvore ocultem seus campos ID e ID pai porque não são muito legíveis. Em vez disso, o expansor aparece em um campo com um valor mais legível, como o nome da entidade. |
| `Expand the top level of the tree` | Se marcada, a grade de árvore será expandida no nível superior. Útil se você quiser mostrar mais informações por padrão. |

## <a name="grouping-in-a-grid"></a>Agrupando em uma grade

O agrupamento permite que você crie exibições hierárquicas semelhantes àquelas acima com consultas significativamente mais simples. Você perde a agregação nos nós internos da árvore, mas isso será aceitável para alguns cenários. Use *Group by* para criar exibições de árvore quando o conjunto de resultados subjacente não puder ser transformado em um formato livre adequado, por exemplo: alerta, integridade e dados de métrica.

## <a name="adding-a-tree-using-grouping"></a>Adicionando uma árvore usando o agrupamento

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar consulta* para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Defina a visualização como *grade*.
2. Selecione o **botão Configurações de coluna** para abrir o painel configurações.
3. Na seção *árvore/grupo por configurações* na parte inferior, defina:
    * Tipo de árvore: `Group By`
    * Agrupar por: `App`
    * Em seguida, por: `None`
    * Selecione *expandir o nível superior da caixa de seleção de árvore* .
4. Na seção *colunas* na parte superior, defina:
    * Renderizador de coluna de *aplicativo* :`Hidden`
    * *Solicitações* -renderizador de coluna: `Bar` , cor: `Blue` , valor mínimo: `0`
5. Selecione o botão **salvar e fechar** na parte inferior do painel.

[![Captura de tela mostrando a criação de uma visualização em árvore em pastas de trabalho](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar um [grafo em pastas de trabalho](workbooks-graph-visualizations.md).
* Saiba como criar um [bloco em pastas de trabalho](workbooks-tile-visualizations.md).
