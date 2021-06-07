---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025919"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os [grupos de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) fornecem exibições independentes no fluxo de eventos que permitem que aplicativos e serviços do Azure consumam dados de modo independente do mesmo ponto de extremidade do Hub de Eventos. Nesta seção, você adiciona um grupo de consumidores ao ponto de extremidade interno do Hub IoT que é usado posteriormente neste tutorial para efetuar pull de dados do ponto de extremidade.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **pontos de extremidade internos**, selecione **eventos** no painel direito e insira um nome em **grupos de consumidores**. Clique em **Salvar**.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)