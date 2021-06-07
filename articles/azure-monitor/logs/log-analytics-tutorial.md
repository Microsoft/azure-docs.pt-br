---
title: Tutorial do Log Analytics
description: Aprenda com este tutorial a usar os recursos do Log Analytics no Azure Monitor para criar e executar uma consulta de log e analisar os resultados dela no portal do Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: a5788e5ac64c1c74a06c94c5634a05315ee098b8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799285"
---
# <a name="log-analytics-tutorial"></a>Tutorial do Log Analytics
O Log Analytics é uma ferramenta do portal do Azure usada para editar e executar consultas de log dos dados coletados pelos logs do Azure Monitor e analisar os resultados de maneira interativa. Use as consultas do Log Analytics para recuperar registros que correspondem a critérios específicos, identificar tendências, analisar padrões e fornecer uma variedade de insights sobre seus dados. 

Este tutorial descreve a interface do Log Analytics, apresenta algumas consultas básicas e mostra como trabalhar com os resultados. Você aprenderá o seguinte:

> [!div class="checklist"]
> * Entender o esquema de dados de log
> * Gravar e executar consultas simples e modificar o intervalo de tempo para consultas
> * Filtrar, classificar e agrupar resultados de consulta
> * Exibir, modificar e compartilhar visuais de resultados de consulta
> * Carregar, exportar e copiar consultas e resultados

> [!IMPORTANT]
> Este tutorial usa os recursos do Log Analytics para criar e executar uma consulta em vez de trabalhar com a própria consulta. Você aproveitará os recursos do Log Analytics para criar uma consulta e usar outra consulta de exemplo. Quando estiver pronto para aprender a sintaxe das consultas e começar a editar diretamente a própria consulta, siga o [tutorial da linguagem de consulta Kusto](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Esse tutorial descreve várias consultas de exemplo que você pode editar e executar no Log Analytics, aproveitando vários dos recursos que você aprenderá neste tutorial.


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial usa o [ambiente de demonstração do Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), que inclui vários dados de exemplo que dão suporte às consultas de exemplo. Você também pode usar sua assinatura do Azure, mas talvez não tenha os dados nas mesmas tabelas.

## <a name="open-log-analytics"></a>Abrir Log Analytics
Abra o [ambiente de demonstração do Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) ou selecione **Logs** no menu do Azure Monitor na sua assinatura. Isso definirá o escopo inicial para um workspace do Log Analytics, o que significa que a consulta selecionará todos os dados desse workspace. Se você selecionar **Logs** no menu de um recurso do Azure, o escopo será definido como somente os registros desse recurso. Veja [Escopo da consulta de log](./scope.md) para obter detalhes sobre o escopo.

Veja o escopo no canto superior esquerdo da tela. Se você estiver usando seu ambiente, verá uma opção para selecionar outro escopo, mas essa opção não está disponível no ambiente de demonstração.

[![Escopo da consulta](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Esquema de tabela
O lado esquerdo da tela inclui a guia **Tabelas**, que permite inspecionar as tabelas disponíveis no escopo atual. Por padrão, elas são agrupadas por **Solução**, mas você altera o agrupamento ou filtra as tabelas. 

Expanda a solução de **Gerenciamento de Log** e localize a tabela **AzureActivity**. Expanda a tabela para ver o esquema ou posicione o cursor sobre o nome da tabela para mostrar mais informações sobre ela. 

[![Exibição de tabelas](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Clique em **Saiba mais** para acessar a referência de tabela que documenta cada tabela e as respectivas colunas. Clique em **Visualizar dados** para ter uma visão rápida de alguns registros recentes na tabela. Isso pode ser útil para garantir que esses são os dados esperados antes de você realmente executar uma consulta com eles.

[![Dados de exemplo](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Escreva uma consulta
Vamos continuar e escrever uma consulta usando a tabela **AzureActivity**. Clique duas vezes no nome dela para adicioná-la à janela de consulta. Você também pode digitar o nome diretamente na janela e, até mesmo, obter o IntelliSense, que ajudará a preencher os nomes das tabelas no escopo atual e os comandos KQL.

Essa é a consulta mais simples que podemos escrever. Ela só retorna todos os registros em uma tabela. Execute-a clicando no botão **Executar** ou clicando em SHIFT + ENTER com o cursor posicionado em qualquer lugar do texto da consulta.

[![Resultados da consulta](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Você pode ver que temos resultados. O número de registros retornados pela consulta é exibido no canto inferior direito. 

## <a name="filter"></a>Filtrar

Vamos adicionar um filtro à consulta para reduzir o número de registros retornados. Selecione a guia **Filtrar** no painel esquerdo. Isso mostra colunas diferentes nos resultados da consulta que você pode usar para filtrar os resultados. Os valores superiores nessas colunas são exibidos com o número de registros que têm esse valor. Clique em **Administrativo** em **CategoryValue** e em **Aplicar e Executar**. 

[![Painel Consulta](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Uma instrução **where** é adicionada à consulta com o valor selecionado. Agora, os resultados incluem apenas os registros com esse valor e, portanto, você pode ver que a contagem de registros foi reduzida.

[![Resultados da consulta filtrados](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Intervalo de horas
Todas as tabelas de um workspace do Log Analytics têm uma coluna chamada **TimeGenerated**, que é a hora em que o registro foi criado. Todas as consultas têm um intervalo de tempo que limita os resultados aos registros com um valor **TimeGenerated** dentro desse intervalo. O intervalo de tempo pode ser definido na consulta ou com o seletor na parte superior da tela.

Por padrão, a consulta retornará os registros das últimas 24 horas. Selecione a lista suspensa **Intervalo de tempo** e altere-o para **7 dias**. Clique em **Executar** novamente para retornar os resultados. Você pode ver que os resultados são retornados, mas temos uma mensagem aqui que indica que não estamos vendo todos os resultados. Isso ocorre porque o Log Analytics pode retornar, no máximo, 30 mil registros, e nossa consulta retornou mais registros que isso. 

[![Intervalo de tempo](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Condições de várias consultas
Vamos reduzir ainda mais nossos resultados adicionando outra condição de filtro. Uma consulta pode incluir qualquer número de filtros para ter como alvo exatamente o conjunto de registros desejado. Selecione **Êxito** em **ActivityStatusValue** e clique em **Aplicar e Executar**. 

[![Vários filtros dos resultados da consulta](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analisar resultados
Além de ajudar você a escrever e executar consultas, o Log Analytics fornece recursos para trabalhar com os resultados. Comece expandindo um registro para ver os valores de todas as colunas.

[![Expandir registro](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Clique no nome de qualquer coluna para classificar os resultados por essa coluna. Clique no ícone de filtro ao lado dela para fornecer uma condição de filtro. Isso é semelhante à adição de uma condição de filtro à própria consulta, exceto pelo fato de que esse filtro será limpo se a consulta for executada novamente. Use esse método se desejar analisar rapidamente um conjunto de registros como parte da análise interativa.

Por exemplo, defina um filtro na coluna **CallerIpAddress** para limitar os registros a um só chamador. 

[![Filtro dos resultados da consulta](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Em vez de filtrar os resultados, você pode agrupar os registros por uma coluna específica. Limpe o filtro recém-criado e ative o controle deslizante **Agrupar colunas**. 

[![Agrupar colunas](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Agora, arraste a coluna **CallerIpAddress** para a linha de agrupamento. Os resultados agora são organizados por essa coluna, e você pode recolher cada grupo para ajudar com a sua análise.

[![Resultados da consulta agrupados](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Trabalhar com gráficos
Vamos dar uma olhada em uma consulta que usa dados numéricos que podemos ver em um gráfico. Em vez de criar uma consulta, selecionaremos uma consulta de exemplo.

Clique em **Consultas** no painel esquerdo. Esse painel inclui consultas de exemplo que você pode adicionar à janela de consulta. Se você estiver usando seu workspace, deverá ter uma variedade de consultas em várias categorias, mas se estiver usando o ambiente de demonstração, só poderá ver uma categoria de **workspaces do Log Analytics**. Expanda-a para ver as consultas da categoria.

Clique na consulta chamada **Contagem de Solicitações por ResponseCode**. Isso adicionará a consulta à janela de consulta. Observe que a nova consulta é separada da outra por uma linha em branco. Uma consulta em KQL é encerrada quando encontra uma linha em branco. Portanto, elas são vistas como consultas separadas. 

[![Nova consulta](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

A consulta atual é aquela na qual o cursor está posicionado. Você pode ver que a primeira consulta é realçada, indicando que ela é a consulta atual. Clique em qualquer lugar da nova consulta para selecioná-la e clique no botão **Executar** para executá-la.

[![Gráfico dos resultados da consulta](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Observe que essa saída é um gráfico em vez de uma tabela como a última consulta. Isso ocorre porque a consulta de exemplo usa um comando [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) no final. Observe que há várias opções para trabalhar com o gráfico, como alterá-lo para outro tipo.

Tente selecionar **Resultados** para ver a saída da consulta como uma tabela. 

[![Tabela dos resultados da consulta](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe como usar o Log Analytics, conclua o tutorial sobre como usar consultas de log.
> [!div class="nextstepaction"]
> [Escrever consultas de log do Azure Monitor](get-started-queries.md)
