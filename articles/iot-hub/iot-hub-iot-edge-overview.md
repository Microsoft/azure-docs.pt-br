---
title: "Visão geral do Azure IoT Edge | Microsoft Docs"
description: "Descreve os principais conceitos da arquitetura no Azure IoT Edge, como agentes, módulos e gateways."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Conceitos de arquitetura do Azure IoT Edge

Antes de examinar algum código de exemplo ou criar seu próprio gateway de campo usando o IoT Edge, é necessário entender os principais conceitos nos quais se baseia arquitetura do SDK.

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Você cria um gateway com o Azure IoT Edge criando e montando *módulos do IoT Edge*. Os módulos usam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, executa alguma ação nela, opcionalmente, a transforma em uma nova mensagem e a publica para ser processada por outros módulos. Alguns módulos poderão gerar apenas novas mensagens e nunca processar as mensagens de entrada. Uma cadeia de módulos cria um pipeline de processamento de dados com cada módulo executando uma transformação nos dados em um ponto do pipeline.

![Uma cadeia de módulos no gateway, compilada com o IoT Edge do Azure][1]

O IoT Edge contém os seguintes componentes:

* Módulos pré-gravados que executam funções comuns de gateway.
* As interfaces que um desenvolvedor pode usar para gravar módulos personalizados.
* A infraestrutura necessária para implantar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que permite criar gateways para serem executados em vários sistemas operacionais e plataformas.

![Camada de abstração do IoT Edge do Azure][2]

## <a name="messages"></a>Mensagens

Mesmo que pensar sobre os módulos que transmitem mensagens entre si seja uma maneira conveniente de conceitualizar o funcionamento de um gateway, ela não reflete com precisão o que acontece. Módulos do IoT Edge usam um agente para se comunicarem entre si. Os módulos publicam mensagens para o agente (usando padrões de mensagens, como barramento ou publicação/assinatura) e permitem que o agente direcione a mensagem para os módulos conectados a ele.

Um módulo usa a função **Broker_Publish** para publicar uma mensagem para o agente. O agente entrega as mensagens para um módulo, invocando uma função de retorno de chamada. Uma mensagem consiste em um conjunto de propriedades de chave/valor e no conteúdo transmitido como um bloco de memória.

![A função do Agente no IoT Edge do Azure][3]

## <a name="message-routing-and-filtering"></a>Roteamento e filtragem de mensagens

Há duas maneiras de direcionar mensagens para os módulos corretos do IoT Edge:

* Você pode transmitir um conjunto de links para o agente para que este saiba a origem e o coletor de cada módulo.
* Um módulo pode filtrar as propriedades da mensagem.

Um módulo só deve agir em relação a uma mensagem se a mensagem é destinada a ele. Os links e a filtragem de mensagens criam efetivamente um pipeline de mensagem.

## <a name="next-steps"></a>Próximas etapas

Para ver esses conceitos aplicados em um exemplo que você pode executar, consulte [Explorar arquitetura do Azure IoT Edge][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
