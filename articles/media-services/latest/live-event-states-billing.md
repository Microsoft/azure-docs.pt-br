---
title: Estados de eventos ao vivo e cobrança nos serviços de mídia do Azure
description: Este tópico fornece uma visão geral dos Estados de evento dinâmico dos serviços de mídia do Azure e cobrança.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897808"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de eventos ao vivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos serviços de mídia do Azure, um evento ao vivo começa a cobrança assim que seu estado passa para **em execução ou em** **espera**. Você será cobrado mesmo se não houver vídeo fluindo pelo serviço. Para interromper o evento ao vivo da cobrança, você precisa parar o evento ao vivo. A transcrição ao vivo é cobrada da mesma maneira que o evento ao vivo.

Quando o **LiveEventEncodingType** em seu [evento ao vivo](/rest/api/media/liveevents) é definido como Standard ou Premium1080p, os serviços de mídia desligam automaticamente qualquer evento ao vivo que ainda esteja no estado de **execução** 12 horas após o feed de entrada ser perdido e não há nenhum s de **saída ao vivo** em execução. No entanto, você ainda será cobrado pelo tempo em que o evento ao vivo estava no estado de **execução** .

> [!NOTE]
> Eventos ao vivo de passagem não são desligados automaticamente e devem ser explicitamente interrompidos por meio da API para evitar uma cobrança excessiva.

## <a name="states"></a>Estados

O evento ao vivo pode estar em um dos Estados a seguir.

|Estado|Descrição|
|---|---|
|**Parado**| Esse é o estado inicial do evento ao vivo após a criação (a menos que AutoStart tenha sido definido como true). Nenhuma cobrança ocorre nesse estado. Nenhuma entrada pode ser recebida pelo evento ao vivo. |
|**Iniciando**| O evento ao vivo está iniciando e os recursos estão sendo alocados. Não há cobrança nesse estado.  Se ocorrer um erro, o evento ao vivo retornará ao estado parado.|
| **Alocar** | A ação de alocação foi chamada no evento ao vivo e os recursos estão sendo provisionados para esse evento ao vivo. Depois que essa operação for concluída com êxito, o evento ao vivo passará para o estado de espera.
|**Espera**| os recursos de eventos ao vivo foram provisionados e estão prontos para começar. A cobrança ocorre nesse estado.  A maioria das propriedades ainda pode ser atualizada, no entanto, ingestão ou streaming não é permitido durante esse estado.
|**Executando**| Os recursos de evento ao vivo foram alocados, as URLs de ingestão e de visualização foram geradas e são capazes de receber fluxos ao vivo. Neste ponto, o faturamento está ativo. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças.|
|**Parando**| O evento ao vivo está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. |
|**Excluir**| O evento ao vivo está sendo excluído. Não haverá cobrança nesse estado transitório. |

Você pode optar por habilitar transcrições dinâmicas ao criar o evento ao vivo. Se você fizer isso, será cobrado pelas transcrições dinâmicas sempre que o evento ao vivo estiver no estado **executando** . Observe que você será cobrado mesmo se não houver áudio fluindo pelo evento ao vivo.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
