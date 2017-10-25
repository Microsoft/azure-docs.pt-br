---
title: Usar um gateway IoT para conectar um dispositivo ao Hub IoT do Azure | Microsoft Docs
description: Saiba como usar o Intel NUC como um gateway IoT para conectar um SensorTag da TI e enviar dados de sensor ao Hub IoT do Azure na nuvem.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gateway iot conectar dispositivo para a nuvem
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Usar o gateway IoT para conectar as coisas à nuvem – SensorTag para o Hub IoT do Azure

> [!NOTE]
> Antes de iniciar este tutorial, conclua a [Configurar o Intel NUC como um gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). Em [Configurar o Intel NUC como um gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), você configura o dispositivo Intel NUC como um gateway IoT.

## <a name="what-you-will-learn"></a>O que você aprenderá

Você aprende a usar um gateway IoT para conectar um SensorTag da Texas Instruments (CC2650STK) ao Hub IoT do Azure. O gateway IoT envia dados de temperatura e umidade coletados do SensorTag para o Hub IoT do Azure.

## <a name="what-you-will-do"></a>O que você fará

- Crie um Hub IoT.
- Registrar um dispositivo no Hub IoT para o SensorTag.
- Habilitar a conexão entre o gateway IoT e o SensorTag.
- Executar um aplicativo de exemplo BLE para enviar dados do SensorTag para o Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- Do tutorial [Configurar o Intel NUC como um gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) concluído, no qual você configura o Intel NUC como um gateway IoT.
- * Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
- Um cliente SSH executado no computador host. Recomenda-se o PuTTY no Windows. O Linux e o macOS já vêm com um cliente SSH.
- Do endereço IP e do nome de usuário e a senha para acessar o gateway no cliente SSH.
- Uma conexão com a Internet.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Aqui você registra esse novo dispositivo para o seu SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Habilitar a conexão entre o gateway IoT e o SensorTag

Nesta seção, você realiza as seguintes tarefas:

- Obtém o endereço MAC do SensorTag para a conexão Bluetooth.
- Inicia uma conexão Bluetooth do gateway IoT com o SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Obter o endereço MAC do SensorTag para a conexão Bluetooth

1. No computador host, execute o cliente SSH e conecte-se ao gateway IoT.
1. Desbloqueie o Bluetooth, executando o seguinte comando:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Inicie o serviço Bluetooth no gateway IoT e insira um shell de Bluetooth para configurar o Bluetooth, executando os seguintes comandos:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Ligue o controlador Bluetooth, executando o seguinte comando no shell do Bluetooth:

   ```bash
   power on
   ```

   ![ligar o controlador Bluetooth no gateway IoT com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Inicie a varredura por dispositivos Bluetooth próximos, executando o seguinte comando:

   ```bash
   scan on
   ```

   ![Procurar dispositivos Bluetooth próximos com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Pressione o botão de emparelhamento no SensorTag. O LED verde no SensorTag pisca.
1. No shell do Bluetooth, você verá que o SensorTag foi encontrado. Anote o endereço MAC do SensorTag. Neste exemplo, o endereço MAC do SensorTag é `24:71:89:C0:7F:82`.
1. Desative a varredura, executando o seguinte comando:

   ```bash
   scan off
   ```

   ![Interromper a varredura de dispositivos Bluetooth próximos com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Iniciar uma conexão Bluetooth do gateway IoT com o SensorTag

1. Conecte-se ao SensorTag executando o seguinte comando:

   ```bash
   connect <MAC address>
   ```

   ![Conectar ao SensorTag com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Desconecte do SensorTag e saia do shell do Bluetooth, executando os seguintes comandos:

   ```bash
   disconnect
   exit
   ```

   ![Desconectar do SensorTag com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Você habilitou com êxito a conexão entre o SensorTag e o gateway IoT.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Executar um aplicativo de exemplo BLE para enviar dados do SensorTag para o Hub IoT

O aplicativo de exemplo BLE (Bluetooth de Baixa Energia) é fornecido pelo Azure IoT Edge. O aplicativo de exemplo coleta dados da conexão BLE e os envia ao Hub IoT. Para executar o aplicativo de exemplo, você precisa:

1. Configurar o aplicativo de exemplo.
1. Executar o aplicativo de exemplo no gateway IoT.

### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Acesse a pasta do aplicativo de exemplo, executando o seguinte comando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. Abra o arquivo de configuração, executando o seguinte comando:

   ```bash
   vi ble_gateway.json
   ```

1. No arquivo de configuração, preencha os seguintes valores:

   **IoTHubName**: o nome do seu Hub IoT.

   **IoTHubSuffix**: obtenha o IoTHubSuffix da chave primária da cadeia de conexão do dispositivo que você anotou. Certifique-se de obter a chave primária da cadeia de conexão do dispositivo e não a chave primária da cadeia de conexão do seu Hub IoT. A chave primária da cadeia de conexão do dispositivo é no formato `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport**: o valor padrão é `amqp`. Este valor mostra o protocolo durante o transporte. Ele pode ser `http`, `amqp` ou `mqtt`.

   **macAddress**: o endereço MAC do SensorTag que você anotou.

   **deviceID**: ID do dispositivo que você criou em seu Hub IoT.

   **deviceKey**: a chave primária da cadeia de conexão do dispositivo.

   ![Concluir o arquivo de configuração do aplicativo BLE de exemplo](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Pressione `ESC` e digite `:wq` para salvar o arquivo.

### <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

1. Verifique se o SensorTag está ligado.
1. Execute o comando a seguir:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Próximas etapas

[Usar o gateway IoT para transformação dos dados de sensor com o Azure IoT Edge](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
