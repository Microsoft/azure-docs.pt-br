---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050293"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](../articles/event-grid/edge/persist-state-linux.md) ou [persistir o estado no Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo de grade de eventos será implantado com a autenticação de cliente desativada e permitirá assinantes HTTP. Para cargas de trabalho de produção, recomendamos que você habilite somente solicitações HTTPS e assinantes com autenticação de cliente habilitada. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Navegue até seu Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **definir módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contêiner**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. Clique em **Salvar**
 1. Clique em **Avançar** para continuar na seção rotas

    > [!NOTE]
    > Se você estiver usando uma VM do Azure como um dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de instalação

 Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="review-deployment"></a>Rever implantação

1. A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Confirme que você vê os dois módulos na lista: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o runtime do IoT Edge e são padrões requeridos em toda implantação.
1. Examine as informações da implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você volta para a página do IoT Edge de seu hub IoT.
1. Selecione o **dispositivo de IOT Edge** de destino com a implantação para abrir seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo grade de eventos está listado como ambos **especificados na implantação** e **relatados pelo dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.