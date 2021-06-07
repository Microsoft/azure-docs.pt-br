---
title: Visão geral do Log Analytics no Azure Monitor
description: Descreve Log Analytics que é uma ferramenta na portal do Azure usada para editar e executar consultas de log para analisar dados em logs de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: d83cc4045f09ed0f704a0d61c6b303406835eb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052289"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Visão geral do Log Analytics no Azure Monitor
O Log Analytics é uma ferramenta no portal do Azure usada para editar e executar consultas de log com alguns dados nos Logs do Azure Monitor. Você pode escrever uma consulta simples que retorna um conjunto de registros e, em seguida, usar recursos de Log Analytics para classificá-los, filtrá-los e analisá-los. Ou você pode escrever uma consulta mais avançada para executar a análise estatística e visualizar os resultados em um gráfico para identificar uma tendência específica. Independentemente de você trabalhar com os resultados de suas consultas interativamente ou usá-las com outros recursos de Azure Monitor, como alertas de consulta de log ou pastas de trabalho, Log Analytics é a ferramenta que você pretende usar para escrever e testá-las. 


> [!TIP]
> Este artigo fornece uma descrição de Log Analytics e cada um de seus recursos. Se você quiser ir diretamente para um tutorial, consulte [log Analytics tutorial](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Iniciando Log Analytics
Inicie Log Analytics nos **logs** no menu **Azure monitor** na portal do Azure. Você também verá essa opção no menu para a maioria dos recursos do Azure. Independentemente de onde você o inicia, será a mesma ferramenta de Log Analytics. O menu usado para iniciar Log Analytics determina os dados que estarão disponíveis no entanto. Se você iniciá-lo no menu do **Azure Monitor** ou no menu dos **workspaces do Log Analytics**, terá acesso a todos os registros em um workspace. Se você selecionar **Logs** em outro tipo de recurso, seus dados serão limitados aos dados de log desse recurso. Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](./scope.md) para obter detalhes.

[![Iniciar o Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Ao iniciar o Log Analytics, a primeira coisa que você verá é uma caixa de diálogo com [consultas de exemplo](../logs/example-queries.md). Eles são categorizados por solução, e você pode procurar ou Pesquisar consultas que correspondam a seus requisitos específicos. Você pode encontrar um que faça exatamente o que você precisa ou carregue um para o editor e modifique-o conforme necessário. A navegação por meio de consultas de exemplo é, na verdade, uma ótima maneira de aprender a escrever suas próprias consultas. É claro que, se você quiser começar com um script vazio e escrevê-lo por conta própria, poderá fechar as consultas de exemplo. Basta clicar nas **consultas** na parte superior da tela se desejar obtê-las de volta.

## <a name="log-analytics-interface"></a>Interface do Log Analytics
A imagem a seguir identifica os diferentes componentes do Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. Barra de ação superior
Controles para trabalhar com a consulta na janela de consulta.

| Opção | Descrição |
|:---|:---|
| Escopo | Especifica o escopo dos dados usados para a consulta. Isso pode ser todos os dados em um espaço de trabalho Log Analytics ou dados para um recurso específico em vários espaços de trabalho. Consulte [escopo da consulta](./scope.md). |
| Botão Executar | Clique para executar a consulta selecionada na janela de consulta. Você também pode pressionar Shift + Enter para executar uma consulta. |
| Seletor de hora | Selecione o intervalo de tempo para os dados disponíveis para a consulta. Isso será substituído se você incluir um filtro de tempo na consulta. Consulte o [escopo de consulta de log e o intervalo de tempo em Log Analytics Azure monitor](./scope.md). |
| Botão Salvar | Salve a consulta no Gerenciador de consultas para o espaço de trabalho. |
 botão Copiar | Copie um link para a consulta, o texto da consulta ou os resultados da consulta para a área de transferência. |
| Botão Nova regra de alerta | Crie uma nova guia com uma consulta vazia. |
| Botão Exportar | Exporte os resultados da consulta para um arquivo CSV ou a consulta para Power Query formato de linguagem de fórmula para uso com o Power bi. |
| Botão fixar no painel | Adicione os resultados da consulta a um painel do Azure. |
| Botão Formatar consulta | Organize o texto selecionado para facilitar a leitura. |
| Botão consultas de exemplo | Abra a caixa de diálogo consultas de exemplo que é exibida quando você abre a Log Analytics pela primeira vez. |
| Botão Gerenciador de consultas | Abra o **Gerenciador de consultas** que fornece acesso a consultas salvas no espaço de trabalho. |


### <a name="2-sidebar"></a>2. Barra lateral
Listas de tabelas no espaço de trabalho, exemplos de consultas e opções de filtro para a consulta atual.

| Tab | Descrição |
|:---|:---|
| Tabelas | Lista as tabelas que fazem parte do escopo selecionado. Selecione **Agrupar por** para alterar o agrupamento das tabelas. Passe o mouse sobre um nome de tabela para exibir uma caixa de diálogo com uma descrição da tabela e das opções para exibir sua documentação e visualizar seus dados. Expanda uma tabela para exibir suas colunas. Clique duas vezes em um nome de tabela ou coluna para adicioná-lo à consulta. |
| Consultas | Lista de consultas de exemplo que você pode abrir na janela de consulta. Essa é a mesma lista exibida quando você abre Log Analytics. Selecione **Agrupar por** para alterar o agrupamento das consultas. Clique duas vezes em uma consulta para adicioná-la à janela de consulta ou passe o mouse sobre ela para obter outras opções. |
| Filtrar | Cria opções de filtro com base nos resultados de uma consulta. Depois de executar uma consulta, as colunas serão exibidas com valores diferentes dos resultados. Selecione um ou mais valores e, em seguida, clique em **aplicar & executar** para adicionar um comando **Where** à consulta e executá-lo novamente. |

### <a name="3-query-window"></a>3. Janela de consulta
A janela de consulta é onde você edita a consulta. Isso inclui IntelliSense para comandos KQL e codificação de cores para aprimorar a legibilidade. Clique em **+** parte superior da janela para abrir outra guia.

Como uma única janela pode incluir várias consultas. Uma consulta não pode incluir linhas em branco, portanto, você pode separar várias consultas em uma janela por uma ou mais linhas em branco. A consulta atual é a que tem o cursor posicionado em qualquer lugar.

Para executar a consulta atual, clique no botão **Executar** ou pressione Shift + Enter.

### <a name="4-results-window"></a>4. Janela de resultados
Os resultados da consulta são exibidos na janela de resultados. Por padrão, os resultados são exibidos como uma tabela. Para exibi-los como um gráfico, selecione **Gráfico** na janela de resultados ou adicione um comando **render** à consulta.

#### <a name="results-view"></a>visualização dos resultados
Exibe os resultados da consulta em uma tabela organizada por colunas e linhas. Clique à esquerda de uma linha para expandir seus valores. Clique no menu suspenso **colunas** para alterar a lista de colunas. Classifique os resultados clicando em um nome de coluna. Filtre os resultados clicando no funil ao lado de um nome de coluna. Limpe os filtros e redefina a classificação executando a consulta novamente.

Selecione **colunas de grupo** para exibir a barra de agrupamento acima dos resultados da consulta. Agrupe os resultados por qualquer coluna arrastando-o para a barra. Crie grupos aninhados nos resultados adicionando colunas adicionais. 

#### <a name="chart-view"></a>Modo de exibição de Gráfico
Exibe os resultados como um dos vários tipos de gráfico disponíveis. Você pode especificar o tipo de gráfico em um comando **render** em sua consulta ou selecioná-lo na lista suspensa **tipo de visualização** .

| Opção | Descrição |
|:---|:---|
| **Tipo de visualização** | Tipo de gráfico a ser exibido. |
| **Eixo X** | Coluna nos resultados a serem usados para o eixo X 
| **Eixo Y** | Coluna nos resultados a serem usados para o eixo Y. Normalmente, isso será uma coluna numérica. |
| **Dividir por** | Coluna nos resultados que define a série no gráfico. Uma série é criada para cada valor na coluna. |
| **Aggregation** | Tipo de agregação a ser executada nos valores numéricos no eixo Y. |

## <a name="relationship-to-azure-data-explorer"></a>Relação com o Azure Data Explorer
Se você já estiver familiarizado com a interface do usuário da Web do Azure Data Explorer, Log Analytics deverá parecer familiar. Isso ocorre porque ele é criado sobre o Azure Data Explorer e usa a mesma linguagem de consulta Kusto (KQL). Log Analytics adiciona recursos específicos a Azure Monitor como filtragem por intervalo de tempo e a capacidade de criar uma regra de alerta a partir de uma consulta. Ambas as ferramentas incluíram um Gerenciador que permite que você examine a estrutura de tabelas disponíveis, mas a interface do usuário da Web do Azure Data Explorer trabalha principalmente com tabelas em bancos de dados do Azure Data Explorer enquanto Log Analytics trabalha com tabelas em um espaço de trabalho Log Analytics. 

## <a name="next-steps"></a>Próximas etapas
- Percorra um [tutorial sobre como usar log Analytics no portal do Azure](./log-analytics-tutorial.md).
- Percorra um [tutorial sobre como escrever consultas](./get-started-queries.md).