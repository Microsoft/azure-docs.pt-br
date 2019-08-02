---
title: Como monitorar e reduzir a limitação e prevenir a latência no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como monitorar, diagnosticar e atenuar problemas de desempenho que causam latência e limitação no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 129476c833e596d40daa7081e23c0fd6d1b93b30
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165773"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorar e melhorar a limitação para reduzir a latência no Azure Time Series Insights

Quando a quantidade de dados de entrada excede a configuração do ambiente, você pode enfrentar latência ou limitação no Azure Time Series Insights.

É possível evitar a latência e a limitação configurando corretamente o ambiente para a quantidade de dados que você deseja analisar.

Provavelmente, você enfrentará latência e limitação quando:

- Adicionar uma origem de evento que contém os dados antigos que podem exceder a taxa de entrada alocada (o Time Series Insights precisará ser atualizado).
- Adicionar mais origens de evento em um ambiente, resultando em um pico de eventos adicionais (que poderá exceder a capacidade do ambiente).
- Enviar por push grandes quantidades de eventos históricos para uma origem de evento, resultando em um retardo (o Time Series Insights precisará ser atualizado).
- Unir os dados de referência com a telemetria, resultando em um tamanho de evento maior.  De uma perspectiva de limitação, um pacote de dados recebido com um tamanho de pacote igual a 32 KB é tratado como 32 eventos, cada um com um tamanho de 1 KB. O tamanho máximo permitido do evento é de 32 KB; pacotes de dados maiores que 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento de entrada de dados de séries temporais e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorar a latência e a limitação com alertas

Os alertas podem ajudá-lo a diagnosticar e atenuar os problemas de latência causados por seu ambiente.

1. No portal do Azure, selecione **métricas**.

   [![Métricas](media/environment-mitigate-latency/add-metrics.png)](media/environment-mitigate-latency/add-metrics.png#lightbox)

1. Selecione **Adicionar alerta de métrica**.  

   [![Adicionar alerta de métrica](media/environment-mitigate-latency/add-metric-alert.png)](media/environment-mitigate-latency/add-metric-alert.png#lightbox)

Nessa opção, você pode configurar alertas usando as seguintes métricas:

|Métrica  |DESCRIÇÃO  |
|---------|---------|
|**Bytes de Entrada Recebidos**     | Contagem de bytes brutos lidos de origens de evento. A contagem bruta geralmente inclui o nome e o valor da propriedade.  |  
|**Mensagens de Entrada Inválidas Recebidas**     | Contagem de mensagens inválidas lidas de todas as origens de evento dos Hubs de Eventos do Azure ou do Hub IoT do Azure.      |
|**Mensagens de Entrada Recebidas**   | Contagem de mensagens lidas de todas as origens de evento dos Hubs de Eventos ou Hubs IoT.        |
|**Bytes de Entrada Armazenados**     | Tamanho total de eventos armazenados e disponíveis para consulta. O tamanho é calculado apenas no valor da propriedade.        |
|**Eventos de entrada armazenados**     |   Contagem de eventos nivelados armazenados e disponíveis para consulta.      |
|**Tempo de atraso das mensagens de entrada recebidas**    |  Diferença em segundos entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada.      |
|**Atraso na contagem das mensagens de entrada recebidas**    |  Diferença entre o número de sequência da última mensagem enfileirada na partição da origem do evento e número de sequência da mensagem que está sendo processada na entrada.      |

![Latency](media/environment-mitigate-latency/latency.png)

* Se você está sendo limitado, você verá um valor para o *entrada de intervalo de tempo de mensagem recebida*, informando quantos segundos atrás de seu TSI é de tempo real, a mensagem atinge a origem do evento (excluindo o tempo de indexação de appx. 30-60 segundos).  *Retardo de contagem de mensagens recebidas de entrada* também deve ter um valor, permitindo que você determine a quantas mensagens atrás você está.  A maneira mais fácil de alcançar é aumentar a capacidade do seu ambiente para um tamanho que permitirá superar a diferença.  

  Por exemplo, se você tiver um ambiente S1 de unidade única e vê que há um retardo de 5.000.000 de mensagem, você pode aumentar o tamanho do seu ambiente para seis unidades por cerca de um dia para alcançar.  Você pode aumentar ainda mais para alcançar mais rapidamente. O período de atualização é uma ocorrência comum ao provisionar inicialmente um ambiente, especialmente ao se conectar a uma origem de evento que já tenha eventos nela ou quando você carrega em massa grandes quantidades de dados históricos.

* Outra técnica é definir um alerta **Eventos de Entrada Armazenados** >= um limite um pouco abaixo da capacidade total do ambiente por um período de 2 horas.  Esse alerta pode ajudá-lo a entender se você está constantemente na capacidade, o que indica uma alta probabilidade de latência. 

  Por exemplo, se você tiver três unidades S1 provisionadas (ou 2.100 eventos por capacidade de entrada por minuto), poderá definir um alerta **Eventos de Entrada Armazenados** para >= 1.900 eventos por 2 horas. Caso você esteja excedendo esse limite constantemente e, portanto, disparando o alerta, provavelmente, está com um provisionamento insuficiente.  

* Se você suspeitar que está sendo limitado, você pode comparar seu **mensagens de entrada recebidas** com seu evento origem as mensagens enviadas da.  Se a entrada no Hub de Eventos for maior que as **Mensagens de Entrada Recebidas**, provavelmente, o Time Series Insights está sendo limitado.

## <a name="improving-performance"></a>Melhorando o desempenho

Para reduzir a limitação ou a latência enfrentada, a melhor maneira de corrigir isso é aumentar a capacidade do ambiente.

É possível evitar a latência e a limitação configurando corretamente o ambiente para a quantidade de dados que você deseja analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, consulte [Dimensionar o ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter etapas de solução de problemas adicionais, consulte [Diagnosticar e solucionar problemas no ambiente do Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Para obter mais assistência, inicie uma conversa no [fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Também contate o [suporte do Azure](https://azure.microsoft.com/support/options/) para obter opções de suporte assistido.
