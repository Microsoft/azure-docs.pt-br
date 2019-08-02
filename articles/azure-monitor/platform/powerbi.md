---
title: Importar dados do Log Analytics do Azure para o Power BI | Microsoft Docs
description: O Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics para o Power BI e configurá-los para serem atualizados automaticamente.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2019
ms.author: bwren
ms.openlocfilehash: 0b1627306f1a8e9d9285c72118bfebdcb53d369b
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626112"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importar dados de log do Azure Monitor no Power BI


O [Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Você pode importar os resultados de uma consulta de log do Azure Monitor em um conjunto de dados do Power BI, portanto, você pode tirar proveito de seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na web e dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Visão geral
Para importar dados de um [espaço de trabalho do Log Analytics](manage-access.md) no Azure Monitor para o Power BI, você cria um conjunto de dados no Power BI com base em um [consulta de log](../log-query/log-query-overview.md) no Azure Monitor.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Depois você pode compilar relatórios do Power BI que usam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporte sua consulta do Log Analytics para a [linguagem do Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification).  Depois use-a para criar uma consulta no Power BI Desktop e publicá-la no Power BI como um conjunto de dados.  Os detalhes para esse processo são descritos abaixo.

![Log Analytics para Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece pela criação de um [consulta de log](../log-query/log-query-overview.md) que retorna os dados que você deseja preencher o conjunto de dados do Power BI.  Depois exporte essa consulta para a [linguagem do Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) que pode ser usado pelo Power BI Desktop.

1. [Criar a consulta de log no Log Analytics](../log-query/get-started-portal.md) para extrair os dados para seu conjunto de dados.
2. Selecione **exportar** > **Power BI Query (M)** .  Isso exporta a consulta para um arquivo de texto chamado **Powerbiquery**. 

    ![Exportar pesquisa de logs](media/powerbi/export-analytics.png)

3. Abra o arquivo de texto e copie o conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Importar a consulta no Power BI Desktop
O Power BI Desktop é um aplicativo de área de trabalho que permite criar conjuntos de dados e relatórios que podem ser publicados no Power BI.  Você também pode usá-lo para criar uma consulta usando a linguagem do Power Query exportada do Azure Monitor. 

1. Instale o [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e depois abra o aplicativo.
2. Selecione **Obter Dados** > **Consulta em Branco** para abrir uma nova consulta.  Depois selecione **Editor Avançado** e cole o conteúdo do arquivo exportado na consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/powerbi/desktop-new-query.png)

5. A consulta é executada, e seus resultados são exibidos.  Pode ser que sejam solicitadas as credenciais para se conectar ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Query1**. Clique em **Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Ao publicar no Power BI, serão criados um conjunto de dados e um relatório.  Se você criar um relatório no Power BI Desktop, isso será publicado com seus dados.  Se não, será criado um relatório em branco.  É possível modificar o relatório no Power BI ou criar um novo com base no conjunto de dados.

1. Crie um relatório baseado nos seus dados.  Use a [documentação do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se não estiver familiarizado com ele.  
1. Quando você estiver pronto para enviá-lo ao Power BI, clique em **Publicar**.  
1. Quando solicitado, selecione um destino em sua conta do Power BI.  A menos que tenha um destino específico em mente, use **Meu workspace**.

    ![Publicação no Power BI Desktop](media/powerbi/desktop-publish.png)

1. Quando a publicação for concluída, clique em **Abrir no Power BI** para abrir o Power BI com seu novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terá os mesmos dados vistos anteriormente no Power BI Desktop.  Você precisa atualizar o conjunto de dados periodicamente para executar a consulta novamente e preenchê-lo com os dados mais recentes do Azure Monitor.  

1. Clique no workspace no qual você carregou o relatório e selecione o menu **Conjuntos de dados**. 
1. Selecione o menu de contexto ao lado do novo conjunto de dados e selecione **Configurações**. 
1. Em **Credenciais da fonte de dados** você deve ver uma mensagem de que as credenciais são inválidas.  Isso ocorre porque você ainda não forneceu as credenciais para o conjunto de dados usar quando ele atualiza os dados.  
1. Clique em **Editar credenciais** e especifique as credenciais com acesso ao espaço de trabalho do Log Analytics no Azure Monitor. Se você precisar de autenticação de dois fatores, selecione **OAuth2** para o **método de autenticação** ao ser solicitado a fazer logon com suas credenciais.

    ![Agenda do Power BI](media/powerbi/powerbi-schedule.png)

5. Em **Atualização agendada**, ative a opção **Manter seus dados atualizados**.  Outra opção é alterar a **Frequência de atualização** e um ou mais períodos específicos para executar a atualização.

    ![Atualização do Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](../log-query/log-query-overview.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [Power BI](https://powerbi.microsoft.com) para criar visualizações baseadas em exportações do log do Azure Monitor.