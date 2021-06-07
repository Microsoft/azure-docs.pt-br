---
title: 'Tutorial: Enviar dados de Hubs de Eventos para o data warehouse – Grade de Eventos'
description: Descreve como armazenar dados capturados pelos Hubs de Eventos no Azure Synapse Analytics por meio de gatilhos do Azure Functions e da Grade de Eventos.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854709"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir Big Data para um data warehouse
A [Grade de Eventos](overview.md) do Azure é um serviço de roteamento de evento inteligente que permite que você reaja às notificações ou aos eventos de aplicativos e serviços. Por exemplo, ele pode disparar uma Função do Azure para processar dados dos Hubs de Eventos capturados em um Armazenamento de Blobs ou Data Lake Storage. Este [exemplo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra como usar a Grade de Eventos e o Azure Functions para migrar dados capturados pelos Hubs de Eventos do Armazenamento de Blobs para o Azure Synapse Analytics, especificamente para um pool de SQL dedicado.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as diferenças nos serviços do sistema de mensagens do Azure, veja [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para uma introdução à Captura dos Hubs de Eventos, consulte [Habilitar a Captura dos Hubs de Eventos usando o Portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, consulte [exemplo Captura dos Hubs de Eventos e Grade de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
