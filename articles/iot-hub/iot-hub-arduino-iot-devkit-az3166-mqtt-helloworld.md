---
title: Enviar mensagens para um servidor MQTT usando a biblioteca de clientes MQTT do Azure | Microsoft Docs
description: Use o DevKit como um cliente para enviar mensagens para um servidor MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369260"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas de Internet das Coisas (IoT) normalmente lidam com conexões de Internet de qualidade ruim, intermitentes ou lentas. MQTT é um protocolo de conectividade entre máquinas (M2M), que foi desenvolvido com esses desafios em mente. 

A biblioteca de clientes MQTT usada aqui é parte do projeto [PEclipse Paho](https://www.eclipse.org/paho/), que fornece APIs para usar MQTT em vários meios de transporte.

## <a name="what-you-learn"></a>O que você aprenderá

Neste projeto, você aprenderá a:
- Como usar a biblioteca de clientes MQTT para enviar mensagens para um agente MQTT.
- Como configurar seu MXChip IoT DevKit como um cliente MQTT.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

1. Se o Kit de Desenvolvimento já estiver conectado ao seu computador, desconecte-o.

2. Iniciar o VS Code.

3. Conecte o DevKit ao computador.

## <a name="open-the-mqttclient-sample"></a>Abra o exemplo MQTTClient

Expanda a seção **EXEMPLOS DO ARDUINO** no lado esquerdo, vá até **Exemplos para MXCHIP AZ3166 > MQTT** e selecione **MQTTClient**. É aberta uma nova janela do VS Code com a pasta de projeto nela.

> [!NOTE]
> Você também pode abrir o exemplo da paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Compilar e carregar o esboço do Arduino no DevKit

Digite `Ctrl+P` (macOS: `Cmd+P`) para executar `task device-upload`. Quando o upload for concluído, o DevKit será reiniciado e executará o esboço.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Você pode receber uma mensagem de erro dizendo "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte a [seção de desenvolvimento de perguntas frequentes sobre o Kit de Desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Teste do projeto

No VS Code, siga este procedimento para abrir e configurar o Serial Monitor:

1. Clique na palavra `COM[X]` na barra de status para definir a porta COM certa com `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone de tomada na barra de status para abrir o Serial Monitor: ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de status, clique no número que representa a Taxa de Baud e defina-a como `115200`: ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Serial Monitor exibe todas as mensagens enviadas pelo esboço de exemplo. O esboço conecta o DevKit ao Wi-Fi. Depois que a conexão Wi-Fi for bem-sucedida, o esboço enviará uma mensagem para o agente MQTT. Depois disso, o exemplo repetidamente envia duas mensagens "iot.eclipse.org" usando QoS 0 e QoS 1, respectivamente.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do Kit de Desenvolvedores de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Consulte também

* [Como conectar o IoT DevKit AZ3166 ao Hub IoT na nuvem](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake para um Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar seu MXChip Iot DevKit como um cliente MQTT e usar a biblioteca de clientes MQTT para enviar mensagens a um agente MQTT, aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de Monitoramento Remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao seu aplicativo Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
