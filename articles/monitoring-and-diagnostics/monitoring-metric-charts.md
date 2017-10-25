---
title: Azure Monitor Metrics Explorer | Microsoft Docs
description: Saiba mais sobre os novos recursos do Azure Monitor Metrics Explorer
author: vgorbenko
manager: Victor.Mushkatin
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.openlocfilehash: 537dd6d64fe49093dd73d8040cde5a9153a7bd5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Metrics Explorer

Estas instruções descrevem a experiência de criação de gráficos de Métricas da próxima geração do Azure Monitor que atualmente estão em visualização pública. A nova experiência dá suporte a renderização de gráficos em métricas multidimensionais e métricas básicas sem dimensões. Plote gráficos que sobrepõem métricas de diferentes tipos de recursos, vários grupos de recursos e assinaturas. Os gráficos de métricas multidimensionais podem ser personalizados pela aplicação de filtros de dimensão, bem como de agrupamento. Qualquer gráfico, incluindo gráficos personalizados, pode ser fixado em painéis.

Se estiver buscando informações sobre a experiência antiga que dá suporte apenas às métricas básicas sem dimensões, consulte a seção intitulada “Acessar métricas por meio do portal” no [guia de Visão geral de métricas do Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>O que é o Azure Monitor Metrics Explorer?

O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. O Metrics Explorer é um ponto de partida essencial para investigar vários problemas de desempenho e disponibilidade com seus aplicativos e sua infraestrutura hospedados no Azure ou monitorados pelos serviços do Azure Monitor. 

## <a name="what-are-metrics-in-azure"></a>O que são métricas no Azure?

Métricas no Microsoft Azure são a série de valores medidos e as contagens coletadas e armazenadas ao longo do tempo. Há métricas padrão (ou da “plataforma”) e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de uso e integridade dos recursos do Azure. Por outro lado, as métricas personalizadas são enviadas para o Azure por meio de seus aplicativos usando a [API do Application Insights para eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). As métricas personalizadas são armazenadas nos recursos do Application Insights junto com outras métricas específicas ao aplicativo.

## <a name="what-are-multi-dimensional-metrics"></a>O que são métricas multidimensionais?

Muitos dos recursos do Azure agora expõem métricas multidimensionais. Essas métricas controlam várias séries de valores para uma ou muitas dimensões nomeadas. Por exemplo, uma métrica “Espaço em disco disponível” pode ter uma dimensão chamada “Unidade” com valores “C:”, “D:”, que permitirá exibir o espaço em disco disponível em todas as unidades ou para cada unidade individualmente. 

O exemplo abaixo ilustra dois conjuntos de dados de uma métrica hipotética chamada “Taxa de Transferência de Rede”. O primeiro conjunto de dados não tem nenhuma dimensão. O segundo conjunto de dados mostra os valores com duas dimensões, “Endereço IP” e “Direção”:

### <a name="network-throughput"></a>Taxa de Transferência de Rede
(Essa métrica não tem dimensões)

 |Timestamp        | Valor da Métrica | 
   | ------------- |:-------------| 
   | 9/8/2017 8h14 | 1.331,8 Kbps | 
   | 9/8/2017 8h15 | 1.141,4 Kbps |
   | 9/8/2017 8h16 | 1.110,2 Kbps |

Essa métrica não dimensional pode responder apenas a uma pergunta básica, como “qual era minha taxa de transferência de rede em determinado horário?”

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Taxa de Transferência de Rede + duas dimensões (“IP” e “Direção”)

| Timestamp          | Dimensão “IP” | Dimensão “Direção” | Valor da Métrica| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Send"    | 646,5 Kbps |
   | 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Receive" | 420,1 Kbps |
   | 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 Kbps | 
   | 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 Kbps |
   | 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Send"    | 515,2 Kbps |
   | 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Receive" | 371,1 Kbps |
   | 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 Kbps |
   | 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 Kbps |

Essa métrica pode responder a perguntas como “qual era a taxa de transferência de rede para cada endereço IP?” e “quantos dados foram enviados vs. recebidos?” As métricas multidimensionais trazem um valor analítico e de diagnóstico adicional em comparação às métricas não dimensionais. 

## <a name="how-do-i-create-a-new-chart"></a>Como fazer para criar um novo gráfico?

   > [!NOTE]
   > Alguns dos recursos da experiência antiga de Métricas ainda não estão disponíveis no novo Metrics Explorer. Durante a versão prévia da nova experiência, você pode continuar usando a exibição antiga de Métricas (não dimensionais) do Azure Monitor. 

1. Abrir o portal do Azure
2. Navegue para a nova guia **Monitor** e, em seguida, selecione **Métricas (versão prévia)**.

   ![Imagem de Versão Prévia de Métricas](./media/monitoring-metric-charts/001.png)

3. O **seletor de métrica** será aberto automaticamente para você. Escolha um recurso na lista para exibir suas métricas associadas. Apenas os recursos com métricas são mostrados na lista.

   ![Imagem de Versão Prévia de Métricas](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Caso você tenha mais de uma assinatura do Azure, o Metrics Explorer extrairá os recursos de todas as assinaturas que estão selecionadas em Configurações do Portal -> Filtrar por lista de assinaturas. Para alterar isso, clique no ícone de engrenagem das configurações do Portal na parte superior da tela e selecione quais assinatura você deseja usar.

4. Para alguns tipos de recurso (ou seja, Contas de Armazenamento e Máquinas Virtuais), antes de selecionar uma métrica, você deve escolher um **Subserviço**. Cada subserviço traz seu próprio conjunto de métricas relevantes apenas para esse subserviço e não para outros subserviços.

   Por exemplo, cada Armazenamento do Azure tem métricas dos subserviços “Blobs”, “Arquivos”, “Filas” e “Tabelas”, que fazem parte da conta de armazenamento. No entanto, a métrica “Contagem de Mensagens da Fila” é naturalmente aplicável ao subserviço “Fila” e não a outros subserviços da conta de armazenamento.

   ![Imagem de Versão Prévia de Métricas](./media/monitoring-metric-charts/003.png)

5. Selecione uma métrica na lista. Se souber um nome parcial da métrica desejada, comece a digitá-lo para ver uma lista filtrada das métricas disponíveis:

   ![Imagem de Versão Prévia de Métricas](./media/monitoring-metric-charts/004.png)

6. Depois de selecionar uma métrica, o gráfico será renderizado com a agregação padrão da métrica selecionada. Neste ponto, basta clicar fora do **seletor de métricas** para fechá-lo. Você também pode alternar o gráfico para uma agregação diferente. Para algumas métricas, a alternação da agregação permite escolher qual valor você deseja ver no gráfico. Por exemplo, alterne entre os valores de média, mínimo e máximo. 

7. Clicando no ícone Adicionar Métrica ![ícone de métrica](./media/monitoring-metric-charts/icon001.png) e repetindo as etapas 3 a 6, você pode adicionar mais métricas ao mesmo gráfico.

   > [!NOTE]
   > Normalmente, você não deseja ter métricas com diferentes unidades de medida (ou seja, “milissegundos” e “quilobytes”) ou com uma escala consideravelmente diferente em um gráfico. Em vez disso, considere o uso de vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no Metrics Explorer.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Como fazer para aplicar filtros aos gráficos?

Aplique filtros aos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica “Contagem de transações” tiver uma dimensão, “Tipo de resposta”, que indica se a resposta das transações foi bem-sucedida ou falhou, a filtragem nessa dimensão plotará uma linha de gráfico apenas para as transações bem-sucedidas (ou apenas para aquelas com falha). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro:

1. Clique no ícone Adicionar Filtro ![ícone de filtro](./media/monitoring-metric-charts/icon002.png) acima do gráfico

2. Selecione qual dimensão (propriedade) você deseja filtrar

   ![imagem de métrica](./media/monitoring-metric-charts/006.png)

3. Selecione quais valores de dimensão você deseja incluir ao plotar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![imagem de métrica](./media/monitoring-metric-charts/007.png)

4. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![imagem de métrica](./media/monitoring-metric-charts/008.png)

5. Repita as etapas 1 a 4 para aplicar vários filtros aos mesmos gráficos.

## <a name="how-do-i-segment-a-chart"></a>Como fazer para segmentar um gráfico?

Divida uma métrica pela dimensão para visualizar uma comparação de diferentes segmentos da métrica em relação um ao outro e identificar os segmentos distantes de uma dimensão. 

### <a name="to-segment-a-chart"></a>Para segmentar um gráfico:

1. Clique no ícone Adicionar Agrupamento  ![imagem de métrica](./media/monitoring-metric-charts/icon003.png) acima do gráfico.
 
   > [!NOTE]
   > Você pode ter vários filtros, mas apenas um agrupamento em um único gráfico.

2. Escolha uma dimensão na qual você deseja segmentar o gráfico: 

   ![imagem de métrica](./media/monitoring-metric-charts/010.png)

   Agora o gráfico mostra várias linhas, uma para cada segmento de dimensão:

   ![imagem de métrica](./media/monitoring-metric-charts/012.png)

3. Clique fora do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Use a Filtragem e o Agrupamento na mesma dimensão para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura dos gráficos.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Como fazer para fixar gráficos nos painéis?

Depois de configurar os gráficos, talvez você deseje adicioná-los aos painéis, de modo que você possa exibi-los novamente, possivelmente, no contexto de outra telemetria de monitoramento ou compartilhá-los com sua equipe. 

Para fixar um gráfico configurado em um painel:

Depois de configurar o gráfico, clique no menu **Ações do Gráfico** no canto superior direito do gráfico e clique em **Fixar no painel**.

   ![imagem de métrica](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Próximas etapas

  Leia [Criar painéis personalizados de KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para a criação de painéis acionáveis com métricas.