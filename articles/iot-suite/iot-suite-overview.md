---
title: "Visão geral do Microsoft Azure IoT Suite | Microsoft Docs"
description: "Visão geral de como o Azure IoT Suite oferece soluções pré-configuradas de Internet das coisas para coletar, analisar, armazenar dados, fornecer visualizações e integrar com outros sistemas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-iot-suite"></a>Visão geral do Azure IoT Suite

Os serviços de IoT (Internet das coisas) do Azure oferecem uma ampla variedade de recursos. Esses serviços de nível corporativo permitem que você:

* Colete dados de dispositivos
* Analise fluxos de dados em movimento
* Armazene e consulte grandes conjuntos de dados
* Visualize dados históricos e em tempo real
* Realize a integração com sistemas de back-office
* Gerenciar seus dispositivos

Para fornecer essas funcionalidades, o Azure IoT Suite reúne diversos serviços do Azure com extensões personalizadas como *soluções pré-configuradas*. Essas soluções pré-configuradas são implementações básicas dos padrões comuns de solução de IoT que ajudam a reduzir o tempo necessário para entregar suas soluções de IoT. Usando os [kits de desenvolvimento de software de IoT][lnk-sdks], você pode personalizar e estender essas soluções para atender aos seus requisitos. Você também pode usar essas soluções como exemplos ou modelos no desenvolvimento de novas soluções de IoT.

O vídeo a seguir oferece uma introdução ao Azure IoT Suite:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Serviços do Azure IoT no Azure IoT Suite
As soluções pré-configuradas normalmente usam os seguintes serviços:

* O principal do Azure IoT Suite é o serviço [Hub IoT do Azure][lnk-iot-hub]. Esse serviço fornece os recursos de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e age como o gateway para a nuvem e para outros serviços importantes do Pacote IoT. O serviço permite que você receba mensagens de seus dispositivos em escala e envie comandos para seus dispositivos. O serviço também permite que você [gerencie seus dispositivos][lnk-device-management]. Por exemplo, você pode configurar, reinicializar ou executar uma redefinição de fábrica em um ou mais dispositivos conectados ao hub.
* O [Stream Analytics do Azure][lnk-asa] fornece análise de dados em movimento. O IoT Suite usa esse serviço para processar telemetria de entrada, executar a agregação e detectar eventos. As soluções pré-configuradas também usam a análise de fluxo para processar mensagens informativas que contêm dados como metadados ou respostas de comando de dispositivos. As soluções usam o Stream Analytics para processar mensagens de seus dispositivos e entregar as mensagens para outros serviços.
* O [Armazenamento do Azure][lnk-azure-storage] e o [Azure Cosmos DB][lnk-document-db] fornecem os recursos de armazenamento de dados. As soluções pré-configuradas usam o armazenamento de blobs para armazenar telemetria e disponibilizá-la para análise. As soluções usam o Cosmos DB para armazenar os metadados de dispositivos e habilitar os recursos de gerenciamento de dispositivo das soluções.
* Os [Aplicativos Web do Azure][lnk-web-apps] e o [Microsoft Power BI][lnk-power-bi] fornecem os recursos de visualização de dados. A flexibilidade do Power BI permite que você crie seus próprios painéis interativos rapidamente por meio de dados do Pacote IoT.

Para obter uma visão geral da arquitetura de uma solução de IoT típica, consulte [Microsoft Azure e a IoT (Internet das Coisas)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluções pré-configuradas

O IoT Suite inclui soluções pré-configuradas que o habilitam a começar rapidamente e explorar os cenários comuns da IoT, como:

* Monitoramento remoto
* Manutenção preditiva
* Fábrica conectada

Você pode implantar as soluções em sua assinatura do Azure e executar um cenário IoT completo e de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do que o IoT Suite pode fazer e quais são seus principais componentes, poderá saber mais sobre as soluções pré-configuradas no IoT Suite. Para obter mais informações, consulte [Quais são as soluções pré-configuradas do Azure IoT?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
