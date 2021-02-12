---
title: Azure IoT Edge e IoT Central do Azure | Microsoft Docs
description: Entenda como usar Azure IoT Edge com um aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 9a7c886ba4dd6e7ab4bd62700f5437855a16a5ad
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796560"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Conectar dispositivos Azure IoT Edge a um aplicativo do Azure IoT Central

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

O IoT Edge é composto por três componentes:

* Os **módulos do IoT Edge** são contêineres que executam serviços do Azure, serviços do parceiro ou o seu próprio código. Os módulos são implantados em dispositivos do IoT Edge e executados localmente nesses dispositivos.
* O **tempo de execução do IOT Edge** é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite monitorar e gerenciar dispositivos do IoT Edge remotamente. O IoT Central é a interface de nuvem.

Um dispositivo **Azure IoT Edge** pode ser um dispositivo de gateway com dispositivos downstream conectados ao dispositivo IoT Edge. Este artigo compartilha mais informações sobre os padrões de conectividade do dispositivo downstream.

Um **modelo de dispositivo** define as funcionalidades do seu dispositivo e dos módulos IoT Edge. As funcionalidades incluem telemetria que o módulo envia, as propriedades do módulo e os comandos aos quais um módulo responde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo downstream com um gateway e módulos

Os dispositivos downstream podem se conectar a um dispositivo de gateway do IoT Edge por meio do módulo `$edgeHub`. Esse dispositivo IoT Edge se torna um gateway transparente neste cenário.

![Diagrama de gateway transparente](./media/concepts-iot-edge/gateway-transparent.png)

Os dispositivos downstream também podem se conectar a um dispositivo de gateway do IoT Edge por meio de um módulo personalizado. No cenário a seguir, os dispositivos downstream conectam-se por meio de um módulo personalizado Modbus.

![Diagrama da conexão de módulo personalizado](./media/concepts-iot-edge/gateway-module.png)

O diagrama a seguir mostra a conexão a um dispositivo de gateway do IoT Edge por meio de ambos os tipos de módulos (personalizado e `$edgeHub`).  

![Diagrama de conexão usando ambos os módulos de conexão](./media/concepts-iot-edge/gateway-module-transparent.png)

Por fim, os dispositivos downstream podem se conectar a um dispositivo de gateway do IoT Edge por meio de vários módulos personalizados. O diagrama a seguir mostra os dispositivos downstream que se conectam por meio de um módulo personalizado Modbus, um módulo BLE personalizado e o módulo `$edgeHub`. 

![Diagrama de conexão por meio de vários módulos personalizados](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifestos de implantação e modelos de dispositivo

No IoT Edge, é possível implantar e gerenciar a lógica de negócios na forma de módulos. Os módulos do IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (por exemplo, o Azure Stream Analytics) ou o código específico da sua própria solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, confira [Módulos do IoT Edge](../../iot-edge/iot-edge-modules.md).

Em um alto nível, um manifesto de implantação é uma lista de módulos gêmeos que são configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais módulos instalar e como configurá-los. Manifestos de implantação incluem as propriedades desejadas para cada módulo gêmeo. Dispositivos do IoT Edge relatam de volta as propriedades relatadas para cada módulo.

Use o Visual Studio Code para criar um manifesto de implantação. Para saber mais, confira [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

No Azure IoT Central, você pode importar um manifesto de implantação para criar um modelo de dispositivo. O fluxograma a seguir mostra um ciclo de vida do manifesto de implantação no IoT Central.

![Fluxograma do ciclo de vida do manifesto de implantação](./media/concepts-iot-edge/dmflow.png)

O IoT Central modela um dispositivo IoT Edge da seguinte maneira:

* Cada modelo de dispositivo IoT Edge tem um modelo de dispositivo.
* Para cada módulo personalizado listado no manifesto de implantação, um modelo de funcionalidade do módulo é gerado.
* Uma relação é estabelecida entre cada modelo de funcionalidade de módulo e um modelo de dispositivo.
* Um modelo de funcionalidade do módulo implementa interfaces de módulo.
* Cada interface de módulo contém telemetria, propriedades e comandos.

![Diagrama de modelagem do IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivos IoT Edge gateway

Se você selecionou um dispositivo de IoT Edge para ser um dispositivo de gateway, poderá adicionar relações de downstream a modelos de dispositivo para dispositivos que você deseja conectar ao dispositivo de gateway.

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, uma próxima etapa sugerida será saber mais sobre os [tipos de dispositivo de gateway em IOT central](./tutorial-define-gateway-device-type.md).
