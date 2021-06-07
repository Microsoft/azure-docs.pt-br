---
title: Tutorial – Criar um aplicativo de detecção de objetos e movimentos e análise de vídeo no Azure IoT Central (YOLO v3)
description: Este tutorial mostra como criar um aplicativo de análise de vídeo no IoT Central. Você vai criá-lo, personalizá-lo e conectá-lo a outros serviços do Azure. Este tutorial usa o sistema de detecção de objetos em tempo real do YOLO v3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 70769ec97affcd95d07a1365d6357262072c86ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832056"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Tutorial: Criar um aplicativo de detecção de objetos e movimentos e análise de vídeo no Azure IoT Central (YOLO v3)

Como construtor de soluções, saiba como criar um aplicativo de análise de vídeo com o modelo de aplicativo de *detecção de objetos e movimentos e análise de vídeo* do IoT Central, os dispositivos Azure IoT Edge, os Serviços de Mídia do Azure e o sistema de detecção de objetos e de movimento em tempo real do YOLO v3. A solução usa uma loja de varejo para mostrar como atender à necessidade comercial comum de monitorar câmeras de segurança. A solução usa a detecção automática de objetos em um feed de vídeo para identificar e localizar rapidamente eventos interessantes.

> [!TIP]
> Para usar o OpenVINO&trade;, em vez do YOLO v3 para a detecção de objetos e de movimento, confira [Tutorial: Criar um aplicativo de detecção de objetos e movimentos e análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – esse arquivo ajuda você a registrar as diversas opções de configuração necessárias à medida que trabalha nesses tutoriais.
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) – você só precisa baixar esse arquivo se planeja usar o dispositivo Intel NUC no segundo tutorial.

> [!NOTE]
> O repositório GitHub também inclui o código-fonte dos módulos **LvaEdgeGatewayModule** e **lvaYolov3** do IoT Edge. Para obter mais informações sobre como trabalhar com o código-fonte, confira [Criar os módulos do Gateway de LVA](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Implante um módulo do IoT Edge usando um manifesto de implantação. No IoT Central, você pode importar o manifesto como um modelo de dispositivo e permitir que o IoT Central gerenciar a implantação do módulo.

Para preparar o manifesto de implantação:

1. Abra o arquivo *deployment.amd64.json*, que você salvou na pasta *lva-configuration*, usando um editor de texto.

1. Localize as configurações do `LvaEdgeGatewayModule` e verifique se o nome da imagem é igual ao mostrado no seguinte snippet:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adicione o nome da sua conta dos Serviços de Mídia ao nó `env` da seção `LvaEdgeGatewayModule`. Você tomou nota do nome da conta dos Serviços de Mídia no arquivo *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. O modelo não expõe essas propriedades no IoT Central; portanto, você precisa adicionar os valores de configuração dos Serviços de Mídia ao manifesto de implantação. Localize o módulo `lvaEdge` e substitua os espaços reservados pelos valores que você anotou no arquivo *scratchpad.txt* quando criou sua conta dos Serviços de Mídia.

    A `azureMediaServicesArmId` é a **ID do Recurso** que você anotou no arquivo *scratchpad.txt* quando criou a conta dos Serviços de Mídia.

    A seguinte tabela mostra os valores de **Conectar-se à API dos Serviços de Mídia (JSON)** no arquivo *scratchpad.txt* que você deve usar no manifesto de implantação:

    | Manifesto de implantação       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Use a tabela anterior para adicionar os valores corretos no manifesto de implantação, caso contrário, o dispositivo não funcionará.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Salve as alterações.

Este tutorial configura sua solução para usar o módulo YOLO v3 para detecção de objetos e de movimento. O seguinte snippet mostra a configuração do módulo:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Substituir o manifesto

Na página **Gateway de Borda de LVA v2**, selecione **+ Substituir manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Substituir manifesto":::

Procure a pasta *lva-configuration* e selecione o arquivo de manifesto *deployment.amd64.json* editado anteriormente. Escolha **Carregar**. Após a conclusão da validação, selecione **Substituir**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver concluído o aplicativo, remova todos os recursos criados da seguinte maneira:

1. No aplicativo do IoT Central, procure a página **Seu aplicativo** na seção **Administração**. Em seguida, selecione **Excluir**.
1. No portal do Azure, exclua o grupo de recursos **lva-rg**.
1. No computador local, pare o contêiner **amp-viewer** do Docker.

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um aplicativo do IoT Central usando o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo** e um modelo de dispositivo para o dispositivo de gateway, além de adicionar um dispositivo de gateway ao aplicativo.

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam uma VM de nuvem com fluxos de vídeo simulados:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](tutorial-video-analytics-iot-edge-vm.md)

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam um dispositivo real com uma câmera **ONVIF** real:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)