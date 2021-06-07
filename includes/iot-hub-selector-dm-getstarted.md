---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "69558788"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Os aplicativos de back-end podem usar primitivos do Hub IoT do Azure, como um [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod], para iniciar remotamente e monitorar as ações de gerenciamento de dispositivo nos dispositivos. Este tutorial mostra como um aplicativo de back-end e um dispositivo podem trabalhar juntos para permitir que você inicie e monitore uma reinicialização remota por meio do Hub IoT.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use um método direto para iniciar as ações de gerenciamento do dispositivo (como a reinicialização, redefinição de fábrica e atualização do firmware) a partir de um aplicativo de back-end na nuvem. O dispositivo é responsável por:

* Lidar com a solicitação do método enviada a partir do Hub IoT.

* Iniciar a ação específica do dispositivo correspondente no dispositivo.

* Fornecimento de atualizações de status por meio das *propriedades relatadas* para o Hub IoT.

Você pode usar um aplicativo de back-end na nuvem para executar consultas do dispositivo gêmeo para relatar o progresso de suas ações de gerenciamento do dispositivo.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
