---
title: Visão geral da simulação de dispositivo – Azure | Microsoft Docs
description: Uma descrição do acelerador de solução de simulação de dispositivo e suas funcionalidades.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448438"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Visão geral do acelerador de solução da Simulação de Dispositivo

Em uma solução de IoT baseada em nuvem, seus dispositivos se conectam a um ponto de extremidade de nuvem para enviar telemetria, como temperatura, local e status. Sua solução consome essa telemetria, permitindo que você execute ações ou derive insights dela.

Ao desenvolver uma solução de IoT, teste e experimentação são partes essenciais do processo. A simulação é uma ferramenta importante ao longo desse processo. Com a Simulação de Dispositivo, você pode:

* Colocar um protótipo rapidamente em funcionamento e, em seguida, iterar ajustando comportamento do dispositivo simulado rapidamente. Esse processo permite que você verifique a ideia antes de investir em hardware caro. Você pode criar dispositivos personalizados por meio da interface do usuário Web para gerar um protótipo de dispositivo em segundos.
* Valide se a solução funciona conforme o esperado, desde o dispositivo até a solução, simulando comportamentos reais do dispositivo. Você pode criar scripts complexos de comportamentos de dispositivo usando o JavaScript para gerar telemetria simulada realística.
* Teste em escala sua solução simulando condições de carga normal, de pico e além de pico. Testes de escala também ajudam você a dimensionar corretamente os recursos do Azure necessários para executar sua solução.

![Simulação de drone de exemplo](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Com a simulação de dispositivo, você pode definir modelos de dispositivo para simular os dispositivos reais. Esse modelo inclui formatos de mensagem, propriedades gêmeas e métodos. Você também pode simular comportamentos complexos de dispositivo com JavaScript.

Você pode executar simulações para um ou milhares de dispositivos que se conectam a qualquer hub IoT. Para ajudar com o teste, você pode, opcionalmente, implantar um hub IoT junto com a simulação de dispositivo para um ambiente autônomo.

A Simulação de Dispositivo é gratuita. No entanto, a Simulação de Dispositivo implanta sua assinatura do Azure na nuvem e consome recursos do Azure. Se a simulação de dispositivo não atender às suas necessidades, o [código-fonte também estará disponível no GitHub](https://github.com/Azure/device-simulation-dotnet) para que você possa copiar e modificar.

## <a name="sample-simulations"></a>Simulações de exemplo

Quando você implanta a simulação de dispositivo, obtém alguns dispositivos e simulações de exemplo. Você pode usar esses exemplos para aprender a usar a simulação de dispositivo. Para começar, execute uma [simulação de exemplo que simule 10 caminhões](quickstart-device-simulation-deploy.md). Você também pode [criar sua própria simulação usando um dos muitos dispositivos de exemplo fornecidos](iot-accelerators-device-simulation-create-simulation.md).

![Configuração da simulação](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>Dispositivos simulados personalizados

Você pode usar Simulação de Dispositivo para [criar modelos de dispositivo personalizados](iot-accelerators-device-simulation-create-custom-device.md) para usar em suas simulações. Por exemplo, você pode definir um novo modelo de dispositivo de refrigerador que envie telemetria de temperatura e umidade. Dispositivos simulados personalizados são ideais para comportamentos de dispositivo simples com valores de telemetria aleatórios, incrementais ou decrementais.

![Criar modelo de dispositivo](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Dispositivos simulados avançados

Quando você precisa de mais controle sobre os valores de telemetria que envia um dispositivo, pode usar um modelo de dispositivo avançado. Modelos de dispositivo avançados permitem o suporte JavaScript para manipular os valores de telemetria enviados. Por exemplo, você pode simular a temperatura do interior de um carro estacionado em um dia quente e ensolarado: conforme a temperatura externa aumenta, a temperatura interna aumenta exponencialmente.

Modelos de dispositivo avançados permitem [criar e carregar seus próprios modelos de dispositivo](iot-accelerators-device-simulation-advanced-device.md), que consistem em um arquivo de definição de dispositivo JSON e os arquivos JavaScript correspondentes.

Modelos de dispositivo avançados permitem:

* Especificar o formato de mensagem enviado do dispositivo, juntamente com os tipos de telemetria.
* Usar scripts personalizados para gerar valores de telemetria que mantêm o estado do dispositivo ao longo do tempo.
* Usar scripts personalizados para especificar como o dispositivo simulado responde aos métodos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o acelerador de solução de Simulação de Dispositivo e seus recursos. Para começar a usar o acelerador de solução, vá para o início rápido:

> [!div class="nextstepaction"]
> [Implantar e executar uma simulação de dispositivo IoT no Azure](quickstart-device-simulation-deploy.md)
