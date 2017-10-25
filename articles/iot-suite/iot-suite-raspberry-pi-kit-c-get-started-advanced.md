---
title: "Conectar um Raspberry Pi ao Azure IoT Suite usando C para oferecer suporte a atualizações de firmware | Microsoft Docs"
description: "Use o Microsoft Azure IoT Starter Kit para o Raspberry Pi 3 e o Azure IoT Suite. Use C para conectar seu Raspberry Pi à solução de monitoramento remoto, enviar telemetria de sensores para a nuvem e executar uma atualização de firmware remoto."
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
ms.openlocfilehash: f36f6512bb30e4b109b1bd1c3cdab10300f4edc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Conectar o Raspberry Pi 3 à solução de monitoramento remoto e habilitar as atualizações de firmware remotas usando C

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

Este tutorial mostra como usar o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 para:

* Desenvolver um leitor de temperatura e umidade que possa se comunicar com a nuvem.
* Habilitar e executar uma atualização de firmware remota para atualizar o aplicativo cliente no Raspberry Pi.

O tutorial usa:

* SO Raspbian, a linguagem de programação C e o SDK do Microsoft Azure IoT para C a fim de implementar um dispositivo de exemplo.
* A solução pré-configurada de monitoramento remoto do IoT Suite como o back-end baseado na nuvem.

## <a name="overview"></a>Visão geral

Neste tutorial, você completa as seguintes etapas:

* Implantar uma instância da solução pré-configurada de monitoramento remoto em sua assinatura. Esta etapa implanta e configura automaticamente vários serviços do Azure.
* Configurar seu dispositivo e sensores para comunicação com seu computador e com a solução de monitoramento remoto.
* Atualizar o exemplo de código de dispositivo para conectar-se à solução de monitoramento remoto e enviar telemetria, que pode ser exibida no painel da solução.
* Use o exemplo de código de dispositivo para atualizar o aplicativo cliente.

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitoramento remoto provisiona um conjunto de serviços do Azure na sua assinatura do Azure. A implantação reflete uma arquitetura empresarial real. Para evitar encargos desnecessários de consumo do Azure, exclua a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado de realizar as tarefas nela. Caso precise da solução novamente, você poderá recriá-la facilmente. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config].

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Agora você pode baixar e configurar o aplicativo cliente de monitoramento remoto em seu Raspberry Pi.

### <a name="clone-the-repositories"></a>Clonar os repositórios

Caso ainda não tenha feito isso, clone os repositórios necessários executando os seguintes comandos em seu Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Atualizar a cadeia de conexão do dispositivo

Abra o exemplo de arquivo de configuração no editor **nano** usando o seguinte comando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Substitua os valores do espaço reservado pelas informações de ID do dispositivo e do Hub IoT que você criou e salvou no início deste tutorial.

Quando terminar, o conteúdo do arquivo deviceinfo deverá parecer com o exemplo a seguir:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Salve suas alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).

## <a name="build-the-sample"></a>Compilar o exemplo

Se você ainda não tiver feito isso, instale os pacotes de pré-requisito para o SDK do dispositivo IoT do Microsoft Azure para C executando os seguintes comandos em um terminal no Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Agora você pode compilar a solução de exemplo no Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Agora você pode executar o programa de exemplo no Raspberry Pi. Insira o comando:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

O que você vê a seguir é um exemplo da saída vista no prompt de comando do Raspberry Pi:

![Saída do aplicativo Raspberry Pi][img-raspberry-output]

Pressione **Ctrl-C** para sair do programa a qualquer momento.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-raspberry-pi-kit-view-telemetry-advanced.md)]

1. No painel de solução, clique em **Dispositivos** para visitar a página **Dispositivos**. Selecione seu Raspberry Pi na **Lista de Dispositivos**. Depois, escolha **Métodos**:

    ![Listar dispositivos no painel][img-list-devices]

1. Na página **Invocar Método** escolha **InitiateFirmwareUpdate** na lista suspensa **Método**.

1. No campo **FWPackageURI**, insira **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Esse arquivo contém a implementação da versão 2.0 do firmware.

1. Escolha **InvokeMethod**. O aplicativo no Raspberry Pi envia uma confirmação de volta para o painel da solução. Em seguida, ele inicia o processo de atualização de firmware baixando a nova versão do firmware:

    ![Mostrar o histórico do método][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Observar o processo de atualização do firmware

Observe o processo de atualização do firmware enquanto ele é executado no dispositivo e exibindo as propriedades relatadas no painel de solução:

1. Você pode exibir o progresso no processo de atualização no Raspberry Pi:

    ![Mostrar progresso da atualização][img-update-progress]

    > [!NOTE]
    > O aplicativo de monitoramento remoto reinicia silenciosamente após a conclusão da atualização. Use o comando `ps -ef` para verificar se ele está em execução. Se você quiser encerrar o processo, use o comando `kill` com a id do processo.

1. Veja o status da atualização de firmware, conforme indicado pelo dispositivo, no portal da solução. A captura de tela a seguir mostra o status e a duração de cada estágio do processo de atualização e a nova versão do firmware:

    ![Mostrar status do trabalho][img-job-status]

    Se você navegar de volta para o painel, poderá verificar que o dispositivo ainda está enviando telemetria após a atualização do firmware.

> [!WARNING]
> Se você deixar a solução de monitoramento remoto em execução em sua conta do Azure, você receberá uma cobrança pelo tempo de execução. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config]. Exclua a solução pré-configurada de sua conta do Azure quando terminar de usá-la.

## <a name="next-steps"></a>Próximas etapas

Visite o [Centro de Desenvolvimento do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e a documentação sobre o Azure IoT.


[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md