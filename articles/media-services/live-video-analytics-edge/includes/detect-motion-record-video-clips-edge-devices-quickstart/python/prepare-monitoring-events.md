---
ms.openlocfilehash: 3196d377c09ca0cce24093710bc4be13fb21d2e3
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531877"
---
Verifique se você concluiu as etapas para [Preparar-se para monitorar eventos](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Iniciar Monitoramento de Ponto de Extremidade de Evento Interno](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
