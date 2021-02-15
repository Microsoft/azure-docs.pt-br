---
title: Verificar o serviço do Hub IoT do Azure e o Resource Health | Microsoft Docs
description: Usar o Azure Resource Health e a integridade do serviço do Azure para monitorar o Hub IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548455"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Verificar o serviço do Hub IoT e o Resource Health

O Hub IoT do Azure integra-se ao [serviço de integridade do serviço do Azure](../service-health/overview.md) para fornecer a capacidade de monitorar a integridade de nível de serviço do serviço do Hub IOT e dos hubs IOT individuais. Você também pode configurar alertas para serem notificados quando o status do serviço Hub IoT ou um hub IoT for alterado. O serviço de integridade do serviço do Azure é uma combinação de três serviços menores: Azure Resource Health, integridade do serviço do Azure e a página de status do Azure. As seções neste artigo descrevem cada serviço e sua relação com o Hub IoT em mais detalhes.

O serviço de integridade do serviço do Azure ajuda a monitorar eventos de nível de serviço, como interrupções e atualizações que podem afetar a disponibilidade do serviço Hub IoT e seus hubs IoT individuais. O Hub IoT também se integra com Azure Monitor para fornecer métricas de plataforma do Hub IoT e logs de recursos do Hub IoT que você pode usar para monitorar erros operacionais e condições que ocorrem em um hub IoT específico. Para saber mais, consulte [monitorar o Hub IOT](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Verificar a integridade de um hub IoT com Azure Resource Health

Azure Resource Health é uma parte do serviço de integridade do serviço do Azure que controla a integridade de recursos individuais. Você pode verificar o status de integridade do Hub IoT diretamente do Portal.

Para ver o status e o histórico de status do seu hub IoT usando o portal, siga estas etapas:

1. Em [portal do Azure](https://portal.azure.com), vá para o Hub IoT em portal do Azure.

1. No painel esquerdo, em **suporte + solução de problemas** , selecione **Resource Health** .

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Página de integridade de recursos para um hub IoT":::

Para saber mais sobre Azure Resource Health e como interpretar os dados de integridade, confira [Resource Health visão geral](../service-health/resource-health-overview.md) na documentação de integridade do serviço do Azure.

Você também pode selecionar **adicionar alerta de integridade de recurso** para configurar alertas para disparar quando o status de integridade do Hub IOT for alterado. Para saber mais, confira [configurar alertas para eventos de integridade do serviço](../service-health/alerts-activity-log-service-notifications-portal.md) e tópicos relacionados na documentação de integridade do serviço do Azure.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Verificar a integridade dos hubs IoT em sua assinatura com a integridade do serviço do Azure

Com a integridade do serviço do Azure, você pode verificar o status de integridade de todos os hubs IoT em sua assinatura.

Para verificar a integridade de seus Hubs IoT, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até **serviço** integridade de  >  **recursos** integridade.

3. Nas caixas suspensas, selecione sua assinatura e selecione **Hub IOT** como o tipo de recurso.

Para saber mais sobre a integridade do serviço do Azure e como interpretar os dados de integridade, consulte [visão geral da integridade](../service-health/service-health-overview.md) do serviço na documentação de integridade do serviço do Azure.

Para saber como configurar alertas com a integridade do serviço do Azure, consulte [configurar alertas para eventos de integridade do serviço](../service-health/alerts-activity-log-service-notifications-portal.md) e tópicos relacionados na documentação de integridade do serviço do Azure.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Verificar a integridade do serviço do Hub IoT por região na página de status do Azure

Para ver o status do Hub IoT e outros serviços por região em todo o mundo, você pode usar a [página de status do Azure](https://status.azure.com/status). Para obter mais informações sobre a página de status do Azure, consulte [visão geral de status do Azure](../service-health/azure-status-overview.md) na documentação de integridade do serviço do Azure.

## <a name="next-steps"></a>Próximas etapas

* Consulte [serviço de integridade do serviço do Azure](../service-health/overview.md) para obter detalhes sobre a integridade do serviço do azure, Azure Resource Health e a página de status do Azure.
* Consulte [monitorar o Hub IOT do Azure](monitor-iot-hub.md) para obter uma descrição do monitoramento do Hub IOT do Azure.
