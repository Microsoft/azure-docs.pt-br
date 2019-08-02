---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172029"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use os dispositivos gêmeos para:

* Armazene os metadados de dispositivo de seu back-end da solução.

* Relate informações de estado atual, como recursos disponíveis e condições (por exemplo, o método conectividade usado) do aplicativo do dispositivo.

* Sincronize o estado dos fluxos de trabalho de longa duração (como atualizações de firmware e configuração) entre o aplicativo do dispositivo e o aplicativo back-end.

* Consultar os metadados, a configuração ou o estado do seu dispositivo.

Dispositivos gêmeos são projetados para sincronização e para consultar condições e configurações de dispositivos. Mais saber mais sobre quando usar dispositivos gêmeos, confira [Entender dispositivos gêmeos](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Dispositivos gêmeos são armazenados em um hub IoT e contêm:

* *marcações*, metadados do dispositivo acessíveis apenas por meio do back-end da solução;

* *propriedades desejadas*, objetos JSON modificáveis pelo back-end da solução e observáveis pelo aplicativo de dispositivo; e

* *propriedades relatadas*, objetos JSON modificáveis pelo aplicativo de dispositivo e legíveis pelo back-end da solução. Marcas e propriedades não podem conter matrizes, mas objetos podem ser aninhados.

![Imagem de dispositivo gêmeo mostrando a funcionalidade](./media/iot-hub-selector-twin-get-started/twin.png)

Além disso, o back-end da solução pode consultar dispositivos gêmeos com base em todos os dados acima.
Veja [Entender dispositivos gêmeos](../articles/iot-hub/iot-hub-devguide-device-twins.md) para obter mais informações sobre dispositivos gêmeos e a referência de [Linguagem de consulta do Hub IoT](../articles/iot-hub/iot-hub-devguide-query-language.md) para consultas.


Este tutorial mostra como:

* Criar um aplicativo de back-end que adiciona *marcas* a um dispositivo gêmeo e um aplicativo de dispositivo simulado que relata seu canal de conectividade como uma *propriedade relatada* no dispositivo gêmeo.

* Consulte dispositivos por meio do aplicativo de back-end usando filtros nas marcações e propriedades criadas anteriormente.
