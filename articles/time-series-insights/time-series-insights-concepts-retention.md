---
title: Entender a retenção de dados em seu ambiente – Azure Time Series Insight | Microsoft Docs
description: Este artigo descreve duas configurações que controlam a retenção de dados no ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 4f236679d0662df852581a6a8408ed6bc0d4e3fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535682"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Entender a retenção de dados no Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve duas configurações principais que afetam a retenção de dados em seu ambiente de Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>O vídeo a seguir resume Azure Time Series Insights retenção de dados e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada um de seus ambientes de Azure Time Series Insights tem uma configuração que controla o **tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção, o que vier primeiro.

Além disso, seu ambiente de Azure Time Series Insights tem uma configuração de **comportamento de limite de armazenamento excedido** . Ele controla o comportamento de entrada e limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos para escolher ao configurá-lo:

- **Limpar dados antigos** (padrão)  
- **Pausar a entrada**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção está configurada para **Limpar dados antigos**. Essa configuração pode ser alternada conforme necessário após a hora de criação usando o portal do Azure, na página **Configurar** do ambiente de Azure Time Series insights.
>
> - Para obter informações sobre como configurar políticas de retenção, leia [Configurando a retenção em Azure Time Series insights](time-series-insights-how-to-configure-retention.md).

Ambas as políticas de retenção de dados são descritas mais detalhadamente abaixo.

## <a name="purge-old-data"></a>Limpar dados antigos

- **Limpar dados antigos** é a configuração padrão para ambientes de Azure Time Series insights.  
- **Limpar dados antigos** é preferencial quando os usuários desejam sempre ter seus *dados mais recentes* em seu ambiente de Azure Time Series insights.
- A configuração **limpar dados antigos** *limpa* os dados depois que os limites do ambiente (tempo de retenção, tamanho ou contagem, o que ocorrer primeiro) são atingidos. A retenção é definida como 30 dias por padrão.
- Os dados ingeridos mais antigos são limpos primeiro (a abordagem "primeiro a entrar primeiro a sair").

### <a name="example-one"></a>Exemplo um

Considere um ambiente de exemplo com o comportamento de retenção **Continuar entrada e limpar dados antigos**:

O **tempo de retenção de dados** é definido como 400 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total. Suponha que os dados de entrada se acumulam até um volume de 500 MB por dia, em média. Considerando-se a taxa de dados de entrada, esse ambiente pode reter somente o equivalente a 60 dias de dados, já que a capacidade máxima é atingida após 60 dias. Os dados de entrada se acumulam assim: 500 MB por dia x 60 dias = 30 GB.

No dia 61 º, o ambiente mostra os dados mais recentes, mas limpa os dados mais antigos, com mais de 60 dias. A limpeza abre espaço para os novos dados sendo transmitidos em entrada, para que novos dados possam continuar a serem explorados. Se o usuário deseja manter os dados por mais tempo, ele pode aumentar o tamanho do ambiente adicionando unidades adicionais ou reduzir o volume de dados enviados por push.  

### <a name="example-two"></a>Exemplo dois

Suponha que um ambiente também configurou o comportamento de retenção **Continuar entrada e limpar dados antigos**. Neste exemplo, o **Tempo de retenção de dados** é definido para um valor mais baixo, de 180 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados por 180 dias completos, a entrada diária não pode exceder 0,166 GB (166 MB).  

Sempre que a taxa diária de entrada do ambiente excede 0,166 GB por dia, os dados não podem ser armazenados por 180 dias, já que alguns dados são limpos. Considere esse mesmo ambiente durante um período ocupado. Suponha que a taxa de entrada do ambiente possa aumentar para uma média de 0,189 GB por dia. Nesse período ocupado, aproximadamente 158 dias de dados são retidos (30GB/0,189 = 158,73 dias de retenção). Esse tempo é menor que o período de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausar a entrada

- A configuração **Pausar entrada** foi projetada para garantir que os dados não sejam limpos se os limites de tamanho e contagem forem atingidos antes do período de retenção.  
- **Pausar a entrada** fornece tempo adicional para os usuários aumentarem a capacidade de seu ambiente antes de os dados serem limpos devido à violação do período de retenção.
- Ele ajuda a proteger você contra perda de dados, mas pode criar uma oportunidade para a perda de seus dados mais recentes se a entrada estiver em pausa além do período de retenção da origem do evento.
- No entanto, depois que a capacidade máxima de um ambiente é atingida, o ambiente pausa a entrada de dados até que as seguintes ações adicionais ocorram:

  - Você aumenta a capacidade máxima do ambiente para adicionar mais unidades de escala, conforme descrito em [como dimensionar seu ambiente de Azure Time Series insights](time-series-insights-how-to-scale-your-environment.md).
  - O período de retenção de dados é atingido e os dados são limpos, trazendo o ambiente abaixo de sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com o comportamento de retenção configurado para **Pausar entrada**. Neste exemplo, o **Período de retenção de dados** está configurado para 60 dias. A **capacidade** é definida como três (3) unidades de S1. Suponha que esse ambiente tem uma entrada de 2 GB de dados por dia. Nesse ambiente, a entrada é colocada em pausa quando a capacidade máxima é atingida.

Nesse momento, o ambiente mostra o mesmo conjunto de dados até que a entrada seja retomada ou até que a **entrada continuar** esteja habilitada (o que limparia os mais antigos para liberar espaço para novos dados).

Quando a entrada é retomada:

- Os dados são transmitidos na ordem em que foram recebidos pela origem do evento
- Os eventos são indexados com base no respectivo carimbo de data/hora, a menos que você tenha excedido as políticas de retenção na origem do evento. Para obter mais informações sobre a configuração de retenção da origem do evento, veja [Perguntas frequentes dos Hubs de Eventos](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Você deve definir alertas para que forneçam aviso e ajudem a evitar que a entrada seja colocada em pausa. Há possibilidade de perda de dados, já que a retenção padrão é de 1 dia para origens do evento do Azure. Portanto, depois de entrada for colocada em pausa, você provavelmente perderá os dados mais recentes, a menos que uma ação adicional seja executada. Você deve aumentar a capacidade ou mudar o comportamento para **Limpar dados antigos** para evitar a possível perda de dados.

Nos hubs de eventos afetados, considere ajustar a propriedade de **retenção de mensagem** para minimizar a perda de dados ao pausar a entrada ocorre em Azure Time Series insights.

[![Retenção de mensagem do hub de eventos.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Se nenhuma propriedade estiver configurada na origem do evento ( `timeStampPropertyName` ), Azure Time Series insights usa como padrão o carimbo de data/hora de chegada no Hub de eventos como o eixo X. Se `timeStampPropertyName` o estiver configurado para ser algo mais, o ambiente procurará o configurado `timeStampPropertyName` no pacote de dados quando os eventos forem analisados.

Leia [como dimensionar seu ambiente de Azure Time Series insights](time-series-insights-how-to-scale-your-environment.md) para dimensionar seu ambiente para acomodar capacidade adicional ou aumentar a duração da retenção.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como configurar ou alterar as configurações de retenção de dados, examine [Configurando a retenção em Azure Time Series insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre a [redução da latência no Azure Time Series insights](time-series-insights-environment-mitigate-latency.md).
