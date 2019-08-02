---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172026"
---
> [!div class="op_single_selector"]
> * [Dispositivo: Serviço do Node. js: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Dispositivo: C#Serviço: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Dispositivo: Serviço de Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Dispositivo: Serviço do Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Os aplicativos de back-end podem usar primitivos no Hub IoT do Azure, ou seja, o [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod], para iniciar remotamente e monitorar as ações de gerenciamento de dispositivo nos dispositivos. Este tutorial mostra como um aplicativo de back-end e um dispositivo podem trabalhar juntos para permitir que você inicie e monitore uma reinicialização remota por meio do Hub IoT.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use um método direto para iniciar as ações de gerenciamento do dispositivo (como a reinicialização, redefinição de fábrica e atualização do firmware) a partir de um aplicativo de back-end na nuvem. O dispositivo é responsável por:

* Lidar com a solicitação do método enviada a partir do Hub IoT.
* Iniciar a ação específica do dispositivo correspondente no dispositivo.
* Fornecimento de atualizações de status por meio das *propriedades relatadas* para o Hub IoT.

Você pode usar um aplicativo de back-end na nuvem para executar consultas do dispositivo gêmeo para relatar o progresso de suas ações de gerenciamento do dispositivo.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
