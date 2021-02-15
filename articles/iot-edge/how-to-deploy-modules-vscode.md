---
title: Implantar módulos do Visual Studio Code – Azure IoT Edge
description: Use o Visual Studio Code com o Azure IoT Tools para efetuar push de um módulo do IoT Edge de seu Hub IoT para o dispositivo do IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a6415aa55a67b37d9564398eb77dacb48cf16f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378104"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implantar módulos do Azure IoT Edge do Visual Studio Code

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os.

Este artigo mostra como criar um manifesto de implantação JSON e usar esse arquivo para enviar a implantação a um dispositivo do IoT Edge. Para obter informações sobre como criar uma implantação voltada para vários dispositivos com base nas marcações compartilhadas, confira [Implantar módulos do IoT Edge em escala usando o Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
* Um dispositivo IoT Edge

  Se você não tiver um dispositivo IoT Edge configurado, poderá criar um em uma máquina virtual do Azure. Siga as etapas em um dos artigos de início rápido para [criar um dispositivo Linux Virtual](quickstart-linux.md) ou [criar um dispositivo virtual do Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando o Visual Studio Code, salve o manifesto de implantação localmente como um arquivo .JSON. Você usará o caminho do arquivo na próxima seção quando executar o comando para aplicar a configuração ao dispositivo.

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

>[!NOTE]
>Este manifesto de implantação de exemplo usa a versão de esquema 1,1 para o agente de IoT Edge e o Hub. A versão de esquema 1,1 foi lançada junto com IoT Edge versão 1.0.10 e habilita recursos como ordem de inicialização de módulo e priorização de rota.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, é necessário entrar na conta do Azure e selecionar o hub IoT em que está trabalhando.

1. No Visual Studio Code, abra a exibição do **Explorer**.

1. Na parte inferior do Explorer, expanda a seção **Hub IoT**.

   ![Expandir a seção Hub IoT do Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique em **...** no cabeçalho da seção **Hub IoT**. Se você não visualizar as reticências, passe o mouse sobre o cabeçalho.

1. Escolha **Selecionar Hub IoT**.

1. Se não estiver conectado à conta do Azure, siga as instruções para fazer isso.

1. Selecione sua assinatura do Azure.

1. Selecione seu Hub IoT.

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

Você implanta módulos no dispositivo, aplicando o manifesto de implantação configurado com as informações do módulo.

1. Na exibição do explorador do Visual Studio Code, expanda a seção **Hub IoT do Azure** e, em seguida, expanda o nó **Dispositivos**.

1. Clique com o botão direito do mouse no dispositivo IoT Edge que você quer configurar com o manifesto de implantação.

    > [!TIP]
    > Para confirmar que o dispositivo que você escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos IoT Edge incluem esses dois módulos.

1. Selecione **Criar implantação para dispositivo único**.

1. Navegue até o arquivo JSON do manifesto de implantação que você quer usar e clique em **Selecionar manifesto de implantação do Edge**.

   ![Selecionar manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados da implantação são impressos na saída do VS Code. Implantações com êxito são aplicadas em poucos minutos se o dispositivo de destino estiver em execução e conectado à Internet.

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, você verá todos eles na seção **Hub IoT do Azure**. Selecione a seta ao lado do dispositivo do IoT Edge para expandi-lo. Todos os módulos atualmente em execução serão exibidos.

Se você implantou recentemente novos módulos em um dispositivo, passe o mouse sobre o cabeçalho da seção **Dispositivos do Hub IoT** e selecione o ícone de atualização para atualizar a exibição.

Clique com o botão direito do mouse no nome de um módulo para exibir e editar o módulo gêmeo.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala usando o Visual Studio Code](how-to-deploy-at-scale.md)
