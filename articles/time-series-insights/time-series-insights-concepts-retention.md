---
title: Compreender a retenção de dados no ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve duas configurações que controlam a retenção de dados no ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dc192b1e8f5492cdfbfb434b5efb573182c51bb1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164573"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Compreender a retenção de dados no Azure Time Series Insights

Este artigo descreve duas configurações que impactam na retenção de dados em seu ambiente Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>O vídeo a seguir resume a retenção de dados de séries temporais e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada um dos ambientes Azure Time Series tem uma configuração que controla **tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou duração de retenção, o que vier primeiro.

Além disso, o seu ambiente Azure Time Series tem um **limite de armazenamento excedido comportamento** configuração. Ele controla a entrada e limpar o comportamento quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos de escolha ao configurá-lo:

- **Limpar dados antigos** (padrão)  
- **Pausar a entrada**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção está configurada para **Limpar dados antigos**. Essa configuração pode ser alternada conforme necessário, depois usando o portal do Azure, no momento da criação do **configurar** página do ambiente do Time Series Insights.

Para mais informações sobre como alternar entre os comportamentos de retenção, veja [Configuração da retenção no Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Compare o comportamento de retenção de dados:

## <a name="purge-old-data"></a>Limpar dados antigos

- Esse comportamento é o comportamento padrão para ambientes do Time Series Insights.  
- Esse comportamento é preferível quando os usuários querem ver sempre seus *dados mais recentes* em seu ambiente do Time Series Insights.
- Esse comportamento *limpa* dados uma vez que os limites do ambiente (tempo de retenção, tamanho ou contagem, o que ocorrer primeiro) forem atingidos. A retenção é definida como 30 dias por padrão.
- Os dados ingeridos mais antigos são apagados primeiro (abordagem PEPS).

### <a name="example-one"></a>Exemplo de um

Considere um ambiente de exemplo com o comportamento de retenção **Continuar entrada e limpar dados antigos**:

**Tempo de retenção de dados** é definido como 400 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total.   Suponha que os dados de entrada se acumulam até um volume de 500 MB por dia, em média. Considerando-se a taxa de dados de entrada, esse ambiente pode reter somente o equivalente a 60 dias de dados, já que a capacidade máxima é atingida após 60 dias. Os dados de entrada se acumulam assim: 500 MB cada dia x 60 dias = 30 GB.

No 61 º dia, o ambiente mostra os dados mais recentes, mas descarta os dados mais antigos, com mais de 60 dias. A limpeza abre espaço para os novos dados sendo transmitidos em entrada, para que novos dados possam continuar a serem explorados. Se o usuário deseja manter os dados por mais tempo, ele pode aumentar o tamanho do ambiente adicionando unidades adicionais ou reduzir o volume de dados enviados por push.  

### <a name="example-two"></a>Exemplo dois

Suponha que um ambiente também configurou o comportamento de retenção **Continuar entrada e limpar dados antigos**. Neste exemplo, o **Tempo de retenção de dados** é definido para um valor mais baixo, de 180 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados por 180 dias completos, a entrada diária não pode exceder 0,166 GB (166 MB).  

Sempre que a taxa diária de entrada desse ambiente excede 0,166 GB, os dados não podem ser armazenados por 180 dias, já que alguns dados sofrem limpeza. Considere esse mesmo ambiente durante um período ocupado. Suponha que a taxa de entrada do ambiente pode aumentar para uma média de 0,189 GB por dia. Nesse período ocupado, aproximadamente 158 dias de dados são retidos (30GB/0,189 = 158,73 dias de retenção). Esse tempo é menor que o período de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausar a entrada

- O **pausar entrada** configuração é projetada para garantir que dados não forem limpos se os limites de tamanho e contagem forem atingidos antes do seu período de retenção.  
- **Pausar entrada** fornece tempo adicional para os usuários para aumentar a capacidade de seu ambiente antes de dados são apagados devido à violação do período de retenção
- Ele ajuda a proteger você contra perda de dados, mas pode criar uma oportunidade para que a perda de dados mais recentes, se a entrada é colocada em pausa além do período de retenção da origem do evento.
- No entanto, depois que a capacidade máxima de um ambiente é atingida, o ambiente pausará a entrada de dados até que as seguintes ações adicionais ocorram:

   - Você aumentar a capacidade máxima do ambiente para adicionar mais unidades de escala, conforme descrito em [como dimensionar seu ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - O período de retenção de dados é atingido e os dados são limpos, colocando o ambiente abaixo de sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com o comportamento de retenção configurado para **Pausar entrada**. Neste exemplo, o **Período de retenção de dados** está configurado para 60 dias. **Capacidade** é definido como três (3) unidades do S1. Suponha que esse ambiente tem uma entrada de 2 GB de dados por dia. Nesse ambiente, a entrada é colocada em pausa quando a capacidade máxima é atingida.

Nesse momento, o ambiente mostra o mesmo conjunto de dados até retoma o ingresso ou até **continuar entrada** está habilitado (que limparia os dados mais antigos para liberar espaço para novos dados).

Quando a entrada é retomada:

- Os dados são transmitidos na ordem em que foram recebidos pela origem do evento
- Os eventos são indexados com base no respectivo carimbo de data/hora, a menos que você tenha excedido as políticas de retenção na origem do evento. Para obter mais informações sobre a configuração de retenção da origem do evento, veja [Perguntas frequentes dos Hubs de Eventos](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Você deve definir alertas para que forneçam aviso e ajudem a evitar que a entrada seja colocada em pausa. Há possibilidade de perda de dados, já que a retenção padrão é de 1 dia para origens do evento do Azure. Portanto, depois de entrada for colocada em pausa, você provavelmente perderá os dados mais recentes, a menos que uma ação adicional seja executada. Você deve aumentar a capacidade ou mudar o comportamento para **Limpar dados antigos** para evitar a possível perda de dados.

Nos hubs de eventos afetados, considere ajustar a propriedade **Retenção de Mensagem** para minimizar a perda de dados quando a entrada é colocada em pausa no Time Series Insights.

[![Retenção de mensagens do hub de eventos.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Se nenhuma propriedade está configurada na fonte de evento (`timeStampPropertyName`), o Time Series Insights assume como padrão o carimbo de hora de chegada no hub de eventos como o eixo x. Se `timeStampPropertyName` está configurado para ser algo, a ambiente procura configurado `timeStampPropertyName` no pacote de dados de eventos são analisados.

Se você precisar dimensionar o ambiente para acomodar a capacidade adicional ou para aumentar a duração da retenção, veja [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para obter mais informações.  

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como configurar ou alterar as configurações de retenção de dados, examine [Configurando a retenção no Time Series Insights](time-series-insights-how-to-configure-retention.md).
