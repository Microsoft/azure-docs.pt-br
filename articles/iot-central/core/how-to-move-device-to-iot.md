---
title: Como mover um dispositivo para o Azure IoT Central do Hub IoT
description: Como mover o dispositivo para o Azure IoT Central do Hub IoT
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146434"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Como transferir um dispositivo para o Azure IoT Central do Hub IoT

*Este artigo se aplica a operadores e desenvolvedores de dispositivos.*  

Este artigo descreve como transferir um dispositivo para um aplicativo de IoT Central do Azure de um hub IoT. 

Primeiro, o dispositivo se conecta a um ponto de extremidade do DPS para recuperar as informações necessárias para se conectar ao seu aplicativo. Internamente, seu aplicativo IoT Central usa um hub IoT para tratar da conectividade do dispositivo.  

Um dispositivo pode ser conectado a um hub IoT diretamente usando uma cadeia de conexão ou usando o DPS. O [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](../../iot-dps/about-iot-dps.md) é a rota para IOT central.

## <a name="to-move-the-device"></a>Para mover o dispositivo

Para conectar um dispositivo a IoT Central do Hub IOT, um dispositivo precisa ser atualizado com:

* A [ID do escopo](../../iot-dps/concepts-service.md) do aplicativo IOT central.
* Uma chave derivada da chave [SAS do grupo](concepts-get-connected.md) ou [do certificado X. 509](../../iot-hub/iot-hub-x509ca-overview.md)

Para interagir com IoT Central, deve haver um modelo de dispositivo que modela as propriedades/telemetria/comandos que o dispositivo implementa. Para obter mais informações, consulte conectar- [se a IOT central](concepts-get-connected.md) e [quais são os modelos de dispositivo?](concepts-device-templates.md)

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Examine um exemplo de código que mostra como usar tokens SAS no [tutorial: criar e conectar um aplicativo cliente ao aplicativo de IOT central do Azure](tutorial-connect-device.md)
- Saiba como [conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IOT central aplicativo](how-to-connect-devices-x509.md)
- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
- Saiba como [Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [Dispositivos do Azure IoT Edge e do Azure IoT Central](./concepts-iot-edge.md)