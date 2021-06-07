---
title: Opções da solução de IoT (Internet das Coisas) do Azure
description: Diretrizes sobre como escolher entre uma abordagem de serviços de plataforma ou de plataforma de aplicativo gerenciado para criar uma solução de IoT. A abordagem de serviço de plataforma usa serviços como o Hub IoT e os Gêmeos Digitais como blocos de construção. A abordagem de plataforma de aplicativo gerenciado usa o Azure IoT Central para uma introdução rápida.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 7ff8193e87d138878d95e783d144d445436e7a08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453002"
---
# <a name="what-is-the-right-iot-solution-for-your-business"></a>Qual é a solução de IoT certa para sua empresa?

Para criar uma solução de IoT para a sua empresa, normalmente, você opta por usar uma abordagem de *serviços de plataforma* ou de *plataforma de aplicativo gerenciado*.

Os serviços de plataforma fornecem os blocos de construção para aplicativos IoT personalizados e flexíveis. Você tem mais opções de escolha e codificação ao conectar dispositivos e ingerir, armazenar e analisar seus dados. Os serviços da plataforma IoT do Azure incluem os produtos Hub IoT do Azure e Gêmeos Digitais do Azure.

Uma plataforma de aplicativo gerenciado permite que você comece a criar aplicativos mais rapidamente do que os serviços de plataforma, reduzindo o número de decisões necessárias para alcançar os resultados. A plataforma de aplicativo gerenciado cuida da maioria dos elementos da sua solução, de modo que você possa se concentrar em adicionar conhecimento do setor, bem como dimensionar e conectar dispositivos. O Azure IoT Central é uma plataforma de aplicativo gerenciado.

Para escolher entre essas duas abordagens, você deve considerar:

- Como deseja gerenciar a sua solução.
- Qual nível de personalização e controle deseja ter sobre a solução.
- Qual estrutura de preços deseja obter.

## <a name="management"></a>Gerenciamento

Em quais áreas você deseja gastar tempo e recursos do gerenciamento do sistema? 

- Escolha a abordagem de serviços de plataforma para ter controle total sobre os serviços subjacentes em sua solução. Por exemplo, você pode desejar:

    - Gerenciar o dimensionamento e a proteção de serviços de acordo com as suas necessidades.
    - Usar o conhecimento interno ou de parceiros para integrar dispositivos e provisionar serviços.

- Escolha a abordagem de plataforma de aplicativo gerenciado para aproveitar uma plataforma que cuida da escala, da segurança e do gerenciamento de seus aplicativos e dispositivos IoT.

## <a name="control"></a>Control

Quais elementos da sua solução você deseja personalizar?

- Escolha a abordagem de serviços de plataforma para personalização e controle totais sobre a arquitetura da solução.

- Escolha a abordagem de plataforma de aplicativo gerenciado para personalizar a identidade visual, os painéis, as funções de usuário, os dispositivos e a telemetria. No entanto, você não deseja lidar com a sobrecarga de gerenciamento do sistema de IoT subjacente.

## <a name="pricing"></a>Preços

Qual estrutura de preços atende melhor às suas necessidades?

- Escolha a abordagem de serviços de plataforma para ajustar os serviços e controlar meus custos gerais.

- Escolha a abordagem de plataforma de aplicativo gerenciado para uma estrutura de preços simples e previsível.

## <a name="summary"></a>Resumo

A abordagem de serviços de plataforma é apropriada para uma empresa com conhecimento de dispositivo e solução de nuvem que deseja:

- Ajustar os serviços na solução.
- Ter um alto grau de controle sobre os serviços na solução.
- Personalizar a solução por completo.

A abordagem de plataforma de aplicativo gerenciado é apropriada para uma empresa que:

- Não deseja dedicar recursos extensivos ao design, desenvolvimento e gerenciamento do sistema.
- Desejar obter uma estrutura de preços previsível.
- Deseja obter algumas funcionalidades de personalização.

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação mais abrangente dos diferentes serviços e plataformas e como eles são usados, confira [Tecnologias e serviços de IoT do Azure](iot-services-and-technologies.md).

Para saber mais sobre os principais atributos das soluções de IoT bem-sucedidas, confira o white paper [8 atributos das soluções de IoT bem-sucedidas](https://aka.ms/8attributes).

Para ver uma análise detalhada da arquitetura IoT, confira a [Arquitetura de Referência do IoT do Microsoft Azure](/azure/architecture/reference-architectures/iot).