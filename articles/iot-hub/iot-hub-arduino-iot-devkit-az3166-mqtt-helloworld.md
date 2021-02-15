---
title: Enviar mensagens para um servidor MQTT usando a biblioteca de cliente MQTT do Azure
description: Saiba como usar a biblioteca de cliente do MQTT para enviar mensagens para um agente do MQTT. Saiba também como configurar seu mXChip IoT DevKit para ser um cliente MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151750"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas de Internet das Coisas (IoT) normalmente lidam com conexões de Internet de qualidade ruim, intermitentes ou lentas. MQTT é um protocolo de conectividade entre máquinas (M2M), que foi desenvolvido com esses desafios em mente. 

A biblioteca de clientes MQTT usada aqui é parte do projeto [PEclipse Paho](https://www.eclipse.org/paho/), que fornece APIs para usar MQTT em vários meios de transporte.

## <a name="what-you-learn"></a>O que você aprenderá

Neste projeto, você aprenderá a:
- Como usar a biblioteca de clientes MQTT para enviar mensagens para um agente MQTT.
- Como configurar seu MXChip IoT DevKit como um cliente MQTT.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](./iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

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

![Captura de tela mostra uma janela de prompt de comando que carrega e executa o esboço Arduino.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Você pode receber uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte a [seção de desenvolvimento de perguntas frequentes sobre o Kit de Desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

No VS Code, siga este procedimento para abrir e configurar o Serial Monitor:

1. Clique na `COM[X]` palavra na barra de status para definir a porta com à direita com `STMicroelectronics` : a ![ captura de tela mostra Visual Studio Code com o COM8 S T Micro Electronics selecionado.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone de plugue de energia na barra de status para abrir o monitor serial: ![ a captura de tela mostra o resumo de lançamento e o ícone de plugue de energia na barra de status.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de status, clique no número que representa a taxa de transmissão e defina-a como `115200` : a ![ captura de tela mostra a configuração da taxa de transmissão em Visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Serial Monitor exibe todas as mensagens enviadas pelo esboço de exemplo. O esboço conecta o DevKit ao Wi-Fi. Depois que a conexão Wi-Fi for bem-sucedida, o esboço enviará uma mensagem para o agente MQTT. Depois disso, o exemplo repetidamente envia duas mensagens "iot.eclipse.org" usando QoS 0 e QoS 1, respectivamente.

![Captura de tela mostra o monitor serial que exibe as mensagens enviadas pelo esboço.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do Kit de Desenvolvedores de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Veja também

* [Como conectar o IoT DevKit AZ3166 ao Hub IoT na nuvem](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake para um Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar seu MXChip IOT DevKit como um cliente MQTT e usar a biblioteca de cliente MQTT para enviar mensagens para um agente MQTT, aqui está a próxima etapa sugerida: [visão geral do acelerador de solução de monitoramento remoto do Azure IOT](/azure/iot-suite/)