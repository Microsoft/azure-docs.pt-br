---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b3ccc31c9159b5d7b1615add89e8fdc308bf8df
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763379"
---
### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para adicionar um dispositivo ao aplicativo, publique o modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA v2**, selecione **Publicar**.

1. Na página **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

O **Gateway de Borda de LVA v2** agora está disponível como o tipo de dispositivo a ser usado na página **Dispositivos** no aplicativo.

## <a name="migrate-the-gateway-device"></a>Migrar o dispositivo de gateway

O dispositivo **gateway-001** existente usa o modelo de dispositivo **Gateway de LVA do Edge**. Para usar o novo manifesto de implantação, migre o dispositivo para o novo modelo de dispositivo:

Para migrar o dispositivo **gateway-001**:

1. Procure a página **Dispositivos** e selecione o dispositivo **gateway-001** para destacá-lo na lista.

1. Selecione **Migrar**. Se o ícone **Migrar** não estiver visível, selecione **...** para ver mais opções.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrar o dispositivo de gateway para uma nova versão":::

1. Na lista na caixa de diálogo **Migrar**, selecione **Gateway de LVA do Edge v2** e, em seguida, selecione **Migrar**.

Após alguns segundos, a migração é concluída. Seu dispositivo agora está usando o modelo de dispositivo **LVA Edge Gateway v2** com o manifesto de implantação personalizado.

Agora, não há dispositivos usando o modelo de dispositivo **LVA Edge Gateway** original. Exclua este modelo de dispositivo:

1. Navegue até a página **Modelos de dispositivo** e selecione o modelo de dispositivo **LVA Edge Gateway**.

1. Selecione **Excluir** para excluir o modelo de dispositivo.

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Você precisará das credenciais que permitem que o dispositivo se conecte ao seu aplicativo do IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivos**, selecione o dispositivo **gateway-001**.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote no arquivo *scratchpad.txt* o **Escopo da ID**, a **Identificação do Dispositivo** e a **Chave Primária** do dispositivo. Esses valores serão usados depois.

1. Verifique se o método de conexão está definido como **Assinatura de Acesso Compartilhado**.

1. Selecione **Fechar**.

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um aplicativo do IoT Central usando o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo** e um modelo de dispositivo para o dispositivo de gateway, além de adicionar um dispositivo de gateway ao aplicativo.

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam uma VM de nuvem com fluxos de vídeo simulados:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam um dispositivo real com uma câmera **ONVIF** real:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
