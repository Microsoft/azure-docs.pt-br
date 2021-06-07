---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99532004"
---
Você usará o módulo da Análise Dinâmica de Vídeo no IoT Edge para detectar movimento no fluxo de vídeo ao vivo de entrada e enviar eventos para o Hub IoT. Para ver esses eventos, siga estas etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure pelo Hub IoT do Azure no canto inferior esquerdo.
1. Expanda o nó **Dispositivos**.
1. Clique com o botão direito do mouse em **lva-sample-device** e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**.

    ![Iniciar monitoramento do ponto de extremidade de evento interno](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
