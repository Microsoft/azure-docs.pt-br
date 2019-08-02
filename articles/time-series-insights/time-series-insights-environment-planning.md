---
title: Planejar a escala do ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como seguir as práticas recomendadas ao planejar um ambiente Azure Time Series Insights. Áreas que são abordadas incluem a capacidade de armazenamento, retenção de dados, capacidade de entrada, monitoramento e negócios continuidade e recuperação de desastres (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 2c11e3f623817894cea801173239cc386c6c3313
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165829"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planejar seu ambiente de GA de Insights de série de tempo do Azure

Este artigo descreve como planejar seu ambiente de disponibilidade geral (GA) do Azure Time Series Insights com base na taxa de entrada esperada e os requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Assista a este vídeo para saber mais sobre retenção de dados no Azure Time Series Insights e como planejá-la**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Práticas recomendadas

Para começar com o Time Series Insights, é melhor se você souber quantos dados você pretende enviar por push por minuto e o quanto você precisa armazenar seus dados.  

Para saber mais sobre capacidade e retenção para ambas as SKUs do Time Series Insights, confira [Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para melhor planejar seu ambiente do Time Series Insights para o sucesso a longo prazo, considere os seguintes atributos:

- <a href="#storage-capacity">Capacidade de armazenamento</a>
- <a href="#data-retention">Período de retenção de dados</a>
- <a href="#ingress-capacity">Capacidade de entrada</a>
- <a href="#shape-your-events">Formação de seus eventos</a>
- <a href="#ensure-that-you-have-reference-data">Garantindo que você tenha dados de referência em vigor</a>

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por padrão, o Time Series Insights retém dados com base na quantidade de armazenamento que você provisiona (unidades de &#215; a quantidade de armazenamento por unidade) e a entrada.

## <a name="data-retention"></a>Retenção de dados

Você pode alterar o **tempo de retenção de dados** configuração no ambiente do Time Series Insights. Você pode habilitar até 400 dias de retenção. 

Time Series Insights tem dois modos. Um modo otimiza para garantir que seu ambiente tenha os dados mais atualizados. Esse modo é, por padrão. 

Outro modo otimiza para garantir que os limites de retenção são atendidos. No modo de segundo, a entrada é pausada se a capacidade de armazenamento geral do ambiente for atendida. 

Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

Você pode configurar no máximo 400 dias de retenção de dados no ambiente do Time Series Insights.

### <a name="configure-data-retention"></a>Configurar retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

1. No **ambiente Time Series Insights** painel, em **configurações**, selecione **configurar**.

1. No **tempo de retenção de dados (em dias)** , digite um valor entre 1 e 400.

   [![Configurar retenção](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política de retenção de dados apropriado, consulte [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacidade de entrada

A segunda área se concentrar para planejar seu ambiente do Time Series Insights é a capacidade de entrada. Capacidade de entrada é um derivativo de alocação por minuto.

De uma perspectiva de limitação, um pacote de dados recebido com um tamanho de pacote de 32 KB é tratado como 32 eventos, cada 1 KB de tamanho. O tamanho máximo permitido é de 32 KB. Pacotes de dados maiores que 32 KB são truncados.

A tabela a seguir resume a capacidade de entrada por unidade para cada SKU do Time Series Insights:

|SKU  |Contagem de eventos por mês  |Tamanho do evento por mês  |Contagem de eventos por minuto  |Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milhões    |   300 GB   | 7\.200   | 7\.200 KB  |

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Você não pode migrar de um ambiente S1 para um S2. Você não pode migrar de um ambiente S2 para um S1.

Para capacidade de entrada, primeiro determine a entrada total que necessária em uma base por mês. Em seguida, determine quais seu por minuto precisa são. 

Limitação e latência desempenham uma função na capacidade por minuto. Se você tiver um pico na entrada de dados que dura menos de 24 horas, Time Series Insights pode "atualizado" com uma taxa de entrada de duas vezes as taxas listadas na tabela anterior.

Por exemplo, não se você tiver um único SKU S1, seus dados de entrada a uma taxa de 720 eventos por minuto, e a taxa de dados picos para menos de uma hora a uma taxa de 1.440 eventos ou menos, há nenhuma latência perceptível em seu ambiente. No entanto, se você exceder 1.440 eventos por minuto por mais de uma hora, você provavelmente terá latência nos dados visualizados e disponíveis para consulta em seu ambiente.

Você pode não saber com antecedência a quantidade de dados que você pretende enviar por push. Nesse caso, você pode encontrar telemetria de dados do [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Hubs de eventos](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) na sua assinatura do portal do Azure. A telemetria pode ajudar você a determinar como provisionar o seu ambiente. Use o **métricas** painel no portal do Azure para a origem do respectivo evento exibir sua telemetria. Se você compreender suas métricas de origem de eventos, poderá planejar e provisionar com mais eficiência o ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

Para calcular os requisitos de sua entrada:

- Verificar se sua capacidade de entrada está acima de sua taxa média de por minuto e se seu ambiente é grande o suficiente para lidar com a entrada antecipada equivalente a duas vezes sua capacidade para menos de uma hora.

- Se ocorrerem picos de entrada usado pela última vez por mais de 1 hora, a taxa de pico como média. Provisione um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar a limitação e a latência, confira [Reduzir a latência e a limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Formatar os eventos

É importante garantir que a maneira como você envia eventos para o Time Series Insights dá suporte ao tamanho do ambiente você está Provisionando. (Por outro lado, você pode mapear o tamanho do ambiente a quantos eventos o Time Series Insights lê e o tamanho de cada evento.) Também é importante pensar sobre os atributos que você talvez queira usar para dividir e filtrar ao consultar seus dados.

> [!TIP]
> Examine o documentação de formação de JSON [enviando eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Certifique-se de que você tenha dados de referência

Um *conjunto de dados de referência* é uma coleção de itens que aumentam os eventos da origem do evento. O mecanismo de entrada de análise de séries temporais une cada evento da fonte de evento com a linha de dados correspondente em seu conjunto de dados de referência. O evento aumentado, em seguida, está disponível para consulta. A junção é baseada na **Primary Key** colunas que são definidas em seu conjunto de dados de referência.

> [!NOTE]
> Dados de referência não Unidos retroativamente. Somente os dados de entrada atuais e futuros é correspondidos e associados ao conjunto de dados de referência depois que ele é configurado e carregado. Se você planeja enviar uma grande quantidade de dados históricos para análise de séries temporais e não o primeiro carregamento ou criar dados de referência no Time Series Insights, você terá que refazer seu trabalho (Dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerenciar seus dados de referência no Time Series Insights, consulte nosso [documentação do conjunto de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Introdução à criação [um novo ambiente do Time Series Insights no portal do Azure](time-series-insights-get-started.md).

- Saiba como [adicionar uma origem de evento dos Hubs de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) para análise de séries Temporais.

- Leia sobre como [configurar uma origem de evento do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
