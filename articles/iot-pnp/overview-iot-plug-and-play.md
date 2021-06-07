---
title: Introdução ao IoT Plug and Play | Microsoft Docs
description: Saiba mais sobre o IoT Plug and Play. O IoT Plug and Play baseia-se em uma linguagem de programação de modelagem aberta que permite aos dispositivos IoT inteligentes declarar as respectivas funcionalidades. Os dispositivos IoT apresentam essa declaração, chamada de modelo de dispositivo, quando se conectam a soluções de nuvem. A solução de nuvem pode, então, entender automaticamente o dispositivo e começar a interagir com ele, tudo sem que nenhum código seja escrito.
author: rido-min
ms.author: rmpablos
ms.date: 03/21/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 91a09db16524ebc7e4c04069b69b1c42c67538c6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739702"
---
# <a name="what-is-iot-plug-and-play"></a>O que é IoT Plug and Play?

O IoT Plug and Play permite integrar dispositivos inteligentes a soluções sem nenhuma configuração manual. No núcleo do IoT Plug and Play há um _modelo_ de dispositivo que um dispositivo usa para anunciar suas funcionalidades a um aplicativo habilitado para IoT Plug and Play. Esse modelo é estruturado como um conjunto de elementos que definem:

- _Propriedades_ que representam o estado somente leitura ou gravável de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade gravável.
- _Telemetria_, que são os dados emitidos por um dispositivo, sejam esses dados um fluxo regular de leituras de sensor, um erro ocasional ou uma mensagem informativa.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

É possível agrupar esses elementos em interfaces para reutilização entre modelos para facilitar a colaboração e acelerar o desenvolvimento.

Para fazer o IoT Plug and Play funcionar com os [Gêmeos Digitais do Azure](../digital-twins/overview.md), defina modelos e interfaces usando a [DTDL (Linguagem de Definição de Gêmeo Digital)](https://github.com/Azure/opendigitaltwins-dtdl). O IoT Plug and Play e as DTDL estão abertos para a Comunidade, e a Microsoft agradece a colaboração com clientes, parceiros e o setor. Ambos se baseiam em padrões W3C abertos, tais como JSON-LD e RDF, o que permite uma adoção mais fácil entre serviços e ferramentas.

Não há nenhum custo adicional para usar o IoT Plug and Play e o DTDL. As taxas Standard para o [Hub IoT do Azure](../iot-hub/about-iot-hub.md) e outros serviços do Azure permanecem as mesmas.

Este artigo descreve:

- As funções típicas associadas a um projeto que usa o IoT Plug and Play.
- Como usar dispositivos de IoT Plug and Play no aplicativo.
- Como desenvolver um aplicativo de dispositivo IoT compatível com IoT Plug and Play.

## <a name="user-roles"></a>Funções de usuário

O IoT Plug and Play é útil para dois tipos de desenvolvedores:

- Um _desenvolvedor de soluções_ é responsável por desenvolver uma solução de IoT usando o Hub IoT do Azure e outros recursos do Azure e por identificar dispositivos IoT a serem integrados.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo conectado à solução.

## <a name="use-iot-plug-and-play-devices"></a>Usar dispositivos IoT Plug and Play

Como desenvolvedor de soluções, você pode usar o [IoT Central](../iot-central/core/overview-iot-central.md) ou o [Hub IoT](../iot-hub/about-iot-hub.md) para desenvolver uma solução de IoT hospedada na nuvem que usa dispositivos IoT Plug and Play.

A interface do usuário da Web no IoT Central permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e dados ao longo do ciclo de vida deles. Os dispositivos IoT Plug and Play se conectam diretamente a um aplicativo IoT Central, em que você pode usar painéis personalizáveis para monitorar e controlar seus dispositivos. Você também pode usar modelos de dispositivo na interface do usuário da Web do IoT Central para criar e editar modelos DTDL.

Hub IoT – um serviço gerenciado de nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos. Ao conectar um dispositivo IoT Plug and Play a um Hub IoT, você pode usar a ferramenta [Azure IoT Explorer](./howto-use-iot-explorer.md) para ver a telemetria, as propriedades e os comandos definidos no modelo DTDL.

Se houver sensores anexados a um gateway do Windows ou do Linux, use a [ponte do IoT Plug and Play](./concepts-iot-pnp-bridge.md) para conectá-los e criar dispositivos do IoT Plug and Play sem precisar escrever o software/firmware do dispositivo (para [protocolos compatíveis](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)).

## <a name="develop-an-iot-device-application"></a>Desenvolver um aplicativo de dispositivo IoT

Como desenvolvedor de dispositivos, você pode desenvolver um produto de hardware de IoT compatível com IoT Plug and Play. O processo inclui três etapas principais:

1. Defina o modelo do dispositivo. Você cria um conjunto de arquivos JSON que definem as funcionalidades do dispositivo usando a [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Um modelo descreve uma entidade completa (assim como um produto físico) e define o conjunto de interfaces implementadas por essa entidade. Interfaces são contratos compartilhados que identificam exclusivamente a telemetria, as propriedades e os comandos compatíveis com um dispositivo. As interfaces podem ser reutilizadas em modelos diferentes.

1. Crie software de dispositivo ou firmware de uma forma que as respectivas propriedades, comandos e telemetria sigam as convenções de IoT Plug and Play. É possível simplificar a conexão dos sensores existentes anexados a um gateway do Windows ou do Linux com a [ponte do IoT Plug and Play](./concepts-iot-pnp-bridge.md).

1. O dispositivo anuncia a ID do modelo como parte da conexão MQTT. O SDK do Azure IoT inclui novos constructos para fornecer a ID do modelo no momento da conexão.

> [!Important]
> Os dispositivos IoT Plug and Play precisam usar MQTT ou MQTT sobre WebSockets. Outros protocolos, como AMQP ou HTTP, não são válidos para implementar dispositivos IoT Plug and Play.

## <a name="device-certification"></a>Certificado de dispositivo

O [programa de certificação de dispositivo do IoT Plug and Play](../certification/program-requirements-pnp.md) verifica se um dispositivo atende aos requisitos de certificação do IoT Plug and Play. Você pode adicionar um dispositivo certificado ao [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/devicecatalog).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do IoT Plug and Play, a próxima etapa sugerida é experimentar um dos guias de início rápido:

- [Conectar um dispositivo ao Hub IoT](./quickstart-connect-device.md)
- [Interagir com um dispositivo da sua solução](./quickstart-service.md)
