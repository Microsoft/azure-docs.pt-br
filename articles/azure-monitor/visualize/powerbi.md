---
title: Importar dados do Log Analytics do Azure para o Power BI | Microsoft Docs
description: O Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics para o Power BI e configurá-los para serem atualizados automaticamente.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 69e953f6d2cb9542a589fb2e282ff166c43fc0a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043602"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importar Azure Monitor dados de log para Power BI


[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece visualizações e relatórios avançados para análise de diferentes conjuntos de dados.  Você pode importar os resultados de uma Azure Monitor consulta de log em um conjunto de dados Power BI para que você possa aproveitar seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na Web e em dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Visão geral
Para importar dados de um [espaço de trabalho log Analytics](../logs/manage-access.md) no Azure Monitor para Power bi, você cria um conjunto de dados no Power bi com base em uma [consulta de log](../logs/log-query-overview.md) no Azure monitor.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Depois você pode compilar relatórios do Power BI que usam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporte sua consulta do Log Analytics para a [linguagem do Power Query (M)](/powerquery-m/power-query-m-language-specification).  Depois use-a para criar uma consulta no Power BI Desktop e publicá-la no Power BI como um conjunto de dados.  Os detalhes para esse processo são descritos abaixo.

![Log Analytics para Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece criando uma [consulta de log](../logs/log-query-overview.md) que retorne os dados que você deseja que preencham o Power bi DataSet.  Depois exporte essa consulta para a [linguagem do Power Query (M)](/powerquery-m/power-query-m-language-specification) que pode ser usado pelo Power BI Desktop.

> [!WARNING]
> Tenha cuidado para [otimizar sua consulta](../logs/query-optimization.md) para que ela não leve muito tempo para ser executada ou possa atingir o tempo limite. Observe o valor **TimeSpan** na consulta exportada que define o período de tempo dos dados que a consulta irá recuperar. Use o menor TimeSpan que você precisa para limitar a quantidade de dados que a consulta retorna.

1. [Crie a consulta de log no log Analytics](../logs/log-analytics-tutorial.md) para extrair os dados para seu conjunto.
2. Selecione **Exportar**  >  **consulta de Power bi (M)**.  Isso exporta a consulta para um arquivo de texto chamado **PowerBIQuery.txt**. 

    ![Exportar pesquisa de logs](media/powerbi/export-analytics.png)

3. Abra o arquivo de texto e copie o conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Importar a consulta no Power BI Desktop
O Power BI Desktop é um aplicativo de área de trabalho que permite criar conjuntos de dados e relatórios que podem ser publicados no Power BI.  Você também pode usá-lo para criar uma consulta usando o idioma Power Query exportado do Azure Monitor. 

1. Instale o [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e depois abra o aplicativo.
2. Selecione **obter dados**  >  **consulta em branco** para abrir uma nova consulta.  Depois selecione **Editor Avançado** e cole o conteúdo do arquivo exportado na consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/powerbi/desktop-new-query.png)

5. A consulta é executada, e seus resultados são exibidos.  Pode ser que sejam solicitadas as credenciais para se conectar ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Query1**. Clique em **Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Ao publicar no Power BI, serão criados um conjunto de dados e um relatório.  Se você criar um relatório no Power BI Desktop, isso será publicado com seus dados.  Se não, será criado um relatório em branco.  É possível modificar o relatório no Power BI ou criar um novo com base no conjunto de dados.

1. Crie um relatório baseado nos seus dados.  Use a [documentação do Power BI Desktop](/power-bi/desktop-report-view) se não estiver familiarizado com ele.  
1. Quando você estiver pronto para enviá-lo ao Power BI, clique em **Publicar**.  
1. Quando solicitado, selecione um destino em sua conta do Power BI.  A menos que tenha um destino específico em mente, use **Meu workspace**.

    ![Publicação no Power BI Desktop](media/powerbi/desktop-publish.png)

1. Quando a publicação for concluída, clique em **Abrir no Power BI** para abrir o Power BI com seu novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terá os mesmos dados vistos anteriormente no Power BI Desktop.  Você precisa atualizar o conjunto de dados periodicamente para executar a consulta novamente e preenchê-la com os dados mais recentes de Azure Monitor.  

1. Clique no workspace no qual você carregou o relatório e selecione o menu **Conjuntos de dados**. 
1. Selecione o menu de contexto ao lado do novo conjunto de dados e selecione **Configurações**. 
1. Em **Credenciais da fonte de dados** você deve ver uma mensagem de que as credenciais são inválidas.  Isso ocorre porque você ainda não forneceu as credenciais para o conjunto de dados usar quando ele atualiza os dados.  
1. Clique em **Editar credenciais** e especifique as credenciais com acesso ao espaço de trabalho Log Analytics no Azure monitor. Se você precisar de autenticação de dois fatores, selecione **OAuth2** para que o **método de autenticação** seja solicitado a fazer logon com suas credenciais.

    ![Agenda do Power BI](media/powerbi/powerbi-schedule.png)

5. Em **Atualização agendada**, ative a opção **Manter seus dados atualizados**.  Outra opção é alterar a **Frequência de atualização** e um ou mais períodos específicos para executar a atualização.

    ![Atualização do Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](../logs/log-query-overview.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [Power bi](https://powerbi.microsoft.com) criar visualizações com base em exportações de log Azure monitor.