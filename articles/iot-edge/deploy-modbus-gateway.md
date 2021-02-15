---
title: Converter os protocolos modbus com gateways – Azure IoT Edge | Microsoft Docs
description: Permitir que os dispositivos que usam o Modbus TCP se comuniquem com o Hub IoT do Azure por meio da criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: d07a1d1ab0d3b1f4315c09f1c403126139b34612
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043894"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Conectar dispositivos Modbus TCP por meio de um gateway de dispositivo do IoT Edge

Se desejar conectar dispositivos IoT que usam protocolos TCP ou RTU Modbus a um hub IoT do Azure, você poderá usar um dispositivo IoT Edge como um gateway. O dispositivo de gateway lê os dados dos dispositivos Modbus e comunica esses dados à nuvem usando um protocolo com suporte.

![Dispositivos Modbus se conectam ao Hub IoT por meio do gateway de IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo trata de como criar sua própria imagem de contêiner para um módulo Modbus (ou você pode usar um exemplo pré-compilado) e, em seguida, implantá-lo no dispositivo IoT Edge que atuará como seu gateway.

Este artigo pressupõe que você está usando o protocolo TCP Modbus. Para obter mais informações sobre como configurar o módulo para compatibilidade com o Modbus RTU, confira o projeto do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo do Azure IoT Edge. Para obter instruções sobre como configurar um, consulte [implantar Azure IOT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.
* Um dispositivo Modbus simulado ou físico que oferece suporte ao Modbus TCP. Você precisará saber seu endereço IPv4.

## <a name="prepare-a-modbus-container"></a>Preparar um contêiner Modbus

Se você quiser testar a funcionalidade de gateway Modbus, a Microsoft tem um exemplo de módulo que pode usar. Você pode acessar o módulo no Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com o URI da imagem, `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Se quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, haverá um projeto de software livre do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub. Siga as orientações no projeto para criar sua própria imagem de contêiner. Para criar uma imagem de contêiner, consulte [desenvolver módulos C# no Visual Studio](./how-to-visual-studio-develop-module.md) ou [desenvolver módulos no Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre como criar novos módulos e publicar imagens de contêiner em um registro.

## <a name="try-the-solution"></a>Experimente a solução

Esta seção percorre a implantação do módulo Modbus de exemplo da Microsoft em seu dispositivo IoT Edge.

1. No [Portal do Azure](https://portal.azure.com/), vá ao hub IoT.

2. Acesse **IoT Edge** e clique no dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Na seção **módulos IOT Edge** , adicione o módulo Modbus:

   1. Clique na lista suspensa **Adicionar** e selecione **módulo Marketplace**.
   2. Procure `Modbus` e selecione o **módulo TCP do Modbus** da Microsoft.
   3. O módulo é configurado automaticamente para o Hub IoT e aparece na lista de módulos IoT Edge. As rotas também são configuradas automaticamente. Selecione **Examinar + criar**.
   4. Examine o manifesto de implantação e selecione **criar**.

5. Selecione o módulo MODBUS, `ModbusTCPModule` , na lista e selecione a guia **configurações de configuração do módulo** . O JSON necessário para as propriedades desejadas do módulo para cima é populado automaticamente.

6. Procure a propriedade **SlaveConnection** no JSON e defina seu valor como o endereço IPv4 do seu dispositivo Modbus.

7. Selecione **Atualizar**.

8. Selecione **revisão + criar**, examine a implantação e, em seguida, selecione **criar**.

9. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Você deve ver o novo `ModbusTCPModule` módulo em execução junto com o IOT Edge Runtime.

## <a name="view-data"></a>Exibir dados

Exiba os dados provenientes do módulo Modbus:

```cmd/sh
iotedge logs modbus
```

Você também pode exibir a telemetria que o dispositivo está enviando usando a [extensão do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, extensão do kit de ferramentas do Azure IOT).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como IoT Edge dispositivos podem atuar como gateways, consulte [criar um dispositivo de IOT Edge que atue como um gateway transparente](./how-to-create-transparent-gateway.md).
* Para obter mais informações sobre como IoT Edge módulos funcionam, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md).