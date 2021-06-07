---
title: Introdução ao desenvolvimento de dispositivos e aplicativos de Internet das Coisas do Azure
description: Saiba como usar o Azure IoT para realizar o desenvolvimento de dispositivos inseridos e criar aplicativos de nuvem habilitados para dispositivos.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654675"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>O que é o desenvolvimento de aplicativo e de dispositivo Azure IoT?

A Internet das Coisas do Azure é uma coleção de serviços gerenciados e de plataforma que conectam, monitoram e controlam seus dispositivos IoT. A Internet das Coisas do Azure oferece aos desenvolvedores um conjunto abrangente de opções. Suas opções incluem plataformas de dispositivo, serviços de nuvem com suporte, SDKs e ferramentas para a criação de aplicativos de nuvem habilitados para dispositivos.

Este artigo fornece uma visão geral de diversos aspectos importantes para desenvolvedores que estão começando a usar a Internet das Coisas do Azure. Esses conceitos orientarão você, como desenvolvedor de dispositivos IoT, com relação às opções da Internet das Coisas do Azure e a como começar. Especificamente, o artigo oferece uma visão geral destes conceitos:
- [Noções básicas das funções de desenvolvimento de dispositivo](#device-development-roles)
- [Escolhendo o hardware](#choosing-your-hardware)
- [Escolhendo um SDK](#choosing-an-sdk)
- [Selecionando opções de conexão](#selecting-connection-options)

## <a name="device-development-roles"></a>Funções de desenvolvimento de dispositivo
Este artigo aborda duas funções comuns que você pode observar entre desenvolvedores de dispositivos. Da maneira usada aqui, uma função é uma coleção de tarefas de desenvolvimento relacionadas. É útil entender com que tipo de função de desenvolvimento você está trabalhando. Sua função afeta muitas escolhas de desenvolvimento que você faz.

* **Desenvolvimento de aplicativos de dispositivo:** alinha-se a práticas de desenvolvimento modernas, tem como alvo muitas das linguagens de ordem superior e é executado em um sistema operacional de uso geral, como Windows ou Linux.

* **Desenvolvimento de dispositivos inseridos:** descreve o desenvolvimento voltado a dispositivos com restrições de recursos. Normalmente, um dispositivo com restrições de recursos é usado para reduzir custos unitários, o consumo de energia ou o tamanho do dispositivo. Esses dispositivos têm controle direto sobre a plataforma de hardware em que são executados.

### <a name="device-application-development"></a>Desenvolvimento de aplicativos de dispositivo
Desenvolvedores de aplicativos de dispositivo estão adaptando dispositivos existentes para que se conectem à nuvem e se integrem às suas soluções de IoT. Esses dispositivos podem dar suporte a linguagens de ordem superior, como C# ou Python, e muitas vezes dão suporte a um sistema operacional de uso geral robusto, como Windows ou Linux. Dispositivos de destino comuns incluem PCs, contêineres, Raspberry Pis e dispositivos móveis. 

Em vez de desenvolver dispositivos restritos em escala, esses desenvolvedores se concentram em habilitar um cenário de IoT específico exigido por sua solução de nuvem. Alguns deles também trabalham em dispositivos restritos para sua solução de nuvem. Para desenvolvedores que trabalham com dispositivos restritos, confira o caminho [Desenvolvimento de dispositivos inseridos](#embedded-device-development) abaixo.

> [!TIP]
> Confira os [SDKs de dispositivos irrestritos](about-iot-sdks.md#unconstrained-device-sdks) para começar.

### <a name="embedded-device-development"></a>Desenvolvimento de dispositivos inseridos
O desenvolvimento inserido é destinado a dispositivos restritos com memória e processamento limitados. Dispositivos restritos restringem o que pode ser realizado em comparação com uma plataforma de desenvolvimento tradicional.

Normalmente, dispositivos inseridos usam um RTOS (sistema operacional em tempo real) ou nenhum sistema operacional. Os dispositivos inseridos têm controle total sobre seu hardware devido à falta de um sistema operacional de uso geral. Isso faz deles uma boa opção para sistemas em tempo real.

Os SDKs inseridos atuais têm como destino a linguagem **C**. Os SDKs inseridos não fornecem nenhum sistema operacional ou dão suporte ao Azure RTOS. Eles são projetados tendo os destinos inseridos em mente. As considerações de design incluem a necessidade de um volume mínimo e um design de alocação sem memória.

Se o dispositivo pode executar um sistema operacional de uso geral, é recomendável seguir o caminho [Desenvolvimento de aplicativos de dispositivo](#device-application-development). Ele fornece um conjunto mais rico de opções de desenvolvimento.

> [!TIP]
> Confira os [SDKs de dispositivos restritos](about-iot-sdks.md#constrained-device-sdks) para começar.

## <a name="choosing-your-hardware"></a>Escolhendo o hardware
Os dispositivos de Internet das Coisas do Azure são os blocos de construção básicos de uma solução de IoT e são responsáveis por observar e interagir com seu ambiente. Há muitos tipos diferentes de dispositivos de IoT, e é útil entender os tipos de dispositivos que existem e como eles podem afetar o processo de desenvolvimento.

Para obter mais informações sobre a diferença entre os tipos de dispositivos abordados neste artigo, leia [Sobre os tipos de dispositivo de IoT](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Escolhendo um SDK
Como desenvolvedor de dispositivos de Internet das Coisas do Azure, você tem um conjunto diversificado de SDKs de dispositivo e SDKs de serviço do Azure que ajudam a criar aplicativos de nuvem habilitados para dispositivos. Os SDKs simplificam o esforço de desenvolvimento e grande parte da complexidade da conexão e do gerenciamento de dispositivos. 

Conforme indicado na seção [Funções de desenvolvimento de dispositivos](#device-development-roles), há três tipos de SDKs de IoT para o desenvolvimento de dispositivos:
- SDKs de dispositivo inseridos (para dispositivos restritos)
- SDKs de dispositivo (para usar linguagens de ordem superior para conectar dispositivos existentes a aplicativos de IoT)
- SDKs de serviço (para criar soluções de Internet das Coisas do Azure que conectam dispositivos a serviços)

Para saber mais sobre como escolher um SDK de serviço ou de dispositivo de Internet das Coisas do Azure, confira [Visão geral dos SDKs do Dispositivo de Internet das Coisas do Azure](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Selecionando opções de conexão
Uma etapa importante do processo de desenvolvimento é escolher o conjunto de opções que você usará para conectar e gerenciar seus dispositivos. Há dois aspectos críticos a serem considerados:
- Escolher uma plataforma de aplicativo IoT para hospedar seus dispositivos. Para a Internet das Coisas do Azure, isso significa escolher o Hub IoT ou o IoT Central.
- Escolher ferramentas de desenvolvedor para ajudar você a conectar, gerenciar e monitorar dispositivos.

Para saber mais sobre como selecionar uma plataforma e ferramentas de aplicativo, confira [Visão geral: opções de conexão para desenvolvedores de dispositivos da Internet das Coisas do Azure](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Próximas etapas
Selecione uma das séries de inícios rápidos a seguir que for mais relevante para sua função de desenvolvimento. Esses artigos demonstram as noções básicas de como criar um aplicativo da Internet das Coisas do Azure para hospedar dispositivos, usar um SDK, conectar um dispositivo e enviar telemetria.  
- Para desenvolvimento de aplicativos de dispositivo: [Início rápido: enviar telemetria de um dispositivo para o Azure IoT Central](quickstart-send-telemetry-python.md)
- Para desenvolvimento de dispositivos inseridos: [Introdução ao desenvolvimento de dispositivos inseridos da Internet das Coisas do Azure](quickstart-device-development.md)
