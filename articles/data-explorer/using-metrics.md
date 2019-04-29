---
title: Monitorar o desempenho, integridade e uso com métricas do Data Explorer do Azure
description: Saiba como usar as métricas de Data Explorer do Azure para monitorar o desempenho, integridade e uso do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826759"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorar o desempenho, integridade e uso com métricas do Data Explorer do Azure

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Métricas do Data Explorer do Azure fornecem indicadores-chave sobre a integridade e o desempenho dos recursos do cluster. Use as métricas que são detalhadas neste artigo para monitorar a integridade do cluster Data Explorer do Azure e o desempenho no seu cenário específico como métricas de autônomo. Você também pode usar métricas como a base para operacionais [painéis do Azure](/azure/azure-portal/azure-portal-dashboards) e [alertas do Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).

* Criar uma [banco de dados e cluster](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>O uso de métricas

Em seu cluster do Gerenciador de dados do Azure, selecione **métricas** para abrir o painel de métricas e iniciar a análise em seu cluster.

![Selecione Métricas](media/using-metrics/select-metrics.png)

No painel de métricas:

![Painel de métricas](media/using-metrics/metrics-pane.png)

1. Para criar um gráfico de métricas, selecione **métrica** relevantes e o nome **agregação** por métrica conforme detalhado abaixo. O **Resource** e **métrica Namespace** seletores são previamente selecionadas para seu cluster do Gerenciador de dados do Azure.

    **Métrica** | **Unidade** | **Agregação** | **Descrição da métrica**
    |---|---|---|---|
    | Utilização do cache | Porcentagem | AVG, Max, Min | Porcentagem de recursos do cache alocada atualmente em uso pelo cluster. Cache refere-se ao tamanho do SSD alocado para atividade de usuário de acordo com a política de cache definida. Uma utilização média de cache de 80% ou menos é um estado sustentável para um cluster. Se o uso do cache média estiver acima de 80%, o cluster deve estar [escalado verticalmente](manage-cluster-scale-up.md) para um armazenamento com otimização de camada de preços ou [escalado horizontalmente](manage-cluster-scale-out.md) para mais instâncias. Como alternativa, se adapte a política de cache (menos dias no cache). Se a utilização do cache é mais de 100%, o tamanho dos dados a ser armazenado, de acordo com a política de cache for maior do que o tamanho total do cache no cluster. |
    | CPU | Porcentagem | AVG, Max, Min | Porcentagem de recursos de computação alocados atualmente em uso pelas máquinas no cluster. Uma média de CPU de 80% ou menos é sustentável para um cluster. O valor máximo da CPU é 100%, que significa que não há nenhum recurso de computação adicionais para processar dados. Quando um cluster não está sendo bem executado, verifique o valor máximo da CPU para determinar se há CPUs específicas que estão bloqueados. |
    | Eventos processados (para os Hubs de eventos) | Contagem | Max, Min, Sum | Número total de eventos de leitura dos hubs de eventos e processada pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceitos pelo mecanismo de cluster. |
    | Latência de ingestão | Segundos | AVG, Max, Min | Latência de dados ingeridos, desde o momento em que os dados foram recebidos do cluster até que ele esteja pronto para consulta. O período de latência de ingestão depende do cenário de ingestão. |
    | Resultados de ingestão | Contagem | Contagem | Número total de operações de ingestão que falhou e foi bem-sucedida. Use **aplicam-se a divisão** criar buckets de sucesso e falha de resultados e analisar as dimensões (**valor** > **Status**).|
    | Utilização de ingestão | Porcentagem | AVG, Max, Min | Porcentagem de recursos reais usados para ingestão de dados de recursos total alocados na política de capacidade para realizar a ingestão. A política de capacidade padrão é não mais do que 512 operações de ingestão simultâneas ou 75% dos recursos do cluster investidos na ingestão. Ingestão de média de utilização de 80% ou menos é um estado sustentável para um cluster. Valor máximo de utilização de ingestão é 100%, o que significa que todas as capacidade de ingestão de cluster é usada e uma fila de ingestão pode resultar. |
    | Volume de ingestão (em MB) | Contagem | Max, Min, Sum | O tamanho total dos dados ingeridos no cluster (em MB) antes da compactação. |
    | Keep alive | Contagem | Méd. | Controla a capacidade de resposta do cluster. Um cluster totalmente dinâmico retorna o valor 1 e um cluster desconectado ou bloqueado retornará 0. |
    | Duração da consulta | Segundos | Count, Avg, Min, Max, Sum | Total de tempo até que os resultados da consulta são recebidos (não inclui a latência de rede). |
    | | | |

    Informações adicionais sobre [métricas de cluster do Gerenciador de dados do Azure com suporte](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selecione o **adicionar métrica** botão para ver várias métricas no gráfico no mesmo gráfico.
3. Selecione o **+ novo gráfico** botão para ver vários gráficos em uma exibição.
4. Use o seletor de tempo para alterar o intervalo de tempo (padrão: últimas 24 horas).
5. Use [ **Adicionar filtro** e **se aplicam a divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas com dimensões.
6. Selecione **fixar no painel** para adicionar sua configuração de gráfico para os painéis de controle para que você pode exibi-lo novamente.
7. Definir **nova regra de alerta** para visualizar suas métricas usando os critérios definidos. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. Modificar essas configurações na [painel de criação de regra de alerta](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informações adicionais sobre como usar o [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: consultar dados no Azure Data Explorer](web-query-data.md)
