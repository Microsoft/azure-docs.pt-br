---
title: Tutorial – Criar uma instância do IoT Edge para análise de vídeo no Azure IoT Central (VM do Linux)
description: Este tutorial mostra como criar uma instância do IoT Edge para análise de vídeo em uma VM Linux a ser usada com o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832616"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Tutorial: Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)

O Azure IoT Edge é um serviço totalmente gerenciado que fornece inteligência de nuvem no local por meio da implantação e da execução do seguinte:

* Lógica personalizada
* Serviços do Azure
* Inteligência artificial

No IoT Edge, esses serviços são executados diretamente em dispositivos IoT multiplataforma, permitindo que você execute sua solução de IoT com segurança e em escala na nuvem ou offline.

Este tutorial mostra como preparar um dispositivo do IoT Edge em uma VM do Azure. A instância do IoT Edge executa os módulos da Análise Dinâmica de Vídeo usados pelo modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo do Azure IoT Central.

Neste tutorial, você aprende a:
> [!div class="checklist"]
> * Criar uma VM do Azure com o runtime do Azure IoT Edge instalado
> * Preparar a instalação do IoT Edge para hospedar o módulo da Análise Dinâmica de Vídeo e conectar-se ao IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá concluir o tutorial anterior [Criar um aplicativo de análise de vídeo no Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) ou [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

Você também precisará ter uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, crie uma gratuitamente na [página de inscrição do Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Implantar o Azure IoT Edge

Para criar uma VM do Azure com os módulos mais recentes da Análise Dinâmica de Vídeo e do runtime do IoT Edge instalados, selecione o seguinte botão:

[![Botão Implantar no Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Use as informações da seguinte tabela para completar o formulário de **Implantação personalizada**:

| Campo | Valor |
| ----- | ----- |
| Subscription | Selecione sua assinatura do Azure. |
| Resource group | *lva-rg*: grupo de recursos criado no tutorial anterior. |
| Região       | *Leste dos EUA* |
| Prefixo do rótulo DNS | Escolha um prefixo DNS exclusivo para a VM. Deve conter somente caracteres alfabéticos, sem dígitos nem caracteres especiais. |
| Nome de Usuário do Administrador | *AzureUser* |
| Senha do Administrador | Digite uma senha. Anote a senha do arquivo *scratchpad.txt*, pois você a usará mais tarde. |
| ID de escopo | A **ID de Escopo** que você anotou no arquivo *scratchpad.txt* no tutorial anterior quando adicionou o dispositivo de gateway. |
| ID do Dispositivo | *gateway-001*: o dispositivo de gateway que você criou no tutorial anterior. |
| Chave do Dispositivo | A **chave primária do dispositivo** que você anotou no arquivo *scratchpad.txt* no tutorial anterior quando adicionou o dispositivo de gateway. |
| Host de aplicativo do IoT Central | A **URL do Aplicativo** que você anotou no arquivo *scratchpad.txt* no tutorial anterior. Por exemplo, *traders.azureiotcentral.com*. |
| Token da API do aplicativo do IoT Central | O **token da API do operador** que você anotou no arquivo *scratchpad.txt* no tutorial anterior. |
| Chave de provisionamento de dispositivos do IoT Central | A **chave primária do grupo SAS-IoT-Devices** que você anotou no arquivo *scratchpad.txt* no tutorial anterior. |
| Tamanho da VM | *Standard_DS1_v2* |
| Versão do sistema operacional Ubuntu | *18.04-LTS* |
| Local | *[resourceGroup().location]* |

Selecione **Examinar + criar**. Após a conclusão da validação, escolha **Criar**. Normalmente, leva cerca de três minutos para que a implantação seja concluída. Quando a implantação for concluída, procure o grupo de recursos **lva-rg** no portal do Azure.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Verificar se o runtime do IoT Edge carrega os módulos

No portal do Azure, procure o grupo de recursos **lva-rg** e selecione a máquina virtual. Depois, na seção **Suporte + solução de problemas**, selecione **Console serial**.

Clique em **ENTER** para obter um aviso `login:`. Use *AzureUser* como o nome de usuário e a senha que você escolheu quando criou a VM.

Execute o comando a seguir para verificar a versão do runtime do IoT Edge. No momento da elaboração deste artigo, a versão era 1.0.9:

```bash
sudo iotedge --version
```

Liste os módulos do IoT Edge usando o comando:

```bash
sudo iotedge list
```

A implantação configurou os seguintes cinco módulos do IoT Edge para execução:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

A implantação criou um ambiente personalizado do IoT Edge com os módulos necessários para a Análise Dinâmica de Vídeo. A implantação atualizou o **config.yaml** padrão para verificar se o runtime do IoT Edge usou o Serviço de Provisionamento de Dispositivos IoT para se conectar ao IoT Central. A implantação também criou um arquivo chamado **state.json** na pasta **/data/storage** para fornecer dados de configuração adicionais aos módulos. Para obter mais informações, confira o tutorial [Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md).

Para solucionar problemas do dispositivo do IoT Edge, confira [Solução de problemas do dispositivo do IoT Edge](../../iot-edge/troubleshoot.md)

## <a name="use-the-rtsp-simulator"></a>Usar o simulador RTSP

Caso você não tenha dispositivos de câmera reais para se conectar ao seu dispositivo do IoT Edge, use os dois dispositivos de câmera simulados no modelo de aplicativo de análise de vídeo. Esta seção mostra como usar um fluxo de vídeo simulado no dispositivo do IoT Edge.

Estas instruções usam o [Servidor de Mídia Live555](http://www.live555.com/mediaServer/) como um simulador RTSP em um contêiner do Docker.

> [!NOTE]
> As referências aos softwares de terceiros neste repositório servem apenas para fins informativos e de conveniência. A Microsoft não endossa nem fornece direitos para os softwares de terceiros. Para obter mais informações, confira [Servidor de Mídia Live555](http://www.live555.com/mediaServer/).

Use o comando a seguir para executar o utilitário **rtspvideo** em um contêiner do Docker na VM do IoT Edge. O contêiner do Docker cria um fluxo RTSP em segundo plano:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Use o seguinte comando para listar os contêineres do Docker:

```bash
sudo docker ps
```

A lista inclui um contêiner chamado **live555**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver concluído o aplicativo, remova todos os recursos criados da seguinte maneira:

1. No aplicativo do IoT Central, procure a página **Seu aplicativo** na seção **Administração**. Em seguida, selecione **Excluir**.
1. No portal do Azure, exclua o grupo de recursos **lva-rg**.
1. No computador local, pare o contêiner **amp-viewer** do Docker.

## <a name="next-steps"></a>Próximas etapas

Você acabou de implantar o runtime do IoT Edge, os módulos da LVA e o fluxo de simulação Live555 em uma VM do Linux em execução no Azure.

Para gerenciar as câmeras, siga o próximo tutorial

> [!div class="nextstepaction"]
> [Monitorar e gerenciar um aplicativo de detecção de objetos e movimentos e análise de vídeo](./tutorial-video-analytics-manage.md)