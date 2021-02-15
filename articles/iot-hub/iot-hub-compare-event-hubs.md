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
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142771"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Conectar Dispositivos IoT ao Azure: Hub IoT e Hubs de Eventos

O Azure fornece serviços desenvolvidos especificamente para diferentes tipos de conectividade e comunicação para ajudá-lo a conectar seus dados ao poder da nuvem. Tanto o Hub IoT como os Hubs de Eventos do Azure são serviços de nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para informações de negócios. Os dois serviços são semelhantes, pois ambos dão suporte à ingestão de dados com baixa latência e alta confiabilidade, mas eles são projetados para finalidades diferentes. O Hub IoT foi desenvolvido para atender aos requisitos exclusivos de conexão de dispositivos IoT à nuvem do Azure, enquanto os hubs de eventos foram projetados para Big Data streaming. A Microsoft recomenda usar o Hub IoT do Azure para conectar dispositivos IoT ao Azure

O Hub IoT do Azure é o gateway de nuvem que conecta dispositivos IoT para coletar dados e conduzir informações de negócios e automação. Além disso, o Hub IoT inclui recursos que enriquecem a relação entre os dispositivos e seus sistemas de back-end. Os recursos de comunicação bidirecional significam que, enquanto você recebe dados de dispositivos, você também pode enviar comandos e políticas de volta para dispositivos. Por exemplo, use mensagens da nuvem para o dispositivo para atualizar propriedades ou invocar ações de gerenciamento de dispositivo. A comunicação da nuvem para o dispositivo também permite que você envie inteligência de nuvem para seus dispositivos de borda com Azure IoT Edge. A identidade exclusiva no nível de dispositivo fornecida pelo Hub IoT ajuda a proteger melhor a sua solução de IoT contra ataques potenciais. 

[Hubs de Eventos do Azure](../event-hubs/event-hubs-about.md) é o serviço de streaming de Big Data do Azure. Ele foi projetado para cenários de streaming de dados de alta taxa de transferência em que os clientes podem enviar bilhões de solicitações por dia. Os Hubs de Eventos usam um modelo de consumidor particionado para expandir sua transmissão e estão integrados aos serviços de big data e análise do Azure, incluindo Databricks, Stream Analytics, ADLS e HDInsight. Com recursos como a Captura e Aumento Automático de Hubs de Eventos, este serviço é criado para dar suporte a aplicativos e soluções de big data. Além disso, o Hub IoT usa hubs de eventos para seu caminho de fluxo de telemetria, de modo que sua solução de IoT também se beneficia da enorme potência dos hubs de eventos.

Para resumir, ambas as soluções foram projetadas para ingestão de dados em grande escala. Somente o Hub IoT fornece os recursos avançados de IoT específicos que são projetados para você maximizar o valor comercial de conectar seus dispositivos IoT à nuvem do Azure.  Se sua jornada IoT está apenas começando, começar com o Hub IoT para dar suporte a cenários de ingestão de dados garantirá que você tenha acesso instantâneo aos recursos de IoT completos quando suas necessidades comerciais e técnicas o exigir.

A tabela a seguir fornece detalhes sobre como as duas camadas do Hub IoT comparam-se aos Hubs de Eventos quando avaliá-las para os recursos de IoT. Para obter mais informações sobre as camadas Standard e Básica do Hub IoT, consulte [Como escolher a camada certa do Hub IoT](iot-hub-scaling.md).

| Funcionalidade do IoT | Camada Standard do Hub IoT | Camada Básica do Hub IoT | Hubs de Eventos |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a nuvem | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: HTTPS, AMQP, AMQP sobre webSockets | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: MQTT, MQTT sobre webSockets | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Identidade por dispositivo | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Upload de arquivo de dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Serviço de Provisionamento de Dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Mensagens da nuvem para o dispositivo | ![Verificação][checkmark] |  |  |
| Gerenciamento de dispositivo, dispositivo gêmeo | ![Verificação][checkmark] |  |  |
| Fluxos de dispositivo (versão prévia) | ![Verificação][checkmark] |  |  |
| IoT Edge | ![Verificação][checkmark] |  |  |

Mesmo se o único caso de uso for ingestão de dados de dispositivo para nuvem, é altamente recomendável usar o Hub IoT, pois ele fornece um serviço projetado para conectividade do dispositivo do IoT. 

### <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais as funcionalidade do Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png