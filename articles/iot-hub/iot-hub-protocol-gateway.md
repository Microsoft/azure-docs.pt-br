---
title: Gateway de protocolo IoT do Azure | Microsoft Docs
description: Como usar um gateway de protocolo IoT do Azure para estender os recursos do Hub IoT e o suporte a protocolos para permitir que dispositivos se conectem ao seu hub usando protocolos sem suporte nativo no Hub IoT.
services: iot-hub
documentationcenter: 
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: kdotchko
ms.openlocfilehash: ad2d33b0e8f02facd6f0d20bf7f5b7838747cdaf
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Suporte a protocolos adicionais para Hub IoT
O Hub IoT do Azure dá suporte nativo à comunicação pelos protocolos MQTT, AMQP e HTTPS. Em alguns casos, os dispositivos ou gateways de campo não poderão usar um desses protocolos padrão e exigirão a adaptação do protocolo. Nesses casos, você pode usar um gateway personalizado. Nesses casos, um gateway personalizado pode habilitar a adaptação de protocolo para pontos de extremidade do Hub IoT preenchendo o tráfego para e do Hub IoT. Você pode usar o [gateway de protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para habilitar a adaptação de protocolo para o Hub IoT.

## gateway de protocolo IoT do Azure
O gateway de protocolo do IoT do Azure é uma estrutura para a adaptação do protocolo feito para uma comunicação com o dispositivo bidirecional de alta escala com o Hub IoT. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo usando um protocolo específico. Ele preenche o tráfego para o Hub IoT sobre AMQP 1.0. O gateway de protocolo IoT do Azure está disponível como um projeto de software livre para flexibilizar a adição de suporte a vários protocolos e versões de protocolo.

Você pode implantar o gateway de protocolo no Azure de maneira altamente escalonável usando o Azure Service Fabric, as funções de trabalho dos Serviços de Nuvem do Azure ou as Máquinas Virtuais do Windows. Além disso, o gateway de protocolo pode ser implantado em ambientes locais, como gateways de campo.

O gateway de protocolo IoT do Azure inclui um adaptador de protocolo MQTT que permite a personalização do comportamento do protocolo MQTT, se necessário. Como o Hub IoT fornece suporte interno ao protocolo MQTT v 3.1.1, você deverá considerar o uso do adaptador de protocolo MQTT apenas se tiver a necessidade de personalizações de protocolo ou requisitos específicos para funcionalidade adicional.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do gateway de protocolo IoT do Azure permite que você conecte componentes personalizados para processamento especializados, como autenticação personalizada, transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e Hub IoT.

Para manter a flexibilidade, o gateway de protocolo e a implementação de MQTT são fornecidos em um projeto de software livre. Isso permite que você personalize a implementação conforme a necessidade.

## Próximas etapas
Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução de IoT, confira:

* [Repositório de gateway de protocolo IoT do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia de desenvolvedor do gateway do protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Comparar com Hubs de Eventos][lnk-compare]
* [Escala, alta disponibilidade e recuperação de desastre][lnk-scaling]
* [Guia do desenvolvedor do Hub IoT][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
