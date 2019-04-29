---
title: Compreender a retenção de dados no ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve duas configurações que controlam a retenção de dados no ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: b230ac48cf2ca14c9ed988f869b5abba3e347215
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761555"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Compreender a retenção de dados no Time Series Insights

Este artigo descreve duas configurações que impactam na retenção de dados no ambiente do TSI (Time Series Insights).

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Neste vídeo, tratamos da retenção de dados do Azure Time Series Insights e como planejá-la.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada ambiente do TSI tem uma configuração que controla o **Tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente TSI tem uma configuração adicional **Comportamento em caso de limite de armazenamento excedido**. Essa configuração controla o comportamento de ingresso e de limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos dentre os quais escolher:
- **Limpar dados antigos** (padrão)  
- **Pausar a entrada**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção está configurada para **Limpar dados antigos**. Após o momento da criação, essa configuração pode ser modificada conforme necessário usando o Portal do Azure, na página **Configurar** do ambiente do TSI.

Para mais informações sobre como alternar entre os comportamentos de retenção, veja [Configuração da retenção no Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Compare o comportamento de retenção de dados:

## <a name="purge-old-data"></a>Limpar dados antigos

- Esse comportamento é o comportamento padrão para ambientes de TSI e exibe o mesmo comportamento que os ambientes de TSI apresentaram desde que foram lançados em visualização pública.  
- Esse comportamento é preferível quando os usuários querem ver sempre seus *dados mais recentes* em seu ambiente de TSI. 
- Esse comportamento *limpa* dados uma vez que os limites do ambiente (tempo de retenção, tamanho ou contagem, o que ocorrer primeiro) forem atingidos. A retenção é definida como 30 dias por padrão. 
- Os dados ingeridos mais antigos são apagados primeiro (abordagem PEPS).

### <a name="example-one"></a>Exemplo de um

Considere um ambiente de exemplo com o comportamento de retenção **Continuar entrada e limpar dados antigos**: Neste exemplo, o **Tempo de retenção de dados** é definido para um valor mais baixo, de 400 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total.   Suponha que os dados de entrada se acumulam até um volume de 500 MB por dia, em média. Considerando-se a taxa de dados de entrada, esse ambiente pode reter somente o equivalente a 60 dias de dados, já que a capacidade máxima é atingida após 60 dias. Os dados de entrada se acumulam assim: 500 MB cada dia x 60 dias = 30 GB.

Neste exemplo, no 61º dia, o ambiente mostra os dados mais recentes, mas descarta os dados mais antigos, com mais de 60 dias. A limpeza abre espaço para os novos dados sendo transmitidos em entrada, para que novos dados possam continuar a serem explorados. 

Se o usuário deseja manter os dados por mais tempo, ele pode aumentar o tamanho do ambiente adicionando unidades adicionais ou reduzir o volume de dados enviados por push.  

### <a name="example-two"></a>Exemplo dois

Suponha que um ambiente também configurou o comportamento de retenção **Continuar entrada e limpar dados antigos**. Neste exemplo, o **Tempo de retenção de dados** é definido para um valor mais baixo, de 180 dias. **Capacidade** é definido para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados por 180 dias completos, a entrada diária não pode exceder 0,166 GB (166 MB).  

Sempre que a taxa diária de entrada desse ambiente excede 0,166 GB, os dados não podem ser armazenados por 180 dias, já que alguns dados sofrem limpeza. Considere esse mesmo ambiente durante um período ocupado. Suponha que a taxa de entrada do ambiente pode aumentar para uma média de 0,189 GB por dia. Nesse período ocupado, aproximadamente 158 dias de dados são retidos (30GB/0,189 = 158,73 dias de retenção). Esse tempo é menor que o período de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausar a entrada

- Esse comportamento foi desenvolvido para garantir que dados não sejam limpos se os limites de tamanho e de contagem forem atingidos antes do período de retenção desses dados.  
- Esse comportamento fornece tempo adicional para os usuários aumentarem a capacidade de seu ambiente antes dos dados serem apagados devido à violação do período de retenção
- Esse comportamento ajuda a proteger contra perda de dados, mas cria uma oportunidade para a perda dos dados mais recentes se a entrada fica em pausa além do período de retenção de sua origem do evento.
- No entanto, depois que a capacidade máxima de um ambiente for atingida, o ambiente pausará a entrada de dados até que ações adicionais ocorram: 
   - Você aumenta a capacidade máxima do ambiente. Para obter mais informações, veja [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para adicionar mais unidades de escala.
   - O período de retenção de dados é atingido e os dados são limpos, colocando assim o ambiente abaixo de sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com o comportamento de retenção configurado para **Pausar entrada**. Neste exemplo, o **Período de retenção de dados** está configurado para 60 dias. A **Capacidade** está definida para 3 unidades de S1. Suponha que esse ambiente tem uma entrada de 2 GB de dados por dia. Nesse ambiente, a entrada é colocada em pausa quando a capacidade máxima é atingida. Nesse momento, o ambiente mostra o mesmo conjunto de dados até que a entrada seja retomada ou até que a opção 'retomar a entrada' seja habilitada (o que limparia os dados mais antigos para liberar espaço para novos dados). 

Quando a entrada é retomada:

- Os dados são transmitidos na ordem em que foram recebidos pela origem do evento
- Os eventos são indexados com base no respectivo carimbo de data/hora, a menos que você tenha excedido as políticas de retenção na origem do evento. Para obter mais informações sobre a configuração de retenção da origem do evento, veja [Perguntas frequentes dos Hubs de Eventos](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Você deve definir alertas para que forneçam aviso e ajudem a evitar que a entrada seja colocada em pausa. Há possibilidade de perda de dados, já que a retenção padrão é de 1 dia para origens do evento do Azure. Portanto, depois de entrada for colocada em pausa, você provavelmente perderá os dados mais recentes, a menos que uma ação adicional seja executada. Você deve aumentar a capacidade ou mudar o comportamento para **Limpar dados antigos** para evitar a possível perda de dados.

Nos hubs de eventos afetados, considere ajustar a propriedade **Retenção de Mensagem** para minimizar a perda de dados quando a entrada é colocada em pausa no Time Series Insights.

![Retenção de mensagem do hub de eventos.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Se nenhuma propriedade está configurada na origem do evento (timeStampPropertyName), o TSI assume como padrão o carimbo de data/hora de chegada no hub de eventos como o eixo x. Se timeStampPropertyName está configurado para algo diferente, o ambiente de procura o timeStampPropertyName configurado no pacote de dados em que os eventos são analisados. 

Se você precisar dimensionar o ambiente para acomodar a capacidade adicional ou para aumentar a duração da retenção, veja [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para obter mais informações.  

## <a name="next-steps"></a>Próximas etapas

- Para mais informações sobre como mudar o comportamento de retenção, veja [Configuração da retenção no Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).
