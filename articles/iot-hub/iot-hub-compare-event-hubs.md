---
title: Comparar o Hub IoT do Azure ao Hubs de Eventos do Azure | Microsoft Docs
description: Uma comparação dos serviços do Hub IoT e de Eventos do Azure destacando diferenças funcionais e casos de uso. A comparação inclui protocolos com suporte, gerenciamento de dispositivos, monitoramento e uploads de arquivos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735499"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Conectar dispositivos IoT ao Azure: Hub IoT e Hubs de Eventos

O Azure fornece serviços desenvolvidos especificamente para diferentes tipos de conectividade e comunicação para ajudá-lo a conectar seus dados ao poder da nuvem. Tanto o Hub IoT como os Hubs de Eventos do Azure são serviços de nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para informações de negócios. Os dois serviços são semelhantes, pois ambos dão suporte à ingestão de dados com baixa latência e alta confiabilidade, mas eles são projetados para finalidades diferentes. O IoT Hub foi desenvolvido para atender aos requisitos exclusivos de conectar dispositivos IoT na nuvem do Azure, enquanto os Hubs de eventos foi projetado para streaming de grandes dados. A Microsoft recomenda usar o IoT Hub do Azure para conectar dispositivos IoT do Azure

O IoT Hub do Azure é o gateway de nuvem que conecta dispositivos de IoT para coletar dados e conduzir insights de negócios e a automação. Além disso, o Hub IoT inclui recursos que enriquecem a relação entre os dispositivos e seus sistemas de back-end. Recursos de comunicação bidirecional significam que embora você receba dados de dispositivos, você também pode enviar comandos e as políticas de volta aos dispositivos. Por exemplo, use mensagens de nuvem para dispositivo para atualizar as propriedades ou invocar ações de gerenciamento de dispositivo. Comunicação da nuvem para dispositivo também permite que você envie a inteligência da nuvem para seus dispositivos de borda com o Azure IoT Edge. A identidade exclusiva no nível de dispositivo fornecida pelo Hub IoT ajuda a proteger melhor a sua solução de IoT contra ataques potenciais. 

[Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) é o serviço de streaming de Big Data do Azure. Ele foi projetado para cenários de streaming de dados de alta taxa de transferência em que os clientes podem enviar bilhões de solicitações por dia. Os Hubs de Eventos usam um modelo de consumidor particionado para expandir sua transmissão e estão integrados aos serviços de big data e análise do Azure, incluindo Databricks, Stream Analytics, ADLS e HDInsight. Com recursos como a Captura e Aumento Automático de Hubs de Eventos, este serviço é criado para dar suporte a aplicativos e soluções de big data. Além disso, o IoT Hub usa os Hubs de eventos para seu caminho de fluxo de telemetria, portanto, sua solução de IoT também é beneficiado com a enorme potência dos Hubs de eventos.

Para resumir, ambas as soluções são projetadas para ingestão de dados em grande escala. Somente o IoT Hub oferece os recursos avançados que específicos de IoT que foram projetados para maximizar o valor comercial de conectar seus dispositivos de IoT na nuvem do Azure.  Se sua jornada IoT está apenas começando, começar com o Hub IoT para dar suporte a cenários de ingestão de dados garantirá que você tenha acesso instantâneo aos recursos de IoT completos quando suas necessidades comerciais e técnicas o exigir.

A tabela a seguir fornece detalhes sobre como as duas camadas do Hub IoT comparam-se aos Hubs de Eventos quando avaliá-las para os recursos de IoT. Para obter mais informações sobre as camadas Standard e Básica do Hub IoT, consulte [Como escolher a camada certa do Hub IoT](iot-hub-scaling.md).

| Funcionalidade do IoT | Camada Standard do Hub IoT | Camada Básica do Hub IoT | Hubs de Eventos |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a nuvem | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: HTTPS, AMQP, AMQP sobre WebSockets | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: MQTT, MQTT sobre WebSockets | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Identidade por dispositivo | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Upload de arquivo de dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Serviço de provisionamento de dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Mensagens da nuvem para o dispositivo | ![Verificação][checkmark] |  |  |
| Gerenciamento de dispositivo, dispositivo gêmeo | ![Verificação][checkmark] |  |  |
| Fluxos de dispositivo (versão prévia) | ![Verificação][checkmark] |  |  |
| IoT Edge | ![Verificação][checkmark] |  |  |

Mesmo se o único caso de uso for ingestão de dados de dispositivo para nuvem, é altamente recomendável usar o Hub IoT, pois ele fornece um serviço projetado para conectividade do dispositivo do IoT. 

### <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais as funcionalidade do Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
