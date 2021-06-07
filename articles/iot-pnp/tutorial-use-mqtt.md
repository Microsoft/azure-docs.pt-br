---
title: 'Tutorial: usar o MQTT para criar um cliente de dispositivo do IoT Plug and Play do Azure | Microsoft Docs'
description: 'Tutorial: usar o protocolo MQTT diretamente para criar um cliente de dispositivo do IoT Plug and Play sem usar SDKs do Dispositivo IoT do Azure'
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 64ff4615c2b41e341352dce5143d48ec8e6e802a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066765"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Tutorial: usar o MQTT para desenvolver um cliente de dispositivo do IoT Plug and Play

Você deve usar um dos SDKs do dispositivo IoT do Azure para criar seus clientes de dispositivo IoT Plug and Play, se possível. No entanto, em cenários como o uso de um dispositivo com restrição de memória, talvez seja necessário usar uma biblioteca MQTT para se comunicar com o Hub IoT.

O exemplo neste tutorial usa a biblioteca MQTT [Eclipse Mosquitto](http://mosquitto.org/) e o Visual Studio. As etapas neste tutorial pressupõem que você esteja usando o Windows em seu computador de desenvolvimento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Baixar e compilar a biblioteca do Eclipse Mosquitto.
> * Modificar o código de exemplo do MQTT baseado em C para tornar o dispositivo em um IoT Plug and Play.
> * Identificar os tópicos do MQTT que um dispositivo IoT Plug and Play usa.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – inclua a carga de trabalho **Desenvolvimento para Desktop com C++** ao [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Use a ferramenta *Azure IoT Explorer* para adicionar um novo dispositivo ao Hub IoT. Você configurou o hub IoT e a ferramenta Azure IoT Explorer ao concluir [os inícios rápidos e os tutoriais de "Configurar um ambiente para o IoT Plug and Play"](set-up-environment.md):

1. Inicie a ferramenta **Azure IoT Explorer**.
1. Na página **Hubs IoT**, selecione **Ver dispositivos neste hub**.
1. Na página **Dispositivos**, selecione **+ Novo**.
1. Crie um dispositivo chamado *my-mqtt-device* que usa uma chave simétrica gerada automaticamente.
1. Na página **Identidade do dispositivo**, expanda **Cadeia de conexão com token SAS**.
1. Escolha a **Chave primária** a ser usada como **Chave simétrica**, defina o tempo de expiração para 60 minutos e selecione **Gerar**.
1. Copie a **cadeia de conexão de token SAS** gerada, esse valor será usado posteriormente no tutorial.

## <a name="clone-sample-repo"></a>Clone o repositório do exemplo

Use o seguinte comando para clonar o repositório de exemplo em um local adequado no computador local:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Instalar biblioteca MQTT

Use a ferramenta `vcpkg` para baixar e compilar a biblioteca do Eclipse Mosquitto.

Use o seguinte comando para clonar o repositório **Vcpkg** em um local adequado no computador local:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Use os comandos a seguir para preparar o ambiente `vcpkg`. Você precisa de um prompt de comandos com privilégios elevados ao executar `vcpkg integrate install`:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Use o seguinte comando para baixar e compilar a biblioteca do Eclipse Mosquitto:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrar o exemplo para IoT Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Examinar o código de exemplo não IoT Plug and Play

Atualize o código com detalhes do Hub IoT e do dispositivo antes de compilá-lo e executá-lo.

Para exibir o código de exemplo no Visual Studio, abra o arquivo de solução *MQTTWin32.sln* na pasta *IoTMQTTSample\src\Windows*.

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **TelemetryMQTTWin32** e selecione **Definir como Projeto de Inicialização**.

No projeto **TelemetryMQTTWin32**, abra o arquivo de origem **MQTT_Mosquitto.cpp**. Atualize as definições de informações de conexão com os detalhes do dispositivo anotados anteriormente. Substitua os espaços reservados da cadeia de caracteres do token pelo:

* Identificador `IOTHUBNAME` com o nome do hub IoT.
* Identificador `DEVICEID` com `my-mqtt-device`.
* Identificador `PWD` com a parte correta da cadeia de conexão de token SAS gerada para o dispositivo. Use a parte da cadeia de conexão de `SharedAccessSignature sr=` até o final.

Verifique se o código está funcionando corretamente iniciando o Azure IoT Explorer e comece a escutar a telemetria.

Execute o aplicativo (Ctrl + F5) e, depois de alguns segundos, você verá uma saída parecida com esta:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Saída do aplicativo de exemplo MQTT":::

No Azure IoT Explorer, é possível ver que o dispositivo não é um dispositivo IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Dispositivo não IoT Plug and Play no Azure IoT Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Tornar o dispositivo um dispositivo IoT Plug and Play

Um dispositivo IoT Plug and Play precisa seguir um conjunto de convenções simples. Se um dispositivo enviar uma ID de modelo ao se conectar, ele se tornará um dispositivo IoT Plug and Play.

Neste exemplo, adicione uma ID de modelo à conexão de pacote MQTT. Você passa a ID de modelo como um parâmetro querystring no `USERNAME` e altera a `api-version` para `2020-09-30`:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Recompile e execute o exemplo.

O dispositivo gêmeo agora inclui a ID de modelo:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Exibir a ID de modelo no Azure IoT Explorer":::

Agora é possível navegar pelo componente de IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Exibir componentes no Azure IoT Explorer":::

Agora é possível modificar o código do dispositivo para implementar a telemetria, as propriedades e os comandos definidos no modelo. Para ver um exemplo de implementação do dispositivo de termostato usando a biblioteca Mosquitto, confira [Usando o MQTT PnP com o Hub IoT do Azure sem o SDK do IoT no Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) no GitHub.

> [!NOTE]
>O cliente usa o arquivo de certificado raiz `IoTHubRootCA_Baltimore.pem` para verificar a identidade do Hub IoT ao qual ele se conecta.

### <a name="mqtt-topics"></a>Tópicos do MQTT

As definições a seguir são para os tópicos do MQTT que o dispositivo usa para enviar informações ao Hub IoT. Para saber mais, confira [Comunicar-se com o Hub IoT usando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md):

* O `DEVICE_CAPABILITIES_MESSAGE` define o tópico que o dispositivo usa para relatar as interfaces que ele implementa.
* O `DEVICETWIN_PATCH_MESSAGE` define o tópico que o dispositivo usa para relatar atualizações de propriedade ao Hub IoT.
* O `DEVICE_TELEMETRY_MESSAGE` define o tópico que o dispositivo usa para enviar telemetria ao Hub IoT.

Para obter mais informações sobre o MQTT, visite o repositório do GitHub [Exemplos do MQTT para IoT do Azure](https://github.com/Azure-Samples/IoTMQTTSample/).

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a modificar um cliente de dispositivo MQTT para seguir as convenções de IoT Plug and Play. Para saber mais sobre o suporte do Hub IoT ao protocolo MQTT, confira:

> [!div class="nextstepaction"]
> [Comunicar com o Hub IoT usando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md)