---
title: "Soluções do Azure para a Internet das coisas (IoT Suite) | Microsoft Docs"
description: "Visão geral de IoT no Azure, incluindo uma arquitetura da solução do exemplo e como se relaciona com o Azure IoT Suite e soluções pré-configuradas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
O Microsoft Azure IoT Suite é uma solução de nível corporativo que permite a você trabalhar rapidamente por meio de um conjunto de soluções pré-configuradas extensíveis. Essas soluções abordam cenários de IoT comuns, como [monitoramento remoto][lnk-preconfigured-solutions] e [manutenção preditiva][lnk-predictive-maintenance] e [fábrica conectada][lnk-connected-factory]. Essas soluções são implementações da arquitetura da solução de IoT, descrita neste artigo.

As soluções pré-configuradas são completas, funcionais e incluem:

- Dispositivos simulados para você começar.
- Serviços do Azure pré-configurado como [Azure IoT Hub][Azure IoT Hub], [Hubs de Eventos do Azure][Azure Event Hubs], [Stream Analytics do Azure][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning], e [Armazenamento do Azure][Azure storage].
- Consoles de gerenciamento específicas à solução.

As soluções pré-configuradas contêm código comprovado e pronto para produção que você pode personalizar e estender para implementar seus próprios cenários específicos de IoT.

Talvez você também tenha interesse no serviço [Hub IoT do Azure][Azure IoT Hub], usado por muitas das soluções pré-configuradas. O [Hub IoT do Azure][Azure IoT Hub] fornece a comunicação bidirecional segura e confiável entre os dispositivos e a nuvem usados na arquitetura da solução pré-configurada.

## <a name="next-steps"></a>Próximas etapas
Explore estes recursos para saber mais sobre o IoT Suite e as soluções pré-configuradas:

* [O que é o Azure IoT Suite?][lnk-whatissuite]
* [Quais são as soluções pré-configuradas do Azure IoT Suite?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md