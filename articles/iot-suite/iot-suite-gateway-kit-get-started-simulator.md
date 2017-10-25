---
title: Conectar um gateway ao Azure IoT Suite usando um NUC Intel | Microsoft Docs
description: "Use o Kit de Gateway Comercial Microsoft IoT e a solução pré-configurada de monitoramento remoto. Use o gateway Azure IoT Edge para conectar-se à solução de monitoramento remoto, enviar telemetria simulada à nuvem e responder aos métodos invocados no painel da solução."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Conectar o gateway do Azure IoT Edge à solução pré-configurada de monitoramento remoto e enviar telemetria simulada

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Este tutorial mostra como usar o Azure IoT Edge para simular os dados de temperatura e umidade para enviar à solução pré-configurada de monitoramento remoto. O tutorial usa:

- Azure IoT Edge para implementar um exemplo de gateway.
- A solução pré-configurada de monitoramento remoto do IoT Suite como o back-end baseado na nuvem.

## <a name="overview"></a>Visão geral

Neste tutorial, você completa as seguintes etapas:

- Implantar uma instância da solução pré-configurada de monitoramento remoto em sua assinatura. Esta etapa implanta e configura automaticamente vários serviços do Azure.
- Configure seu dispositivo de gateway NUC Intel para comunicação com seu computador e com a solução de monitoramento remoto.
- Configure o gateway do IoT Edge para enviar telemetria simulada que você possa exibir no painel de solução.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitoramento remoto provisiona um conjunto de serviços do Azure na sua assinatura do Azure. A implantação reflete uma arquitetura empresarial real. Para evitar encargos desnecessários de consumo do Azure, exclua a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado de realizar as tarefas nela. Caso precise da solução novamente, você poderá recriá-la facilmente. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config].

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Repita as etapas anteriores para adicionar um segundo dispositivo usando uma ID de dispositivo como **device02**. O exemplo envia dados de dois dispositivos simulados no gateway para a solução de monitoramento remoto.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

O script de build coloca o módulo personalizado do IoT Edge libsimulator.so na pasta de build.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar e executar o gateway do IoT Edge

Agora você pode configurar o gateway do IoT Edge para enviar telemetria simulada no seu painel de monitoramento remoto. Para obter mais informações sobre como configurar um gateway e módulos do IoT Edge, consulte [Conceitos do Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Neste tutorial, você usará o editor de texto `vi` padrão no NUC Intel. Se você nunca tiver usado o `vi` antes, conclua um tutorial introdutório, como [Unix – O Tutorial do Editor vi][lnk-vi-tutorial] para familiarizar-se com esse editor. Como alternativa, instale o editor [nano](https://www.nano-editor.org/) mais amigável usando o comando `smart install nano -y`.

Abra o exemplo de arquivo de configuração no editor **vi** usando o seguinte comando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Substitua os espaços reservados **deviceID** e **deviceKey** pelas IDs e chaves para os dois dispositivos criados anteriormente na solução de monitoramento remoto.

Salve suas alterações.

Agora você pode executar o gateway do IoT Edge usando os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

O gateway inicia no NUC Intel e envia telemetria simulada para a solução de monitoramento remoto:

![O gateway do IoT Edge gera telemetria simulada][img-simulated telemetry]

Pressione **Ctrl-C** para sair do programa a qualquer momento.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

O gateway do IoT Edge agora está enviando a telemetria simulada para a solução de monitoramento remoto. Você pode exibir a telemetria no painel de solução.

- Navegue para o painel da solução.
- Selecione um dos dois dispositivos configurados no gateway na lista suspensa **Dispositivo para Exibir**.
- A telemetria dos dispositivos de gateway é exibida no painel.

![Exibir a telemetria dos dispositivos de gateway simulados][img-telemetry-display]

> [!WARNING]
> Se você deixar a solução de monitoramento remoto em execução em sua conta do Azure, você receberá uma cobrança pelo tempo de execução. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config]. Exclua a solução pré-configurada de sua conta do Azure quando terminar de usá-la.

## <a name="next-steps"></a>Próximas etapas

Visite o [Centro de Desenvolvimento do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e a documentação sobre o Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started