---
title: Gateway de protocolo IoT do Azure | Microsoft Docs
description: Como usar um gateway de protocolo IoT do Azure para estender os recursos do Hub IoT e o suporte a protocolos para permitir que dispositivos se conectem ao seu hub usando protocolos sem suporte nativo no Hub IoT.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325025"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Suporte a protocolos adicionais para Hub IoT

O Hub IoT do Azure dá suporte nativo à comunicação pelos protocolos MQTT, AMQP e HTTPS. Em alguns casos, os dispositivos ou gateways de campo poderão não conseguir usar um desses protocolos padrão e exigirão a adaptação do protocolo. Nesses casos, você pode usar um gateway personalizado. Um gateway personalizado habilita a adaptação de protocolo para pontos de extremidade do Hub IoT conectando o tráfego de e para o Hub IoT. Você pode usar o [gateway de protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para habilitar a adaptação de protocolo para o Hub IoT.

## <a name="azure-iot-protocol-gateway"></a>gateway de protocolo IoT do Azure

O gateway de protocolo do IoT do Azure é uma estrutura para a adaptação do protocolo feito para uma comunicação com o dispositivo bidirecional de alta escala com o Hub IoT. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo usando um protocolo específico. Ele preenche o tráfego para o Hub IoT sobre AMQP 1.0.

Você pode implantar o gateway de protocolo no Azure de maneira altamente escalonável usando o Azure Service Fabric, as funções de trabalho dos Serviços de Nuvem do Azure ou as Máquinas Virtuais do Windows. Além disso, o gateway de protocolo pode ser implantado em ambientes locais, como gateways de campo.

O gateway de protocolo IoT do Azure inclui um adaptador de protocolo MQTT que permite a personalização do comportamento do protocolo MQTT, se necessário. Como o Hub IoT fornece suporte interno ao protocolo MQTT v 3.1.1, você deverá considerar o uso do adaptador de protocolo MQTT apenas se tiver a necessidade de personalizações de protocolo ou requisitos específicos para funcionalidade adicional.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do gateway de protocolo IoT do Azure permite que você conecte componentes personalizados para processamento especializados, como autenticação personalizada, transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e Hub IoT.

Para a flexibilidade, o gateway de protocolo de IoT do Azure e a implementação de MQTT são fornecidos em um projeto de software livre. Você pode usar o projeto de software livre para adicionar suporte para vários protocolos e versões de protocolo ou personalizar a implementação para seu cenário. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução de IoT, confira:

* [Repositório de gateway de protocolo IoT do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Guia de desenvolvedor do gateway do protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Comparar com Hubs de Eventos](iot-hub-compare-event-hubs.md)

* [Colocação em escala, alta disponibilidade e recuperação de desastres](iot-hub-scaling.md)

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)