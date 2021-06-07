---
title: Tópicos do sistema na grade de eventos do Azure
description: Descreve os tópicos do sistema na grade de eventos do Azure.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052958"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na grade de eventos do Azure
Um tópico do sistema na grade de eventos representa um ou mais eventos publicados pelos serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure. Por exemplo, um tópico do sistema pode representar **todos os eventos de blob** ou apenas **BLOBs criados** e eventos **excluídos de blob** publicados para uma **conta de armazenamento específica**. Neste exemplo, quando um blob é carregado na conta de armazenamento, o serviço de armazenamento do Azure publica um evento de **blob criado** no tópico sistema na grade de eventos, que, em seguida, encaminha o evento para os [assinantes](event-handlers.md) do tópico que recebem e processam o evento. 

> [!NOTE] 
> Somente os serviços do Azure podem publicar eventos em tópicos do sistema. Portanto, você não obtém um ponto de extremidade ou chaves de acesso que você pode usar para publicar eventos como você faz para tópicos ou domínios personalizados.

## <a name="azure-services-that-support-system-topics"></a>Serviços do Azure que dão suporte a tópicos do sistema
Aqui está a lista atual de serviços do Azure que dão suporte à criação de tópicos do sistema neles.

- [Configuração de Aplicativos do Azure](event-schema-app-configuration.md)
- [Serviço de Aplicativo do Azure](event-schema-app-service.md)
- [Armazenamento de Blobs do Azure](event-schema-blob-storage.md)
- [Serviços de Comunicação do Azure](event-schema-communication-services.md) 
- [Registro de Contêiner do Azure](event-schema-container-registry.md)
- [Hubs de eventos do Azure](event-schema-event-hubs.md)
- [Hub IoT do Azure](event-schema-iot-hub.md)
- [Cofre da Chave do Azure](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Mapas do Azure](event-schema-azure-maps.md)
- [Serviços de Mídia do Azure](event-schema-media-services.md)
- [Grupos de recursos do Azure](event-schema-resource-groups.md)
- [Barramento de Serviço do Azure](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Assinaturas do Azure](event-schema-subscriptions.md)
- [Cache Redis do Azure](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>Tópicos do sistema como recursos do Azure
No passado, um tópico do sistema era implícito e não estava exposto para simplificar. Os tópicos do sistema agora estão visíveis como recursos do Azure e fornecem os seguintes recursos:

- [Exibir tópicos do sistema no portal do Azure](create-view-manage-system-topics.md#view-all-system-topics)
- Exportar modelos do Resource Manager para tópicos do sistema e assinaturas de evento no portal do Azure
- [Configurar logs de diagnóstico para tópicos do sistema](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Configurar alertas em falhas de publicação e entrega 

## <a name="lifecycle-of-system-topics"></a>Ciclo de vida dos tópicos do sistema
Você pode criar um tópico do sistema de duas maneiras: 

- Crie uma [assinatura de evento em um recurso do Azure como um recurso de extensão](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), que cria automaticamente um tópico do sistema com o nome no formato: `<Azure resource name>-<GUID>` . O tópico do sistema criado dessa maneira é automaticamente excluído quando a última assinatura de evento do tópico é excluída. 
- Crie um tópico do sistema para um recurso do Azure e, em seguida, crie uma assinatura de evento para esse tópico do sistema. Ao usar esse método, você pode especificar um nome para o tópico do sistema. O tópico do sistema não é excluído automaticamente quando a última assinatura de evento é excluída. Você precisa excluí-lo manualmente. 

    Ao usar o portal do Azure, você sempre está usando esse método. Quando você cria uma assinatura de evento usando a [página **eventos** de um recurso do Azure](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage), o tópico do sistema é criado primeiro e, em seguida, a assinatura do tópico é criada. Você pode criar explicitamente um tópico do sistema primeiro usando a [página de **Tópicos do sistema de grade de eventos**](create-view-manage-system-topics.md#create-a-system-topic) e, em seguida, criar uma assinatura para esse tópico. 

Ao usar a [CLI](create-view-manage-system-topics-cli.md), [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)ou [Azure Resource Manager modelo](create-view-manage-system-topics-arm.md), você pode escolher um dos métodos acima. Recomendamos que você crie um tópico do sistema primeiro e, em seguida, crie uma assinatura no tópico, pois essa é a maneira mais recente de criar tópicos do sistema.

A criação do tópico do sistema falhará se você tiver configurado as políticas do Azure de forma que o serviço de grade de eventos não possa criá-la. Por exemplo, você pode ter uma política que permita a criação de apenas determinados tipos de recursos (por exemplo: armazenamento do Azure, hubs de eventos do Azure, etc.) na assinatura. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Local e grupo de recursos para um tópico do sistema
Para as fontes de eventos do Azure que estão em uma região/local específica, o tópico do sistema é criado no mesmo local que a origem do evento do Azure. Por exemplo, se você criar uma assinatura de evento para um armazenamento de BLOBs do Azure no leste dos EUA, o tópico do sistema será criado no leste dos EUA. Para fontes de eventos globais do Azure, como assinaturas do Azure, grupos de recursos ou mapas do Azure, a grade de eventos cria o tópico do sistema no local **global** . 

Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a origem do evento do Azure está. Para assinaturas de evento criadas no escopo de assinatura do Azure, o tópico do sistema é criado no grupo de recursos **padrão-EventGrid** na região **oeste dos EUA 2** . Se o grupo de recursos não existir, a grade de eventos do Azure o criará antes de criar o tópico do sistema. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Crie, exiba e gerencie tópicos do sistema usando portal do Azure](create-view-manage-system-topics.md).
- [Criar, exibir e gerenciar tópicos do sistema de grade de eventos usando CLI do Azure](create-view-manage-system-topics-cli.md)
- [Criar tópicos do sistema de grade de eventos usando modelos de Azure Resource Manager](create-view-manage-system-topics-arm.md)
