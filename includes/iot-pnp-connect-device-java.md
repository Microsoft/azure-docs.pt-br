---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b4913b510dabd0fc2c8456d4c199c6b11569597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612413"
---
Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao hub IoT e usar a ferramenta Azure IoT Explorer para exibir a telemetria que ele envia. O aplicativo de exemplo é escrito no Java e é incluído no SDK do dispositivo de IoT do Azure para Java. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido no Windows, instale o seguinte software em um ambiente do Windows local:

* Java SE Development Kit 8. Em [Suporte de longo prazo do Java para o Azure e o Azure Stack](/java/azure/jdk/), em **Suporte de longo prazo**, selecione **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Baixar o código

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do Java do dispositivo do Hub IoT do Azure.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub dos [SDKs de Java e as Bibliotecas de IoT do Azure](https://github.com/Azure/azure-iot-sdk-java) nesta localização:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Compilar o código

No Windows, navegue até a pasta raiz do repositório do SDK do Java clonado.

Execute o seguinte comando para compilar o aplicativo de exemplo:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Navegue até a pasta *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Para executar o aplicativo de exemplo, execute o seguinte comando:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo termostato simples do IoT Plug and Play. O modelo que esse exemplo implementa não usa os [componentes](../articles/iot-pnp/concepts-modeling-guide.md) do IoT Plug and Play. O [arquivo de modelo de DTDL para o dispositivo de termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

O código do dispositivo usa a classe `DeviceClient` padrão para se conectar ao seu hub IoT. O dispositivo envia a ID de modelo do modelo de DTDL que ele implementa na solicitação de conexão. Um dispositivo que envia uma ID de modelo é um dispositivo IoT Plug and Play:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

A ID do modelo é armazenada no código, conforme mostrado no seguinte snippet:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

O código que atualiza propriedades, manipula comandos e envia telemetria é idêntico ao código de um dispositivo que não usa as convenções do IoT Plug and Play.

O exemplo usa uma biblioteca JSON para analisar objetos JSON nos conteúdos enviados do seu hub IoT:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
