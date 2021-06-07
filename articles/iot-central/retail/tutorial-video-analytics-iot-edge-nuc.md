---
title: Tutorial – Criar uma instância do IoT Edge para análise de vídeo no Azure IoT Central (Intel NUC)
description: Este tutorial mostra como criar uma instância do IoT Edge para análise de vídeo a ser usada com o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831919"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Tutorial: Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)

O Azure IoT Edge é um serviço totalmente gerenciado que fornece inteligência de nuvem no local por meio da implantação e da execução do seguinte:

* Lógica personalizada
* Serviços do Azure
* Inteligência artificial

No IoT Edge, esses serviços são executados diretamente em dispositivos IoT multiplataforma, permitindo que você execute sua solução de IoT com segurança e em escala na nuvem ou offline.

Este tutorial mostra como instalar e configurar o runtime do IoT Edge em um dispositivo Intel NUC.

Neste tutorial, você aprende a:
> [!div class="checklist"]
> * Atualizar e configurar o IoT Edge
> * Configurar o gateway do IoT Edge
> * Conectar uma câmera local compatível com ONVIF ao dispositivo Intel NUC

## <a name="prerequisites"></a>Pré-requisitos

* Antes de começar, você deverá concluir o tutorial anterior [Criar um aplicativo da Análise Dinâmica de Vídeo no Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) ou [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).
* Um dispositivo, como um Intel NUC, executando o Linux, que possa executar contêineres do Docker e tenha capacidade de processamento suficiente para executar a análise de vídeo.
* O [runtime do IoT Edge instalado](../../iot-edge/how-to-install-iot-edge.md) e em execução no dispositivo.
* Para se conectar ao dispositivo do IoT Edge no computador Windows, você precisará do [cliente SSH PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) ou um utilitário equivalente.
* Você também precisará ter uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, crie uma gratuitamente na [página de inscrição do Azure](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Configurar o dispositivo IoT Edge

Caso não tenha o runtime do IoT Edge instalado no computador do Intel NUC, confira as instruções de [Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian](../../iot-edge/how-to-install-iot-edge.md).

Para atualizar o runtime do IoT Edge:

1. Use o utilitário de saída PuTTY para se conectar ao dispositivo do IoT Edge.

1. Execute os comandos a seguir para atualizar e verificar a versão do runtime do IoT Edge. No momento da elaboração deste artigo, a versão era 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Use os seguintes comandos para criar as pastas usadas pela implantação com as permissões necessárias:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Para adicionar o arquivo de configuração *state.json* à pasta */data/storage* no dispositivo do IoT Edge:

1. Use um editor de texto para abrir o arquivo *state.json* na pasta *lva-configuration* no computador local.

1. Atualize os espaços reservados `system` e `iotCentral > properties` com valores de cadeia de caracteres que descrevam o dispositivo de gateway. Você poderá ver esses valores posteriormente no painel do aplicativo do IoT Central.

1. Atualize os espaços reservados `iotCentral > appKeys` com os valores anotados do arquivo *scratchpad.txt* no tutorial anterior. Salve as alterações.

1. Use o utilitário `scp` do PuTTY em um prompt de comando para copiar o arquivo *state.json* recém-editado para a pasta */data/storage* no dispositivo do IoT Edge. Este exemplo usa `192.168.0.144` como um endereço IP de exemplo; substitua-o pelo endereço IP do seu dispositivo do IoT Edge:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Configure o IoT Edge para se registrar e se conectar ao aplicativo do IoT Central:

1. Use o utilitário de saída PuTTY para se conectar ao dispositivo do IoT Edge.

1. Use um editor de texto, como o `nano`, para abrir o arquivo config.yaml do IoT Edge.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > Os arquivos YAML não podem usar guias para recuo; em vez disso, use dois espaços. Os itens de nível superior não podem ter um espaço em branco à esquerda.

1. Role a tela para baixo até ver `# Manual provisioning configuration`. Comente as próximas três linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Role a tela para baixo até ver `# DPS symmetric key provisioning configuration`. Remova a marca de comentário das próximas oito linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Substitua `{scope_id}` pelo **Escopo da ID** que você anotou no arquivo *scratchpad.txt* no tutorial anterior.

1. Substitua `{registration_id}` por *gateway-001*, o dispositivo criado no tutorial anterior.

1. Substitua `{symmetric_key}` pela **Chave primária** do dispositivo **gateway-001** que você anotou no arquivo *scratchpad.txt* no tutorial anterior.

1. Execute o seguinte comando para reiniciar o daemon do IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o status dos módulos do IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A saída do comando anterior mostra cinco módulos em execução. Veja também o status dos módulos em execução no aplicativo do IoT Central.

    > [!TIP]
    > Execute novamente esse comando para verificar o status. Talvez seja necessário aguardar até que todos os módulos iniciem a execução.

Se os módulos do IoT Edge não forem iniciados corretamente, confira [Solução de problemas do dispositivo do IoT Edge](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Coletar o fluxo do RSTP da sua câmera

Identifique as URLs de fluxo do RTSP das câmeras conectadas ao dispositivo do IoT Edge, por exemplo:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Tente ver o fluxo da câmera no computador IoT Edge usando um player de mídia, como o VLC.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver concluído o aplicativo, remova todos os recursos criados da seguinte maneira:

1. No aplicativo do IoT Central, procure a página **Seu aplicativo** na seção **Administração**. Em seguida, selecione **Excluir**.
1. No portal do Azure, exclua o grupo de recursos **lva-rg**.
1. No computador local, pare o contêiner **amp-viewer** do Docker.

## <a name="next-steps"></a>Próximas etapas

Você acabou de implantar o runtime do IoT Edge e os módulos da LVA no dispositivo de gateway Intel NUC.

Para gerenciar as câmeras, siga o próximo tutorial:

> [!div class="nextstepaction"]
> [Monitorar e gerenciar um aplicativo de detecção de objetos e movimentos e análise de vídeo](./tutorial-video-analytics-manage.md)