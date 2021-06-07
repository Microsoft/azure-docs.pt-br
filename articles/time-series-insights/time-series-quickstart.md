---
title: 'Início Rápido: Gerenciador do Azure Time Series Insights – Azure Time Series Insights | Microsoft Docs'
description: Saiba como começar a usar o Gerenciador do Azure Time Series Insights. Visualize grandes volumes de dados do IoT e faça um tour pelos principais recursos do seu ambiente.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/30/2020
ms.openlocfilehash: dd5ed52187a7cd23142b59e20c91c6d125946a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613774"
---
# <a name="quickstart-explore-azure-time-series-insights-gen1"></a>Início Rápido: Explorar o Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este guia de início rápido do Gerenciador do Azure Time Series Insights ajuda você a começar a usar o Azure Time Series Insights Explorer em um ambiente de demonstração gratuito. Neste início rápido, você aprenderá a usar o navegador da Web para visualizar grandes volumes de dados de IoT e conhecerá os principais recursos atualmente em disponibilidade geral.

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele fornece uma exibição global dos dados, o que permite validar rapidamente sua solução de IoT e evitar um tempo de inatividade dispendioso de dispositivos críticos. O Azure Time Series Insights ajuda você a descobrir tendências ocultas, detectar anomalias e realizar análises de causa raiz quase em tempo real.

Para obter flexibilidade adicional, adicione o Azure Time Series Insights a um aplicativo já existente por meio de suas poderosas [APIs REST](./concepts-query-overview.md) e [SDK do cliente](https://github.com/microsoft/tsiclient). Você pode usar as APIs para armazenar, consultar e consumir dados de série temporal em um aplicativo cliente de sua escolha. Você também pode usar o SDK do cliente para adicionar componentes de interface do usuário ao aplicativo atual.

Este início rápido do Gerenciador do Azure Time Series Insights oferece um tour guiado de recursos.

> [!IMPORTANT]
> Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se ainda não tiver criado uma.

## <a name="prepare-the-demo-environment"></a>Preparar o ambiente de demonstração

1. No navegador, acesse a [Demonstração Gen1](https://insights.timeseries.azure.com/demo).

1. Se solicitado, entre no Gerenciador do Azure Time Series Insights usando suas credenciais da conta do Azure.

1. A página de tour rápido do Azure Time Series Insights será exibida. Escolha **Avançar** para iniciar o tour rápido.

   [![Início Rápido de boas-vindas – Selecione Avançar](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explorar o ambiente de demonstração

1. O **Painel de seleção de tempo** é exibido. Use esse painel para selecionar um período a ser visualizado.

   [![Painel de seleção de tempo](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Escolha um período de tempo e arraste-o na região. Em seguida, escolha **Pesquisar**.

   [![Selecionar um período](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   O Azure Time Series Insights exibe uma visualização de gráfico para o período especificado. Você pode realizar várias ações dentro do gráfico de linha. Por exemplo, você pode filtrar, fixar, classificar e empilhar.

   Para retornar ao **Painel de seleção de tempo**, escolha a seta para baixo, conforme mostrado:

   [![Gráfico](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Escolha **Adicionar** no **Painel de termos** para adicionar um novo termo de pesquisa.

   [![Adicionar painel de termos de pesquisa](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. No gráfico, você pode selecionar uma região, clicar com o botão direito do mouse na região e selecionar **Explorar Eventos**.

   [![Explorar Eventos](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Uma grade dos dados brutos é exibida na região que está sendo explorada.

   [![Explorar eventos – exibição de dados de grade](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Seleciona e filtra conjuntos de dados

1. Edite seus termos para alterar os valores no gráfico. Adicione outro termo para fazer uma correlação cruzada entre diferentes tipos de valores.

   [![Adicionar um termo](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Deixe a caixa **Filtrar série** em branco para exibir todos os termos de pesquisa selecionados ou insira um termo de filtro na caixa **Filtrar série** para a filtragem improvisada da série.

   [![Filtrar série](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Para o guia de início rápido, insira **Station5** para fazer uma correlação cruzada entre a temperatura e a pressão dessa estação.

Depois de concluir o guia de início rápido, faça experiências com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="clean-up-resources"></a>Limpar os recursos

Agora que concluiu o tutorial, limpe os recursos que você criou:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), selecione **Todos os recursos** e localize o grupo de recursos do Azure Time Series Insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Excluir** ou remova cada recurso individualmente.

## <a name="next-steps"></a>Próximas etapas

* Você está pronto para criar seu ambiente do Azure Time Series Insights. Leia [Planejar o ambiente do Azure Time Series Insights](time-series-insights-environment-planning.md).
