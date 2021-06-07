---
title: Arquitetura de Plug and Play de IoT | Microsoft Docs
description: Como um construtor de soluções, entenda os principais elementos de arquitetura do Plug and Play IoT.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95484076"
---
# <a name="iot-plug-and-play-architecture"></a>Arquitetura do IoT Plug and Play

O IoT Plug and Play permite integrar dispositivos inteligentes a soluções sem nenhuma configuração manual. No núcleo do Plug and Play IoT, é um _modelo_ de dispositivo que descreve os recursos de um dispositivo para um aplicativo habilitado para IOT plug and Play. Esse modelo é estruturado como um conjunto de interfaces que definem:

- _Propriedades_ que representam o estado somente leitura ou gravável de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade gravável.
- _Telemetria_, que são os dados emitidos por um dispositivo, sejam esses dados um fluxo regular de leituras de sensor, um erro ocasional ou uma mensagem informativa.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

Cada modelo e interface tem uma ID exclusiva.

O diagrama a seguir mostra os principais elementos de uma solução de Plug and Play de IoT:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Arquitetura do IoT Plug and Play":::

## <a name="model-repository"></a>Repositório de modelos

O [repositório de modelo](./concepts-model-repository.md) é um repositório para definições de modelo e de interface. Você define modelos e interfaces usando o [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

A interface do usuário da Web permite que você gerencie os modelos e as interfaces.

O repositório de modelo tem controles de acesso baseados em função internos que permitem limitar o acesso às definições de interface.

## <a name="devices"></a>Dispositivos

Um construtor de dispositivos implementa o código a ser executado em um dispositivo inteligente de IoT usando um dos [SDKs do dispositivo IOT do Azure](./libraries-sdks.md). Os SDKs do dispositivo ajudam o Device Builder a:

- Conecte-se com segurança a um hub IoT.
- Registre o dispositivo com o Hub IoT e anuncie a ID do modelo que identifica a coleção de interfaces DTDL que o dispositivo implementa.
- Sincronize as propriedades definidas nas interfaces DTDL entre o dispositivo e o Hub IoT.
- Adicione manipuladores de comandos para os comandos definidos nas interfaces DTDL.
- Enviar telemetria para o Hub IoT.

## <a name="iot-edge-gateway"></a>IoT Edge gateway

Um gateway de IoT Edge atua como um intermediário para conectar dispositivos de IoT Plug and Play que não podem se conectar diretamente a um hub IoT. Para saber mais, veja [como um dispositivo de IOT Edge pode ser usado como um gateway](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Um _módulo IOT Edge_ permite implantar e gerenciar a lógica de negócios na borda. Os módulos do Azure IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (por exemplo, o Azure Stream Analytics) ou seu próprio código específico à solução.

O _Hub de IOT Edge_ é um dos módulos que compõem o tempo de execução Azure IOT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (sejam dispositivos ou módulos) podem se conectar no runtime do IoT Edge como faria para no Hub IoT.

Os SDKs do dispositivo ajudam um construtor de módulo a:

- Use o Hub de IoT Edge para se conectar com segurança ao Hub IoT.
- Registre o módulo com o Hub IoT e anuncie a ID do modelo que identifica a coleção de interfaces DTDL que o dispositivo implementa.
- Sincronize as propriedades definidas nas interfaces DTDL entre o dispositivo e o Hub IoT.
- Adicione manipuladores de comandos para os comandos definidos nas interfaces DTDL.
- Enviar telemetria para o Hub IoT.

## <a name="iot-hub"></a>Hub IoT

O [Hub IOT](../iot-hub/about-iot-hub.md) é um serviço hospedado na nuvem que atua como um hub de mensagens central para comunicação bidirecional entre sua solução de IOT e os dispositivos que ele gerencia.

Um hub IoT:

- Torna a ID do modelo implementada por um dispositivo disponível para uma solução de back-end.
- Mantém a conexão digital associada a cada dispositivo de Plug and Play conectado ao Hub.
- Encaminha fluxos de telemetria para outros serviços para processamento ou armazenamento.
- Roteia eventos de alteração de troca digital para outros serviços para habilitar o monitoramento de dispositivo.

## <a name="backend-solution"></a>Solução de back-end

Uma solução de back-end monitora e controla dispositivos conectados interagindo com gêmeos digital no Hub IoT. Use um dos SDKs de serviço para implementar sua solução de back-end. Para entender os recursos de um dispositivo conectado, o back-end da solução:

1. Recupera a ID do modelo que o dispositivo registrou com o Hub IoT.
1. Usa a ID do modelo para recuperar as definições de interface de qualquer repositório de modelo.
1. Usa o analisador de modelo para extrair informações das definições de interface.

A solução de back-end pode usar as informações das definições de interface para:

- Ler valores de propriedade relatados pelos dispositivos.
- Atualizar propriedades graváveis em um dispositivo.
- Chamar comandos implementados por um dispositivo.
- Entenda o formato da telemetria enviada por um dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral da arquitetura de uma solução de Plug and Play de IoT, as próximas etapas são para saber mais sobre:

- [O repositório de modelos](./concepts-model-repository.md)
- [Integração do modelo de entrelaçamento digital](./concepts-model-discovery.md)
- [Desenvolvendo para Plug and Play de IoT](./concepts-developer-guide-device.md)
