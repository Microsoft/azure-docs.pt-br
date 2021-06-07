---
title: Azure Monitor parâmetros de menu suspenso da pasta de trabalho
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas contendo parâmetros de lista suspensa
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717189"
---
# <a name="workbook-drop-down-parameters"></a>Parâmetros de menu suspenso da pasta de trabalho

Os menus suspensos permitem que o usuário colete um ou mais valores de entrada de um conjunto conhecido (por exemplo, selecione uma das solicitações do aplicativo). Os menus suspensos fornecem uma maneira amigável ao usuário de coletar entradas arbitrárias de usuários. Os menus suspensos são especialmente úteis para habilitar a filtragem em seus relatórios interativos. 

A maneira mais fácil de especificar um menu suspenso é fornecendo uma lista estática na configuração de parâmetro. Uma maneira mais interessante é obter a lista dinamicamente por meio de uma consulta KQL. As configurações de parâmetro também permitem que você especifique se ela é única ou múltipla seleção, e se ela é de seleção múltipla, como o conjunto de resultados deve ser formatado (delimitador, cotação, etc.).

## <a name="creating-a-static-drop-down-parameter"></a>Criando um parâmetro suspenso estático

1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `Environment`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. Permitir `multiple selection` : `unchecked`
    5. Obter dados de: `JSON`
5. No bloco de texto de entrada JSON, insira este trecho de JSON:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Pressione o `Update` botão azul.
7. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.
8. O parâmetro de ambiente será uma lista suspensa com os três valores.

    ![Imagem mostrando a criação de um estático afogado](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Criando uma lista suspensa estática com grupos de itens

Se o resultado da consulta/JSON contiver um campo "grupo", a lista suspensa exibirá grupos de valores. Siga o exemplo acima, mas use o JSON a seguir em vez disso:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Imagem mostrando um exemplo de uma lista suspensa agrupada](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Criando um parâmetro suspenso dinâmico
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `RequestName`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. Permitir `multiple selection` : `unchecked`
    5. Obter dados de: `Query`
5. No bloco de texto de entrada JSON, insira este trecho de JSON:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Pressione o `Run Query` botão azul.
2. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.
3. O parâmetro RequestName será uma lista suspensa dos nomes de todas as solicitações no aplicativo.

    ![Imagem mostrando a criação de uma lista suspensa dinâmica](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parâmetro suspenso de referência

### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. No editor de KQL, insira este trecho de código

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Isso expande o tempo de avaliação da consulta para:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Execute a consulta para ver os resultados. Opcionalmente, renderizá-lo como um gráfico.

    ![Imagem mostrando uma lista suspensa referenciada em KQL](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valor do parâmetro, rótulo, seleção e grupo
A consulta usada no parâmetro suspenso dinâmico acima apenas retorna uma lista de valores que são processados de forma fiel na lista suspensa. Mas e se você quisesse um nome de exibição diferente ou um deles a ser selecionado? Os parâmetros suspensos permitem isso por meio das colunas valor, rótulo, seleção e grupo.

O exemplo a seguir mostra como obter uma lista de dependências Application Insights cujos nomes de exibição são estilizados com um Emoji, tem o primeiro selecionado e é agrupado por nomes de operação.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Imagem mostrando um parâmetro suspenso usando opções de valor, rótulo, seleção e grupo](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opções de parâmetro de lista suspensa
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | O valor selecionado | OBTER fabrikamaccount |
| `{DependencyName:label}` | O rótulo selecionado | 🌐 OBTER fabrikamaccount |
| `{DependencyName:value}` | O valor selecionado | OBTER fabrikamaccount |

## <a name="multiple-selection"></a>Seleção múltipla
Os exemplos até agora definem explicitamente o parâmetro para selecionar apenas um valor na lista suspensa. Os parâmetros suspensos também oferecem suporte para `multiple selection` isso é tão simples quanto marcar a `Allow multiple selection` opção. 

O usuário também tem a opção de especificar o formato do conjunto de resultados por meio `delimiter` das `quote with` configurações e. O padrão apenas retorna os valores como uma coleção neste formulário: ' a ', ' b ', ' C'. Eles também têm a opção de limitar o número de seleções.

O KQL que faz referência ao parâmetro precisará ser alterado para funcionar com o formato do resultado. A maneira mais comum de habilitá-lo é por meio do `in` operador.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Aqui está um exemplo para a lista suspensa de seleção múltipla no trabalho:

![Imagem mostrando um parâmetro suspenso de seleção múltipla](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](./workbooks-overview.md#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.