---
title: Arquitetura de referência para solução de gerenciamento de resíduos conectado criada com o Azure IoT Central | Microsoft Docs
description: Conheça os conceitos de uma solução de gerenciamento de resíduos conectado criada com o Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d6c8c693db42789a965ee896a6d913d6ee20ae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831664"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Arquitetura da referência de monitoramento de resíduos conectado 

Uma solução de gerenciamento de resíduos conectado pode ser criada usando o **modelo de aplicativo do Azure IoT Central** como um aplicativo de IoT inicial. Este artigo fornece diretrizes de arquitetura de referência de alto nível sobre a criação de uma solução de ponta a ponta. 

![Arquitetura de gerenciamento de resíduos conectado](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Conceitos:

1. Dispositivos e conectividade  
1. Central da IoT 
1. Extensibilidade e integrações
1. Aplicativos de negócios

Vamos dar uma olhada nos principais componentes que geralmente desempenham um papel em uma solução de monitoramento de consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Os dispositivos usados em ambientes abertos, como lixeiras, podem estar conectados por meio da LPWAN (redes de longa distância de baixo consumo de energia) por meio de um operador de rede de terceiros. Para esses tipos de dispositivos, use a [Ponte de Dispositivos do Azure IoT Central](../core/howto-build-iotc-device-bridge.md) para enviar os dados do seu dispositivo para o aplicativo de IoT no Azure IoT Central. Como alternativa, você pode ter gateways de dispositivo que sejam compatíveis com IP e que possam se conectar diretamente ao IoT Central.

## <a name="iot-central"></a>Central da IoT 
O Azure IoT Central é uma plataforma de aplicativo IoT que deixa você pronto para trabalhar rapidamente na sua solução de IoT. Você pode colocar sua marca, personalizar e integrar sua solução com serviços de terceiros.
Depois de conectar seus dispositivos inteligentes de água ao IoT Central, você obtém o comando e o controle do dispositivo, assim como o monitoramento e alertas, e também a interface do usuário com o RBAC interno, dashboards de insight configuráveis e opções de extensibilidade. 

## <a name="extensibility-and-integrations"></a>Extensibilidade e integrações
Você pode estender seu aplicativo de IoT no IoT Central e, opcionalmente:
* Transformar e integrar seus dados de IoT para análise avançada, por exemplo, modelos de aprendizado de máquina de treinamento, por meio de exportação de dados contínua do aplicativo do IoT Central.
* automatizar fluxos de trabalho em outros sistemas disparando ações por meio do Power Automate ou de webhooks no aplicativo IoT Central
* Acessar seu aplicativo de IoT de forma programática no IoT Central por meio de APIs do IoT Central.

## <a name="business-applications"></a>Aplicativos de negócios 
Os dados de IoT podem ser usados para alimentar uma variedade de aplicativos de negócios em uma empresa de gestão de resíduos. Para saber como conectar seu aplicativo de gerenciamento de resíduos conectado ao IoT Central a serviços de campo, siga o tutorial em [Como integrar com o Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar um aplicativo de gerenciamento de resíduos conectado](./tutorial-connected-waste-management.md) do IoT Central
* Saiba mais sobre os [modelos de governo do IoT Central](./overview-iot-central-government.md)
* Para saber mais sobre o IoT Central, confira [Visão geral do IoT Central](../core/overview-iot-central.md)
