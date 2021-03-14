---
title: Azure Monitor para Data Explorer do Azure (versão prévia) | Microsoft Docs
description: Este artigo descreve Azure Monitor informações para clusters de Data Explorer do Azure.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: dcfe12b30e336863c8e112d9ad675a2f57fe48f4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179129"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor para Data Explorer do Azure (versão prévia)

Azure Monitor para o Data Explorer do Azure (versão prévia) fornece monitoramento abrangente de seus clusters, fornecendo uma exibição unificada do desempenho, das operações, do uso e das falhas do cluster.
Este artigo o ajudará a entender como integrar e usar Azure Monitor para o Azure Data Explorer (versão prévia).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Introdução ao Azure Monitor para Data Explorer do Azure (versão prévia)

Antes de saltar para a experiência, você deve entender como ela apresenta e visualiza informações.
-    **Em perspectiva de escala** mostrando uma exibição de instantâneo de métricas principais de clusters, para rastrear facilmente o desempenho de consultas, ingestão e operações de exportação.
-   **Análise de busca detalhada** de um cluster específico do Azure data Explorer para ajudar a executar uma análise detalhada.
-    **Personalizável** , onde você pode alterar quais métricas deseja ver, modificar ou definir limites que se alinham com seus limites e salvar suas próprias pastas de trabalho personalizadas. Os gráficos na pasta de trabalho podem ser fixados a painéis do Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Exibir de Azure Monitor (em perspectiva de escala)

Em Azure Monitor, você pode exibir as principais métricas de desempenho para o cluster, incluindo métricas para consultas, ingestão e operações de exportação de vários clusters em sua assinatura e ajudar a identificar problemas de desempenho.

Para exibir o desempenho de seus clusters em todas as suas assinaturas, execute as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Selecione **Monitor** no painel esquerdo na portal do Azure e, na seção Hub do insights, selecione **clusters de data Explorer do Azure (versão prévia)**.

![Captura de tela da experiência de visão geral com vários grafos](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Guia Visão Geral

Na guia **visão geral** da assinatura selecionada, a tabela exibe métricas interativas para os clusters de data Explorer do Azure agrupados na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* Assinaturas – somente as assinaturas que têm clusters do Azure Data Explorer são listadas.

* Clusters do Azure Data Explorer – por padrão, apenas até cinco clusters são previamente selecionados. Se você selecionar todos ou vários clusters no seletor de escopo, até 200 clusters serão retornados.

* Intervalo de tempo - por padrão, exibe as últimas 24 horas de informações com base nas seleções correspondentes.

O bloco do contador, na lista suspensa, acumula o número total de clusters de Data Explorer do Azure nas assinaturas selecionadas e reflete quantos são selecionados. Há codificações de cores condicionais para as colunas: Keep Alive, CPU, utilização de ingestão e utilização de cache. As células codificadas em laranja têm valores que não são sustentáveis para o cluster. 

Para entender melhor o que cada uma das métricas representam, é recomendável ler a documentação sobre as [métricas de data Explorer do Azure](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Guia desempenho da consulta

Essa guia mostra a duração da consulta, o número total de consultas simultâneas e o número total de consultas limitadas.

![Captura de tela da guia desempenho de consulta](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Guia desempenho de ingestão

Essa guia mostra a latência de ingestão, resultados de ingestão com êxito, resultados de ingestão com falha, volume de ingestão e eventos processados para hubs de evento/IoT.

[![Captura de tela da guia desempenho de ingestão](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Guia desempenho de ingestão de streaming

Esta guia fornece informações sobre a taxa média de dados, a duração média e a taxa de solicitação.

### <a name="export-performance-tab"></a>Guia desempenho de exportação

Esta guia fornece informações sobre registros exportados, atraso, contagem pendente e porcentagem de utilização para operações de exportação contínuas.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Exibir de um recurso de cluster Data Explorer do Azure (análise de busca detalhada)

Para acessar Azure Monitor para clusters de Data Explorer do Azure diretamente de um cluster de Data Explorer do Azure:

1. No portal do Azure, selecione **clusters de data Explorer do Azure**.

2. Na lista, escolha um cluster de Data Explorer do Azure. Na seção monitoramento, escolha **insights (versão prévia)**.

Essas exibições também podem ser acessadas selecionando o nome do recurso de um cluster de Data Explorer do Azure de dentro da exibição do Azure Monitor insights.

Azure Monitor para o Azure Data Explorer combina logs e métricas para fornecer uma solução de monitoramento global. A inclusão de visualizações baseadas em logs requer que os usuários [habilitem o log de diagnóstico de seus data Explorer cluster do Azure e os enviem a um espaço de trabalho log Analytics.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Os logs de diagnóstico que devem ser habilitados são: **comando**, **consulta**, **TableDetails** e **TableUsageStatistics**.

![Captura de tela do botão azul que exibe o texto "Habilitar logs para monitoramento"](./media/data-explorer/enable-logs.png)


 A guia **visão geral** mostra:

- Blocos de métricas destacando a disponibilidade e o status geral do cluster para avaliar rapidamente sua integridade.

- Um resumo das [recomendações do supervisor](/azure/data-explorer/azure-advisor) ativo e do status de [integridade do recurso](/azure/data-explorer/monitor-with-resource-health) .

- Gráficos mostrando os principais consumidores de CPU e memória e o número de usuários exclusivos ao longo do tempo.


[![Captura de tela da exibição de um recurso de cluster Data Explorer do Azure](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

A guia **métricas-chave** mostra uma exibição unificada de algumas das métricas do cluster, agrupadas por: métricas gerais, relacionadas à consulta, relacionadas à ingestão e métricas relacionadas à ingestão de streaming.

[![Captura de tela da exibição de falhas](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

A guia **uso** permite aos usuários aprofundar-se no desempenho dos comandos e consultas do cluster. Nesta página, você pode:
 
 - Veja quais grupos de carga de trabalho, usuários e aplicativos estão enviando a maioria das consultas ou consumindo a maior parte da CPU e memória (para que você possa entender quais cargas de trabalho estão enviando as consultas mais pesadas para o cluster processar).
 - Identifique os principais grupos de carga de trabalho, usuários e aplicativos por consultas com falha.
 - Identifique alterações recentes no número de consultas, em comparação com a média diária histórica (nos últimos 16 dias), por grupo de carga de trabalho, usuário e aplicativo.
 - Identifique tendências e picos no número de consultas, memória e consumo de CPU por grupo de carga de trabalho, usuário, aplicativo e tipo de comando.

[![Captura de tela do modo de exibição de operações com gráficos de rosca do aplicativo principal por contagem de comando e consulta, principais principais por contagem de comando e consulta e comandos principais por tipos de comando](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Captura de tela do modo de exibição de operações com gráficos de linhas de contagem de consultas por aplicativo, memória total por aplicativo e total de CPU por aplicativo](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

A guia **tabelas** mostra as propriedades históricas e mais recentes das tabelas no cluster. Você pode ver quais tabelas estão consumindo mais espaço, acompanhar o histórico de crescimento por tamanho de tabela, dados ativos e o número de linhas ao longo do tempo.

A guia **cache** permite que os usuários analisem os padrões de pesquisa de suas consultas reais e os comparem com a política de cache configurada (para cada tabela). Você pode identificar as tabelas usadas pela maioria das consultas e tabelas que não são consultadas e adaptar a política de cache de forma adequada. Você pode obter recomendações específicas de política de cache sobre tabelas específicas no Azure Advisor (atualmente, as recomendações de cache estão disponíveis apenas no [painel principal do assistente do Azure](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), com base nas consultas reais, de volta nos últimos 30 dias e uma política de cache não otimizada para pelo menos 95% das consultas. As recomendações de redução de cache no Azure Advisor estão disponíveis para clusters que são "limitados por dados" (o que significa que o cluster tem baixa CPU e baixa utilização de ingestão, mas devido à alta capacidade de dados, o cluster não pôde reduzir ou reduzir horizontalmente).

[![Captura de tela de detalhes do cache](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Fixar no painel do Azure

Você pode fixar qualquer uma das seções de métrica (da perspectiva em escala) a um painel do Azure selecionando o ícone de pino na parte superior direita da seção.

![Captura de tela do ícone de fixação selecionado](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Personalizar Azure Monitor para o cluster de Data Explorer do Azure

Esta seção destaca os cenários comuns para editar a pasta de trabalho a fim de personalizar o suporte às necessidades de análise de dados:
* Escopo a pasta de trabalho para sempre selecionar uma assinatura específica ou cluster (s) de Data Explorer do Azure
* Alterar as métricas na grade
* Alterar limites ou renderização/codificação de cores

Você pode iniciar as personalizações habilitando o modo de edição e selecionando o botão **Personalizar** na barra de ferramentas superior.

![Captura de tela do botão Personalizar](./media/data-explorer/customize.png)

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão em nossa pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, seja na seção Meus Relatórios, que é particular a você, ou na seção Relatórios Compartilhados, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Captura de tela da galeria da pasta de trabalho](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes gerais de solução de problemas, consulte o [artigo de solução de problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar o Azure Monitor para o cluster de Data Explorer do Azure (versão prévia). Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não posso ver todas as minhas assinaturas no seletor de assinatura?

Mostramos apenas assinaturas que contêm clusters Data Explorer do Azure, escolhidas no filtro de assinatura selecionado, que são selecionadas na "diretório + assinatura" no cabeçalho portal do Azure.

![Captura de tela do filtro de assinatura](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Por que não vejo nenhum dado para meu cluster de Data Explorer do Azure nas seções uso, tabelas ou cache?

Para exibir os dados baseados em logs, você precisará [habilitar os logs de diagnóstico](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) para cada um dos clusters data Explorer do Azure que deseja monitorar. Isso pode ser feito nas configurações de diagnóstico para cada cluster. Você precisará enviar seus dados para um espaço de trabalho Log Analytics. Os logs de diagnóstico que devem ser habilitados são: comando, consulta, TableDetails e TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Já habilitei os logs para meu cluster de Data Explorer do Azure, por que ainda não consigo ver meus dados em comandos e consultas?

Atualmente, os logs de diagnóstico não funcionam retroativamente, portanto, os dados só começarão a aparecer uma vez que houve ações executadas para o Data Explorer do Azure. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo de como o cluster de Data Explorer do Azure está ativo.


## <a name="next-steps"></a>Próximas etapas

Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../visualize/workbooks-overview.md).
