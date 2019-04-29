---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 82222dd927f46761941a6a750d96222cc626e71b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887156"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo MXChip IoT DevKit (DevKit) ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa ter os seguintes recursos:

1. Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Um dispositivo DevKit. Para adquirir um dispositivo DevKit, visite [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Aplicativo Devkits de exemplo

Um aplicativo criado a partir de **Devkits de exemplo** modelo de aplicativo inclui um **MXChip** modelo de dispositivo que define as seguintes características de dispositivo:

- Medidas de telemetria para o **umidade**, **temperatura**, **pressão**, **magnetômetro** (medido ao longo de X, Y, eixo Z), **Acelerômetro** (medido ao longo de X, Y, eixo Z), e **giroscópio** (medido ao longo de X, Y, eixo Z).
- Estado de medida para **estado do dispositivo**.
- Medida de evento para **botão B pressionado**.
- As configurações para **tensão**, **atual**, **velocidade**e um **IR** alternância.
- Propriedades do dispositivo **morrem número** e **local do dispositivo**, que é uma propriedade local.
- Propriedade de nuvem **fabricados no**.
- Comandos **Echo** e **contagem regressiva**. Quando um dispositivo real recebe um **Echo** comando, ele mostra o valor enviado na tela do dispositivo. Quando um dispositivo real recebe um **contagem regressiva** de comando, os ciclos de LED por meio de um padrão, e o dispositivo envia valores de contagem regressiva para a IoT Central.

Para obter detalhes completos sobre a configuração, consulte [detalhes do modelo de dispositivo de MXChip](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

### <a name="get-your-device-connection-details"></a>Obter detalhes de conexão de seu dispositivo

Em seu aplicativo do Azure IoT Central, adicionar um dispositivo real do **MXChip** modelo de dispositivo e anote os detalhes de conexão do dispositivo: **Definir o escopo de ID, ID do dispositivo e chave primária**:

1. Adicionar um **dispositivo real** no Device Explorer, selecione **+ Novo > Real** para adicionar um dispositivo real.

    * Insira um minúsculo **ID do dispositivo**, ou usar o sugerido **ID do dispositivo**.
    * Insira um **nome do dispositivo**, ou use o nome sugerido

    ![Adicionar dispositivo](media/howto-connect-devkit/add-device.png)

1. Para obter detalhes de conexão de dispositivo **ID do escopo**, **ID do dispositivo**, e **Primary key**, selecione **Connect** na página do dispositivo.

    ![Detalhes da conexão](media/howto-connect-devkit/device-connect.png)

1. Anote os detalhes de conexão. Você está temporariamente desconectado da internet quando você prepara seu dispositivo do Kit de desenvolvimento na próxima etapa.

### <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

Se você tiver usado anteriormente o dispositivo e quiser reconfigurá-lo para usar uma rede diferente de Wi-Fi, a cadeia de caracteres de conexão ou a medição de telemetria, pressione ambos os **um** e **B** botões ao mesmo tempo. Se não funcionar, pressione **redefinir** botão e tente novamente.

#### <a name="to-prepare-the-devkit-device"></a>Para preparar o dispositivo DevKit

1. Baixe o último firmware do Azure IoT Central pré-criado para o MXChip na página [versões](https://aka.ms/iotcentral-docs-MXChip-releases) no GitHub.
1. Conecte o dispositivo DevKit ao computador de desenvolvimento usando um cabo USB. No Windows, uma janela do explorador de arquivos abre em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)**.
1. Arraste o arquivo **iotCentral.bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reiniciado com o novo firmware.

1. Quando o dispositivo DevKit for reiniciado, a tela a seguir será exibida:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Se a tela exibir qualquer outra diferente dessa, redefina o dispositivo e pressione os botões  **A** e **B** no dispositivo ao mesmo tempo para reinicializar o dispositivo.

1. O dispositivo agora está no modo de AP (Ponto de Acesso). É possível conectar esse ponto de acesso WiFi a partir do computador ou dispositivo móvel.

1. No computador, telefone ou tablet, conecte o nome da rede WiFi exibido na tela do dispositivo. Quando você se conecta a essa rede, você não tem acesso à internet. Esse estado é esperado e você estiver conectado apenas a esta rede por um curto período enquanto você configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/start](http://192.168.0.1/start). A página da Web a seguir é exibida:

    ![Página de configuração do dispositivo](media/howto-connect-devkit/configpage.png)

    Na página da web, digite:
    - O nome da sua rede Wi-Fi
    - a senha de rede Wi-Fi
    - O código PIN, mostrado na tela do dispositivo
    - Os detalhes de conexão **ID do escopo**, **ID do dispositivo**, e **chave primária** do seu dispositivo (você deve já tiver salvo essa seguindo as etapas)
    - Selecione todas as medidas a telemetria disponível

1. Após escolher **Configurar Dispositivo**, você verá esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Pressione o botão **Reiniciar** no dispositivo.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Quando o dispositivo DevKit for reiniciado, a tela no dispositivo exibirá:

* O número de mensagens de telemetria enviadas.
* O número de falhas.
* O número de propriedades desejadas recebidas e o número de propriedades relatadas enviadas.

> [!NOTE]
> Se o dispositivo é exibido executar um loop quando ele tenta se conectar, verifique se o dispositivo estiver **Blocked** no IoT Central, e **Unblock** o dispositivo para que ele possa se conectar ao aplicativo.

Agite o dispositivo para enviar uma propriedade relatada. O dispositivo envia um número aleatório como a propriedade de dispositivo **Número impresso**.

É possível exibir as medidas de telemetria e os valores das propriedades relatadas e definir as configurações no Azure IoT Central:

1. Use **Device Explorer** para navegar até a página **Medidas** do dispositivo MXChip real que você adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit/realdevicenew.png)

1. Na página **Medidas**, é possível ver a telemetria proveniente do dispositivo MXChip:

    ![Exibir telemetria do dispositivo real](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na página **Propriedades**, é possível ver o último número impresso e a localização do dispositivo relatados pelo dispositivo:

    ![Exibir propriedades do dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Na página **Configurações**, é possível atualizar as configurações no dispositivo MXChip:

    ![Exibir as configurações do dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Sobre o **comandos** página, você pode chamar o **eco** e **contagem regressiva** comandos:

    ![Comandos de chamada](media/howto-connect-devkit/devicecommands.png)

1. Na página **Painel**, você pode ver o mapa do local

    ![Exibir o painel do dispositivo](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Se você quiser explorar e modificar o código do dispositivo, poderá baixá-lo do GitHub. Se você planeja modificar o código, siga essas instruções para [preparar o ambiente de desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) do sistema operacional de área de trabalho.

Para baixar o código-fonte, execute o comando a seguir no computador desktop:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

O comando anterior baixa o código-fonte para uma pasta chamada `iot-central-firmware`.

> [!NOTE]
> Se o **git** não estiver instalado no ambiente de desenvolvimento, será possível baixá-lo a partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Examine o código

Use o Visual Studio Code para abrir o `MXCHIP/mxchip_advanced` pasta no `iot-central-firmware` pasta:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver como a telemetria é enviada para o aplicativo do Azure IoT Central, abra o **telemetry.cpp** arquivo no `src` pasta:

- A função `TelemetryController::buildTelemetryPayload` cria a carga de telemetria JSON usando dados dos sensores no dispositivo.

- A função `TelemetryController::sendTelemetryPayload` chamadas `sendTelemetry` na **AzureIOTClient.cpp** para enviar o conteúdo JSON para o IoT Hub o aplicativo usa Azure IoT Central.

Para ver como os valores de propriedade são relatados para o aplicativo do Azure IoT Central, abra o **telemetry.cpp** arquivo no `src` pasta:

- A função `TelemetryController::loop` envia o **local** aproximadamente a cada 30 segundos de propriedade relatada. Ele usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** arquivo de origem.

- A função `TelemetryController::loop` envia o **dieNumber** propriedade relatada quando o acelerômetro dispositivo detecta um toque duplo. Ele usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** arquivo de origem.

Para ver como o dispositivo responde aos comandos chamados a partir do aplicativo de IoT Central, abra o **registeredMethodHandlers.cpp** arquivo no `src` pasta:

- O **dmEcho** função é o manipulador para o **eco** comando. Ele mostra a **displayedValue** arquivado na carga na tela do dispositivo.

- O **dmCountdown** função é o manipulador para o **contagem regressiva** comando. Ele muda a cor do LED do dispositivo e usa uma propriedade relatada para enviar o valor de contagem regressiva para o aplicativo de IoT Central. A propriedade relatada tem o mesmo nome que o comando. A função usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** arquivo de origem.

O código a **AzureIOTClient.cpp** funções a partir do arquivo de origem usa a [SDKs de IoT do Microsoft Azure e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o IoT Hub.

Para obter informações sobre como modificar, compilar e carregar o código de exemplo no dispositivo, consulte o arquivo **readme.md** na pasta `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Detalhes do modelo de Dispositivo de MXChip

Um aplicativo criado com base no modelo de aplicativo de Devkits de Exemplo inclui um modelo de dispositivo MXChip com as características a seguir:

### <a name="measurements"></a>Medidas

#### <a name="telemetry"></a>Telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Estados 
| NOME          | Nome de exibição   | NORMAL | CUIDADO | PERIGO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Estado do dispositivo   | Verde  | Laranja  | Vermelho    | 

#### <a name="events"></a>Eventos 
| NOME             | Nome de exibição      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Botão B Pressionado  | 

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltagem      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Atual      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Configurações de alternância

| Nome de exibição | Nome do campo | Texto ativado | Texto desativado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | DESATIVADO      | Desativar     |

### <a name="properties"></a>propriedades

| Type            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Propriedade de dispositivo | Localização do dispositivo   | location  | location    |
| Text            | Fabricado em     | manufacturedIn   | N/D       |

### <a name="commands"></a>Comandos

| Nome de exibição | Nome do campo | Tipo de retorno | Nome de exibição do campo de entrada | Nome do campo de entrada | Tipo de campo de entrada |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | text        | valor a ser exibido         | displayedValue   | text             |
| Contagem regressiva    | Contagem regressiva  | número      | Contagem de               | countFrom        | número           |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um Raspberry Pi ao seu aplicativo do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo de IoT.
