---
title: Sincronizar o estado do dispositivo do Hub IoT | Microsoft Docs
description: Usar dispositivos gêmeos para sincronizar o estado entre os dispositivos e o Hub IoT
services: iot-hub
documentationcenter: ''
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2019
ms.custom: mvc
ms.openlocfilehash: 4ad3013f6914abbf4c75676e7423848dff9d5e9a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330369"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Tutorial: Configurar seus dispositivos de um serviço de back-end

Além de receber telemetria dos dispositivos, talvez seja necessário configurar os dispositivos a partir do serviço back-end. Ao enviar uma configuração desejada aos dispositivos, você também pode querer receber atualizações de conformidade e status desses dispositivos. Por exemplo, você pode definir uma faixa de temperatura operacional de destino para um dispositivo ou coletar informações de versão de firmware dos dispositivos.

Para sincronizar informações de estado entre um dispositivo e um Hub IoT, você usa _dispositivos gêmeos_. Um [dispositivo gêmeo](iot-hub-devguide-device-twins.md) é um documento JSON, associado a um dispositivo específico e armazenado pelo Hub IoT na nuvem, onde é possível [pesquisá-los](iot-hub-devguide-query-language.md). Um dispositivo gêmeo contém _propriedades desejadas_, _propriedades relatadas_ e _marcas_. Uma propriedade desejada é definida por um aplicativo de back-end e lida por um dispositivo. Uma propriedade relatada é definida por um dispositivo e lida por um aplicativo de back-end. Uma marca é definida por um aplicativo de back-end e nunca é enviada para um dispositivo. As marcas são usadas para organizar os dispositivos. Este tutorial mostra como usar as propriedades desejadas e relatadas para sincronizar informações de estado:

![Resumo de gêmeo](media/tutorial-device-twins/DeviceTwins.png)

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Criar um Hub IoT e adicionar um dispositivo de teste ao registro de identidade.
> * Usar as propriedades desejadas para enviar informações de estado ao dispositivo simulado.
> * Usar as propriedades relatadas para receber informações de estado do dispositivo simulado.

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

Para concluir este tutorial, a assinatura do Azure deve conter um Hub IoT com um dispositivo adicionado ao registro de identidade do dispositivo. A entrada no registro de identidade do dispositivo permite que o dispositivo simulado executado neste tutorial conecte-se ao hub.

Se ainda não houver um Hub IoT configurado na assinatura, será possível configurar um com o script de CLI a seguir. Esse script usa o nome **tutorial-iot-hub** para o Hub IoT e será necessário substituir esse nome pelo nome exclusivo ao executá-lo. O script cria o grupo de recursos e o hub na região **Centro dos EUA**, a qual é possível alterar para uma região mais próxima a você. O script recupera a cadeia de conexão de serviço do Hub IoT, que é usado no exemplo de back-end para conectar ao Hub IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

Este tutorial usa um dispositivo simulado chamado **MyTwinDevice**. O script a seguir adiciona esse dispositivo ao registro de identidade e recupera a cadeia de conexão:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Enviar informações de estado

Você usa as propriedades desejadas para enviar informações de estado de um aplicativo de back-end para um dispositivo. Nesta seção, você verá como:

* Receber e processar as propriedades desejadas em um dispositivo.
* Enviar as propriedades desejadas de um aplicativo de back-end.

Para exibir o código de exemplo do dispositivo simulado que recebe as propriedades desejadas, navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js de exemplo transferido por download. Em seguida, abra o arquivo SimulatedDevice.js em um editor de texto.

As seções a seguir descrevem o código executado no dispositivo simulado que responde às alterações de propriedade desejadas enviadas do aplicativo de back-end:

### <a name="retrieve-the-device-twin-object"></a>Recuperar o objeto de dispositivo gêmeo

O código a seguir conecta o Hub IoT usando uma cadeia de conexão de dispositivo:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

O código a seguir obtém um dispositivo gêmeo do objeto de cliente:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Exemplo de propriedades desejadas

É possível estruturar as propriedades desejadas de qualquer maneira que seja conveniente ao aplicativo. Este exemplo usa uma propriedade de nível superior chamada **fanOn** e agrupa as propriedades restantes em **componentes** separados. O snippet de código JSON a seguir mostra a estrutura das propriedades desejadas que este tutorial usa:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Criar manipuladores

É possível criar manipuladores para atualizações de propriedades desejadas que respondem a atualizações em diferentes níveis na hierarquia JSON. Por exemplo, esse manipulador vê todas as alterações de propriedade desejadas enviadas ao dispositivo a partir de um aplicativo de back-end. A variável **delta** contém as propriedades desejadas enviadas do back-end da solução:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

O manipulador a seguir reage apenas às alterações feitas na propriedade desejada **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Manipuladores para várias propriedades

No exemplo de propriedades desejadas JSON mostrado anteriormente, o nó **clima** em **componentes** contém duas propriedades, **minTemperature** e **maxTemperature**.

Um objeto **gêmeo** local do dispositivo armazena um conjunto completo de propriedades desejadas e relatadas. A variável **delta** enviada do back-end pode atualizar apenas um subconjunto de propriedades desejadas. No snippet de código a seguir, se o dispositivo simulado receber uma atualização apenas para um dos valores **minTemperature** e **maxTemperature**, ele usará o valor no gêmeo local para o outro valor para configurar o dispositivo:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

O objeto **gêmeo** local armazena um conjunto completo de propriedades desejadas e relatadas. A variável **delta** enviada do back-end pode atualizar apenas um subconjunto de propriedades desejadas.

### <a name="handle-insert-update-and-delete-operations"></a>Manipular operações de inserção, atualização e exclusão

As propriedades desejadas enviadas do back-end não indicam qual operação está sendo executada em uma propriedade desejada específica. O código precisa inferir a operação do conjunto atual de propriedades desejadas armazenadas localmente e as alterações enviadas do hub.

O snippet de código a seguir mostra como o dispositivo simulado manipula operações de inserção, atualização e exclusão na lista de **componentes** nas propriedades desejadas. É possível ver como utilizar valores **nulos** para indicar que um componente deve ser excluído:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Enviar propriedades desejadas a um dispositivo a partir do back-end

Vimos como um dispositivo implementa manipuladores para receber atualizações de propriedade desejadas. Esta seção mostra como enviar alterações de propriedade desejadas a um dispositivo a partir de um aplicativo de back-end.

Para exibir o código de exemplo do dispositivo simulado que recebe as propriedades desejadas, navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js de exemplo transferido por download. Em seguida, abra o arquivo ServiceClient.js em um editor de texto.

O snippet de código a seguir mostra como conectar o registro de identidade do dispositivo e acessar o gêmeo para um dispositivo específico:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

O snippet de código a seguir mostra diferentes *patches* de propriedade desejada que o aplicativo de back-end envia ao dispositivo:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

O snippet de código a seguir mostra como o aplicativo de back-end envia uma atualização de propriedade desejada a um dispositivo:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Executar os aplicativos

Nesta seção, você executará dois aplicativos de exemplo para observar como um aplicativo de back-end envia atualizações de propriedades desejadas a um aplicativo de dispositivo simulado.

Para executar o dispositivo simulado e os aplicativos de back-end, serão necessários as cadeias de conexão de serviço e dispositivo. Você fez uma anotação das cadeias de conexão quando criou os recursos no início deste tutorial.

Para executar o aplicativo de dispositivo simulado, abra uma janela do prompt de comando ou shell e navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar o aplicativo de back-end, abra outra janela do prompt de comando ou shell. Em seguida, navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A captura de tela a seguir mostra a saída do aplicativo de dispositivo simulado e destaca como ele trata uma atualização para a propriedade desejada **maxTemperature**. É possível ver como o manipulador de nível superior e os manipuladores de componente de clima executam:

![Dispositivo simulado](./media/tutorial-device-twins/SimulatedDevice1.png)

A captura de tela a seguir mostra a saída do aplicativo de back-end e destaca como ele envia uma atualização para a propriedade desejada **maxTemperature**:

![Aplicativo de back-end](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Receber informações de estado

O aplicativo de back-end recebe informações de estado de um dispositivo como propriedades relatadas. Um dispositivo define as propriedades relatadas e as envia ao hub. Um aplicativo de back-end pode ler os valores atuais das propriedades relatadas do dispositivo gêmeo armazenado no hub.

### <a name="send-reported-properties-from-a-device"></a>Enviar propriedades relatadas de um dispositivo

É possível enviar atualizações para valores de propriedade relatados como um patch. O snippet de código a seguir mostra um modelo para o patch enviado pelo dispositivo simulado. O dispositivo simulado atualiza os campos no patch antes de enviá-lo ao hub:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

O dispositivo simulado usa a função a seguir para enviar o patch que contém as propriedades relatadas ao hub:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Processar propriedades relatadas

Um aplicativo de back-end acessa os valores de propriedade relatados atuais para um dispositivo por meio do dispositivo gêmeo. O snippet de código a seguir mostra como o aplicativo de back-end lê os valores da propriedade relatada do dispositivo simulado:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Executar os aplicativos

Nesta seção, você executará dois aplicativos de exemplo para observar como um aplicativo de dispositivo simulado envia atualizações de propriedades relatadas para um aplicativo de back-end.

Você executará os mesmos dois aplicativos de exemplo que você executou para ver como as propriedades desejadas são enviadas a um dispositivo.

Para executar o dispositivo simulado e os aplicativos de back-end, serão necessários as cadeias de conexão de serviço e dispositivo. Você fez uma anotação das cadeias de conexão quando criou os recursos no início deste tutorial.

Para executar o aplicativo de dispositivo simulado, abra uma janela do prompt de comando ou shell e navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar o aplicativo de back-end, abra outra janela do prompt de comando ou shell. Em seguida, navegue até a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que você baixou. Em seguida, execute os comandos a seguir:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A captura de tela a seguir mostra a saída do aplicativo de dispositivo simulado e destaca como ele envia uma atualização de propriedade relatada ao hub:

![Dispositivo simulado](./media/tutorial-device-twins/SimulatedDevice2.png)

A captura de tela a seguir mostra a saída do aplicativo back-end e destaca como ela recebe e processa uma atualização de propriedade relatada de um dispositivo:

![Aplicativo de back-end](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja concluir o próximo tutorial, deixe o grupo de recursos e o Hub IoT e reutilize-os posteriormente.

Se você não precisar mais do hub IoT, exclua-o junto com o grupo de recursos no portal. Para fazer isso, selecione o grupo de recursos **tutorial-iot-hub-rg** que contém o Hub IoT e clique em **Excluir**.

Como alternativa, use a CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a sincronizar informações de estado entre os dispositivos e o Hub IoT. Avance para o próximo tutorial para aprender como usar dispositivos gêmeos para implementar um processo de atualização de firmware.

> [!div class="nextstepaction"]
> [Implementar um processo de atualização de firmware do dispositivo](tutorial-firmware-update.md)
