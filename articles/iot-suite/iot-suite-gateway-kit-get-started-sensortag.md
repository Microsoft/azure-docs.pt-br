---
title: Conectar um gateway ao Azure IoT Suite usando um NUC Intel | Microsoft Docs
description: "Use o Kit de Gateway Comercial Microsoft IoT e a solução pré-configurada de monitoramento remoto. Use o gateway Azure IoT Edge para habilitar um dispositivo SensorTag a conectar-se à solução de monitoramento remoto, enviar telemetria à nuvem e responder aos métodos invocados no painel da solução."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: bda16be1094276fcecef1e708f9d7db307d94a89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Conectar o gateway do Azure IoT Edge à solução pré-configurada de monitoramento remoto e enviar telemetria de um SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Este tutorial mostra como usar o Azure IoT Edge para enviar dados de temperatura e umidade do dispositivo SensorTag para a solução pré-configurada de monitoramento remoto. O SensorTag se conecta ao gateway NUC Intel por Bluetooth. O tutorial usa:

- Azure IoT Edge para implementar um exemplo de gateway.
- A solução pré-configurada de monitoramento remoto do IoT Suite como o back-end baseado na nuvem.

## <a name="overview"></a>Visão geral

Neste tutorial, você completa as seguintes etapas:

- Implantar uma instância da solução pré-configurada de monitoramento remoto em sua assinatura. Esta etapa implanta e configura automaticamente vários serviços do Azure.
- Configure seu dispositivo de gateway NUC Intel para comunicação com seu computador e com a solução de monitoramento remoto.
- Configure seu gateway NUC Intel para receber telemetria de um dispositivo SensorTag e enviá-la ao painel de monitoramento remoto.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag]. Este tutorial recupera dados de telemetria via Bluetooth do dispositivo SensorTag.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitoramento remoto provisiona um conjunto de serviços do Azure na sua assinatura do Azure. A implantação reflete uma arquitetura empresarial real. Para evitar encargos desnecessários de consumo do Azure, exclua a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado de realizar as tarefas nela. Caso precise da solução novamente, você poderá recriá-la facilmente. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config].

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Configurar a conectividade de Bluetooth

Configure o Bluetooth no NUC Intel para habilitar o dispositivo SensorTag a conectar-se e enviar telemetria.

### <a name="find-the-mac-address-of-the-sensortag"></a>Localizar o endereço MAC do SensorTag

1. No shell do NUC Intel, execute o seguinte comando para desbloquear o serviço Bluetooth:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Execute os seguintes comandos para iniciar o serviço Bluetooth no NUC Intel e entrar no shell de Bluetooth:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Execute o comando a seguir para ligar o controlador Bluetooth:

    ```bash
    power on
    ```

    Quando o controlador estiver ligado, você verá uma mensagem **Sucesso em ligar a energia**.

1. Execute o comando a seguir para verificar se há dispositivos Bluetooth nas proximidades:

    ```bash
    scan on
    ```

1. Pressione o botão de energia no SensorTag para torná-lo detectável. O LED verde piscará.

1. Ao ver uma mensagem no shell indicando que o controlador descobriu o SensorTag, anote o endereço MAC do dispositivo. O endereço MAC é semelhante a **A0:E6:F8:B5:F6:00**. Você precisará do endereço MAC posteriormente no tutorial ao configurar o gateway.

1. Execute o seguinte comando para desligar a verificação de Bluetooth:

    ```bash
    scan off
    ```

1. Execute o comando a seguir para verificar se você pode se conectar ao dispositivo SensorTag:

    ```bash
    connect <SensorTag MAC address>
    ```

    Se você se conectar com êxito, o shell mostrará a mensagem **Conexão bem-sucedida** e imprimirá as informações sobre o dispositivo SensorTag. Caso você não consiga se conectar, verifique se o SensorTag ainda está ligado.

1. Agora você pode desconectar-se do SensorTag e sair do shell do Bluetooth executando os seguintes comandos:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Criar o módulo personalizado do IoT Edge

Agora você pode criar o módulo personalizado do IoT Edge que permite ao gateway enviar mensagens para a solução de monitoramento remoto. Para obter mais informações sobre como configurar um gateway e módulos do IoT Edge, consulte [Conceitos do Azure IoT Edge][lnk-gateway-concepts].

Baixe o código-fonte para os módulos personalizados do IoT Edge do GitHub usando os seguintes comandos:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Crie o módulo personalizado do IoT Edge usando os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

O script de build coloca o módulo do IoT Edge libsensor2remotemonitoring.so personalizado na pasta de build.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar e executar o gateway do IoT Edge

Agora você pode configurar o gateway do IoT Edge para enviar telemetria do dispositivo SensorTag para seu painel de monitoramento remoto. Para obter mais informações sobre como configurar um gateway e módulos do IoT Edge, consulte [Conceitos do Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Neste tutorial, você usará o editor de texto `vi` padrão no NUC Intel. Se você nunca tiver usado o `vi` antes, conclua um tutorial introdutório, como [Unix – O Tutorial do Editor vi][lnk-vi-tutorial] para familiarizar-se com esse editor. Como alternativa, instale o editor [nano](https://www.nano-editor.org/) mais amigável usando o comando `smart install nano -y`.

Abra o exemplo de arquivo de configuração no editor **vi** usando o seguinte comando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Localize as seguintes linhas na configuração para o módulo IoTHub:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Substitua os valores do espaço reservado pelas informações do Hub IoT criadas e salvas no início deste tutorial. O valor para IoTHubName se parece com **yourrmsolution37e08**, e o valor IoTSuffix normalmente é **azure-devices.net**.

Localize as seguintes linhas na configuração para o módulo de mapeamento:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Substitua o espaço reservado **macAddress** pelo o endereço MAC do SensorTag que você anotou anteriormente. Substitua os espaços reservados **deviceID** e **deviceKey** pelas IDs e chaves para os dois dispositivos criados anteriormente na solução de monitoramento remoto.

Localize as seguintes linhas na configuração para o módulo de SensorTag:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Substitua o espaço reservado **endereço\_mac\_do dispositivo** pelo o endereço MAC do SensorTag que você anotou anteriormente.

Salve suas alterações.

Agora você pode executar o gateway usando os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

O gateway do IoT Edge inicia no NUC Intel e envia a telemetria do SensorTag para a solução de monitoramento remoto:

![O gateway do IoT Edge envia telemetria do SensorTag][img-telemetry]

Pressione **Ctrl-C** para sair do programa a qualquer momento.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

O gateway agora está enviando telemetria do dispositivo SensorTag para a solução de monitoramento remoto. Você pode exibir a telemetria no painel de solução. Você também pode enviar comandos ao seu dispositivo SensorTag usando o gateway no painel da solução.

- Navegue para o painel da solução.
- Selecione o dispositivo configurado no gateway que representa o SensorTag na lista suspensa **Dispositivo para Exibir**.
- A telemetria do dispositivo SensorTag é exibida no painel.

![Exibir telemetria dos dispositivos SensorTag][img-telemetry-display]

> [!WARNING]
> Se você deixar a solução de monitoramento remoto em execução em sua conta do Azure, você receberá uma cobrança pelo tempo de execução. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config]. Exclua a solução pré-configurada de sua conta do Azure quando terminar de usá-la.


## <a name="next-steps"></a>Próximas etapas

Visite o [Centro de Desenvolvimento do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e a documentação sobre o Azure IoT.

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started