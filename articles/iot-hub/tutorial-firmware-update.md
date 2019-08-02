---
title: Atualizar o firmware do dispositivo via Hub IoT do Azure | Microsoft Docs
description: Implemente um processo de atualização de firmware do dispositivo usando trabalhos e dispositivos gêmeos.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2019
ms.custom: mvc
ms.openlocfilehash: c576020118778e34b80187ec056fca22a4d9c5b1
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485823"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Tutorial: Implementar um processo de atualização de firmware de dispositivos

Talvez seja necessário atualizar o firmware dos dispositivos conectados ao seu Hub IoT. Por exemplo, talvez você queira adicionar novos recursos para o firmware ou aplicar patches de segurança. Em muitos cenários de IoT, é impraticável visitar fisicamente e depois aplicar manualmente atualizações de firmware para seus dispositivos. Este tutorial mostra como você pode iniciar e monitorar o processo de atualização de firmware remotamente por meio de um aplicativo de back-end conectado ao seu hub.

Para criar e monitorar o processo de atualização de firmware, o aplicativo de back-end neste tutorial cria uma _configuração_ em seu Hub IoT. O [gerenciamento de dispositivo automático](iot-hub-auto-device-config.md) do Hub IoT usa essa configuração para atualizar um conjunto de _propriedades desejadas do dispositivo gêmeo_ em todos os seus dispositivos resfriadores. As propriedades desejadas especificam os detalhes da atualização de firmware necessária. Enquanto os dispositivos resfriadores estiverem executando o processo de atualização de firmware, eles relatam o status para o aplicativo de back-end usando _propriedades relatadas do dispositivo gêmeo_. O aplicativo de back-end pode usar a configuração para monitorar as propriedades relatadas enviadas do dispositivo e acompanhar o processo de atualização do firmware para conclusão:

![Processo de atualização de firmware](media/tutorial-firmware-update/Process.png)

Neste tutorial, você completa as seguintes tarefas:

> [!div class="checklist"]
> * Criar um Hub IoT e adicionar um dispositivo de teste ao registro de identidade do dispositivo.
> * Criar uma configuração para definir a atualização do firmware.
> * Simular o processo de atualização do firmware em um dispositivo.
> * Receber atualizações de status enviadas pelo dispositivo durante o andamento da atualização do firmware.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Os dois exemplos de aplicativo executados neste início rápido são escritos usando o Node.js. Você precisa do Node.js v10.x.x ou posterior em seu computador de desenvolvimento.

Você pode fazer o download do Node.js para várias plataformas a partir do [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

Faça o download do projeto de exemplo de Node.js do https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip e extraia o arquivo ZIP.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Para concluir este tutorial, a assinatura do Azure deve ter um Hub IoT com um dispositivo adicionado ao registro de identidade do dispositivo. A entrada no registro de identidade do dispositivo permite que o dispositivo simulado executado neste tutorial conecte-se ao hub.

Se ainda não houver um Hub IoT configurado na assinatura, será possível configurar um com o script de CLI a seguir. Esse script usa o nome **tutorial-iot-hub** para o Hub IoT e será necessário substituir esse nome pelo nome exclusivo ao executá-lo. O script cria o grupo de recursos e o hub na região **Centro dos EUA**, a qual é possível alterar para uma região mais próxima a você. O script recupera a cadeia de conexão do serviço do Hub IoT, usada no aplicativo de exemplo de back-end para conectar ao seu Hub IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --name $hubname -policy-name service -o table

```

Este tutorial usa um dispositivo simulado chamado **MyFirmwareUpdateDevice**. O script a seguir adiciona esse dispositivo ao seu registro de identidade do dispositivo, define um valor de marca e recupera sua cadeia de conexão:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Se você executar esses comandos em um prompt de comando do Windows ou prompt do PowerShell, confira a página [azure-iot-cli-extension tips](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) para obter informações sobre como citar as cadeias de caracteres JSON.

## <a name="start-the-firmware-update"></a>Iniciar a atualização do firmware

Crie uma [configuração de gerenciamento de dispositivo automática](iot-hub-automatic-device-management.md#create-a-configuration) no aplicativo de back-end para iniciar o processo de atualização de firmware em todos os dispositivos marcados com um **devicetype** do resfriador. Nesta seção, você verá como:

* Crie uma configuração de um aplicativo de back-end.
* Monitore a conclusão do trabalho.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Use propriedades desejadas para iniciar a atualização de firmware no aplicativo de back-end

Para exibir o código do aplicativo de back-end que cria a configuração, navegue até a pasta **iot-hub/Tutorials/FirmwareUpdate** do projeto de exemplo Node.js baixado. Em seguida, abra o arquivo ServiceClient.js em um editor de texto.

O aplicativo de back-end cria a seguinte configuração:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

A configuração inclui as seções a seguir:

* `content` especifica as propriedades de firmware desejadas enviadas para os dispositivos selecionados.
* `metrics` especifica as consultas para executar esse relatório de status da atualização de firmware.
* `targetCondition` seleciona os dispositivos para receber a atualização do firmware.
* `priorty` define a prioridade relativa dessa configuração para outras configurações.

O aplicativo de back-end usa o código a seguir para criar a configuração para definir as propriedades desejadas:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Depois de criar a configuração, o aplicativo monitora a atualização do firmware:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Uma configuração relata dois tipos de métricas:

* Métricas do sistema que relatam quantos dispositivos são destinados e quantos dispositivos têm a atualização aplicada.
* Métricas personalizadas geradas pelas consultas adicionadas à configuração. Neste tutorial, as consultas relatam quantos dispositivos estão em cada estágio do processo de atualização.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Responder à solicitação de atualização de firmware do dispositivo

Para exibir o código do dispositivo simulado aplicativo que lida com as propriedades desejadas de firmware enviadas do aplicativo de back-end, navegue até a pasta **iot-hub/Tutorials/FirmwareUpdate** do projeto de exemplo Node.js baixado. Em seguida, abra o arquivo SimulatedDevice.js em um editor de texto.

O aplicativo do dispositivo simulado cria um manipulador de atualizações para as propriedades **properties.desired.firmware** desejadas no dispositivo gêmeo. No manipulador, ele executa algumas verificações básicas sobre as propriedades desejadas antes de inicializar o processo de atualização:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Atualizar o firmware

A função **initiateFirmwareUpdateFlow** executa a atualização. Essa função usa a função **waterfall** para executar as fases do processo de atualização em sequência. Neste exemplo, a atualização do firmware tem quatro fases. A primeira fase baixa a imagem; a segunda fase verifica a imagem usando uma soma de verificação; a terceira fase aplica a imagem; e a última fase reinicializa o dispositivo:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Durante o processo de atualização, o dispositivo simulado relata o andamento usando propriedades relatadas:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

O snippet a seguir mostra a implementação da fase de download. Durante a fase de download, o dispositivo simulado usa propriedades relatadas para enviar informações de status para o aplicativo de back-end:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Execute o exemplo

Nesta seção, você deve executar dois aplicativos de exemplo para observar como um aplicativo de back-end cria a configuração para gerenciar o processo de atualização de firmware do dispositivo simulado.

Para executar o dispositivo simulado e os aplicativos de back-end, serão necessários as cadeias de conexão de serviço e dispositivo. Você fez uma anotação das cadeias de conexão quando criou os recursos no início deste tutorial.

Para executar o aplicativo de dispositivo simulado, abra uma janela do prompt de comando ou shell e navegue até a pasta **iot-hub/Tutorials/FirmwareUpdate** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar o aplicativo de back-end, abra outra janela do prompt de comando ou shell. Em seguida, navegue até a pasta **iot-hub/Tutorials/FirmwareUpdate** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A captura de tela a seguir mostra a saída do aplicativo de dispositivo simulado e mostra como ele responde à atualização das propriedades desejadas de firmware do aplicativo de back-end:

![Dispositivo simulado](./media/tutorial-firmware-update/SimulatedDevice.png)

A captura de tela a seguir mostra a saída do aplicativo de back-end e destaca como ele cria a configuração para atualizar as propriedades desejadas de firmware:

![Aplicativo de back-end](./media/tutorial-firmware-update/BackEnd1.png)

A captura de tela a seguir mostra a saída do aplicativo de back-end e destaca como ele monitora as métricas de atualização de firmware no dispositivo simulado:

![Aplicativo de back-end](./media/tutorial-firmware-update/BackEnd2.png)

Como as configurações automáticas do dispositivo são executadas no momento da criação e a cada cinco minutos, pode ser que você não veja cada atualização de status ser enviada para o aplicativo de back-end. Você também pode exibir as métricas no portal, na seção **Gerenciamento automático do dispositivo -> Configuração de dispositivo IoT** do seu Hub IoT:

![Exibir configuração no portal](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja concluir o próximo tutorial, deixe o grupo de recursos e o Hub IoT e reutilize-os posteriormente.

Se você não precisar mais do hub IoT, exclua-o junto com o grupo de recursos no portal. Para fazer isso, selecione o grupo de recursos **tutorial-iot-hub-rg** que contém o Hub IoT e clique em **Excluir**.

Como alternativa, use a CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implementar um processo de atualização do firmware para seus dispositivos conectados. Avance para o próximo tutorial para saber como usar ferramentas do portal do Hub IoT e comandos da CLI do Azure para testar a conectividade do dispositivo.

> [!div class="nextstepaction"]
> [Usar um dispositivo simulado para testar a conectividade com o hub IoT](tutorial-connectivity.md)
