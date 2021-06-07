---
title: Planejar seu ambiente Gen1-Azure Time Series Insights | Microsoft Docs
description: Práticas recomendadas para preparar, configurar e implantar seu ambiente de Azure Time Series Insights Gen1.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016200"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Planejar seu ambiente de Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve como planejar seu ambiente de Azure Time Series Insights Gen1 com base em sua taxa de entrada esperada e seus requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Assista a este vídeo para saber mais sobre a retenção de dados em Azure Time Series insights e como planejar para ele**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Práticas recomendadas

Para começar a usar o Azure Time Series Insights, é melhor se você souber a quantidade de dados que espera enviar por minuto e por quanto tempo precisa para armazenar seus dados.  

Para obter mais informações sobre capacidade e retenção para os Azure Time Series Insights SKUs, leia [Azure Time Series insights preços](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para planejar melhor seu ambiente de Azure Time Series Insights para o sucesso a longo prazo, considere os seguintes atributos:

- [Capacidade de armazenamento](#storage-capacity)
- [Período de retenção de dados](#data-retention)
- [Capacidade de entrada](#ingress-capacity)
- [Formação de seus eventos](#shape-your-events)
- [Garantindo que você tenha dados de referência em vigor](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por padrão, Azure Time Series Insights retém dados com base na quantidade de armazenamento que você provisiona (unidades &#215; quantidade de armazenamento por unidade) e entrada.

## <a name="data-retention"></a>Retenção de dados

Você pode alterar a configuração de **tempo de retenção de dados** em seu ambiente de Azure Time Series insights. Você pode habilitar até 400 dias de retenção.

Azure Time Series Insights tem dois modos:

- Um modo otimiza para os dados mais atualizados. Ele impõe uma política para **limpar dados antigos** , deixando dados recentes disponíveis com a instância. Por padrão, esse modo é ativado.
- O outro otimiza os dados para permanecerem abaixo dos limites de retenção configurados. **Pausar a entrada** impede que novos dados sejam inseridos quando é selecionado como o **limite de armazenamento excedeu o comportamento**.

Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

> [!IMPORTANT]
> Você pode configurar um máximo de 400 dias de retenção de dados em seu ambiente de Azure Time Series Insights Gen1.

### <a name="configure-data-retention"></a>Configurar a retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

1. No painel **Time Series insights ambiente** , em **configurações**, selecione **configuração de armazenamento**.

1. Na caixa **tempo de retenção de dados (em dias)** , insira um valor entre 1 e 400.

   [![Configurar retenção](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política de retenção de dados apropriada, leia [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacidade de entrada

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Planejamento de ambiente

A segunda área para se concentrar para planejar seu ambiente de Azure Time Series Insights é a capacidade de entrada. O armazenamento de entrada diário e a capacidade do evento são medidos por minuto, em blocos de 1 KB. O tamanho máximo de pacote permitido é de 32 KB. Pacotes de dados maiores que 32 KB são truncados.

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Não é possível migrar de um ambiente S1 para um S2. Não é possível migrar de um ambiente S2 para um S1.

Para a capacidade de entrada, primeiro determine a entrada total que você precisa por mês. Em seguida, determine quais são suas necessidades por minuto.

A limitação e a latência desempenham uma função na capacidade por minuto. Se você tiver um pico em sua entrada de dados que dura menos de 24 horas, Azure Time Series Insights poderá "se acumular" em uma taxa de entrada de duas vezes as tarifas listadas na tabela anterior.

Por exemplo, se você tiver uma única SKU S1, você entrará dados em uma taxa de 720 eventos por minuto e os picos de taxa de dados por menos de uma hora a uma taxa de 1.440 eventos ou menos, não haverá latência perceptível em seu ambiente. No entanto, se você exceder 1.440 eventos por minuto por mais de uma hora, provavelmente haverá latência nos dados visualizados e disponíveis para consulta em seu ambiente.

Talvez você não saiba com antecedência a quantidade de dados que espera enviar por push. Nesse caso, você pode encontrar a telemetria de dados para o [Hub IOT do Azure](../iot-hub/monitor-iot-hub.md) e [hubs de eventos do Azure](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) em sua assinatura portal do Azure. A telemetria pode ajudá-lo a determinar como provisionar seu ambiente. Use o painel **métricas** na portal do Azure da respectiva origem do evento para exibir sua telemetria. Se você entender suas métricas de origem de evento, poderá planejar e provisionar com mais eficiência seu ambiente de Azure Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

Para calcular seus requisitos de entrada:

- Verifique se a sua capacidade de entrada está acima da taxa média por minuto e se o ambiente é grande o suficiente para lidar com a sua entrada prevista equivalente a duas vezes sua capacidade por menos de uma hora.

- Se ocorrerem picos de entrada que duram mais de uma hora, use a taxa de pico como média. Provisione um ambiente com a capacidade de lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar limitação e latência, leia [reduzir a latência e a limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Formatar os eventos

É importante garantir que a maneira como você envia eventos para Azure Time Series Insights dá suporte ao tamanho do ambiente que você está Provisionando. (Por outro lado, você pode mapear o tamanho do ambiente para quantos eventos Azure Time Series Insights leituras e o tamanho de cada evento.) Também é importante pensar sobre os atributos que você pode querer usar para dividir e filtrar por quando você consulta seus dados.

> [!TIP]
> Examine a documentação do JSON Shaping em [enviando eventos](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Verifique se você tem dados de referência

Um *conjunto* de uma referência é uma coleção de itens que aumentam os eventos da origem do evento. O Azure Time Series Insights o mecanismo de entrada une cada evento de sua origem de evento com a linha de dados correspondente no DataSet de referência. O evento aumentado fica disponível para consulta. A junção é baseada nas colunas de **chave primária** que são definidas em seu conjunto de seus conjuntos de referência.

> [!NOTE]
> Os dados de referência não são associados retroativamente. Somente os dados de entrada atuais e futuros são correspondidos e associados ao DataSet de referência depois de configurados e carregados. Se você planeja enviar uma grande quantidade de dados históricos para Azure Time Series Insights e não carregar primeiro ou criar dados de referência no Azure Time Series Insights, talvez seja necessário refazer seu trabalho (dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerenciar seus dados de referência em Azure Time Series Insights, leia nossa [documentação de conjunto](time-series-insights-add-reference-data-set.md)de dado de referência.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Comece criando [um novo ambiente de Azure Time Series insights no portal do Azure](time-series-insights-get-started.md).

- Saiba como [Adicionar uma fonte de eventos de hubs de eventos](./how-to-ingest-data-event-hub.md) a Azure Time Series insights.

- Leia sobre como [Configurar uma origem de evento do Hub IOT](./how-to-ingest-data-iot-hub.md).