---
title: Fontes de eventos da Grade de Eventos do Azure
description: Descreve as fontes de eventos com suporte para a Grade de Eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: ad9bb5c135684c4573195298fb8e55a08208f6b6
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785815"
---
# <a name="event-sources-in-azure-event-grid"></a>Fontes de eventos na Grade de Eventos do Azure

A origem de um evento é onde o evento acontece. Vários serviços do Azure são automaticamente configurados para enviar eventos. Você também pode criar aplicativos personalizados que enviam eventos. Os aplicativos personalizados não precisam ser hospedados no Azure para usar a Grade de Eventos para a distribuição de eventos.

Este artigo fornece links para conteúdo de cada fonte de eventos.

## <a name="azure-subscriptions"></a>Assinaturas do Azure

Assine eventos de Assinaturas do Azure para responder a alterações em recursos em uma assinatura do Azure.

|Título |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: Automação do Azure com Grade de Eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |
| [Como: assinar eventos por meio do portal](subscribe-through-portal.md) | Use o portal para assinar eventos de uma assinatura do Azure. |
| [CLI do Azure: assinar eventos de uma assinatura do Azure](./scripts/event-grid-cli-azure-subscription.md) |Script de exemplo que cria uma assinatura de Grade de Eventos para uma assinatura do Azure e envia eventos para um WebHook. |
| [PowerShell: assinar eventos de uma assinatura do Azure](./scripts/event-grid-powershell-azure-subscription.md)| Script de exemplo que cria uma assinatura de Grade de Eventos para uma assinatura do Azure e envia eventos para um WebHook. |
| [Esquema do evento](event-schema-subscriptions.md) | Mostra campos nos eventos de assinatura do Azure. |

## <a name="container-registry"></a>Registro de Contêiner

Assine eventos de Registro de Contêiner para responder a alterações nas imagens.

|Título |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de Registro de Contêiner. |
| [Esquema do evento](event-schema-container-registry.md) | Mostra campos nos eventos do Registro de Contêiner. |

## <a name="custom-topics"></a>Tópicos personalizados

Assine os tópicos personalizados para responder a eventos do aplicativo.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: como criar e encaminhar eventos personalizados com a CLI do Azure](custom-event-quickstart.md) | Mostra como usar a CLI do Azure para enviar eventos personalizados. |
| [Início Rápido: como criar e encaminhar eventos personalizados com o Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [Início Rápido: como criar e encaminhar eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Início Rápido: como encaminhar eventos personalizados para o Armazenamento de Filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |
| [Como publicar para tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |
| [CLI do Azure: criar um tópico personalizado da Grade de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [CLI do Azure: assinar eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|
| [PowerShell: criar um tópico de Grade de Eventos personalizado](./scripts/event-grid-powershell-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [PowerShell: assinar eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo do Gerenciador de Recursos que cria um tópico personalizado e uma assinatura para esse tópico personalizado. Envia eventos para um WebHook. |
|
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de Hubs de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |
| [Esquema do evento](event-schema.md) | Mostra campos em eventos personalizados. |

## <a name="event-hubs"></a>Hubs de Eventos

Assine eventos de Hubs de Eventos para responder a eventos de arquivo e Captura. Os Hubs de Eventos podem agir como uma fonte de evento ou um manipulador de eventos. Os artigos a seguir mostram como usar os Hubs de Eventos como uma fonte.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Esquema do evento](event-schema-event-hubs.md) | Mostra campos nos eventos de Hubs de Eventos. |

Para obter exemplos de Hubs de Eventos como um manipulador, consulte [manipulador de Hubs de Eventos](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Hub IoT

Assine eventos de telemetria e eventos do IoT Hub para responder a dispositivo criado, excluído, conectados e desconectados.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu Hub IoT. |
| [Reagir aos eventos do Hub IoT usando a Grade de Eventos para acionar ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do Hub IoT com a Grade de Eventos. |
| [Esquema do evento](event-schema-iot-hub.md) | Mostra campos nos eventos de Hub IoT. |
| [Ordenar eventos de conexão e desconexão de dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como ordenar os eventos de estado de conexão do dispositivo. |

## <a name="media-services"></a>Serviços de mídia

Assine eventos de Serviços de Mídia para responder a eventos de estado de trabalho.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Visão geral: reagir aos eventos dos Serviços de Mídia](../media-services/latest/reacting-to-media-services-events.md) | Visão geral da integração de Serviços de Mídia com Grade de Eventos. |
| [Tutorial: rotear eventos de Serviços de Mídia do Azure para um ponto de extremidade da Web personalizado usando a CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos de Serviços de Mídia. |
| [Esquema do evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra campos nos eventos de Serviços de Mídia. |

## <a name="resource-groups"></a>Grupos de recursos

Assine eventos de grupo de recursos para responder a alterações em recursos em um grupo de recursos.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: como monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os aplicativos lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [CLI do Azure: assinar eventos para um grupo de recursos com a CLI do Azure](./scripts/event-grid-cli-resource-group.md)| Script de exemplo que assina eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [CLI do Azure: assinar eventos de um grupo de recursos e filtrar por um recurso](./scripts/event-grid-cli-resource-group-filter.md) | Script de exemplo que assina eventos de um grupo de recursos e filtra eventos por um recurso. |
| [PowerShell: assinar eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Script de exemplo que assina eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [PowerShell: assinar eventos de um grupo de recursos e filtrar por um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | Script de exemplo que assina eventos de um grupo de recursos e filtra eventos por um recurso. |
| [Modelo do Resource Manager: assinatura do recurso](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Assina eventos para uma assinatura do Azure ou grupo de recursos. Envia eventos para um WebHook. |
| [Esquema do evento](event-schema-resource-groups.md) | Mostra campos em eventos de grupo de recursos. |

## <a name="service-bus"></a>Barramento de Serviço

Assine eventos de Barramento de Serviço para responder às mensagens sem um ouvinte de ativo.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: Exemplos de integração do Barramento de Serviço do Azure para a Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |
| [Visão geral: Integração do Barramento de Serviço do Azure com a Grade de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do Barramento de Serviço com a Grade de Eventos. |
| [Esquema do evento](event-schema-service-bus.md) | Mostra os campos nos eventos de Barramento de Serviço. |

## <a name="storage"></a>Armazenamento

Assine os eventos de Armazenamento de Blobs para responder aos eventos criados e excluídos de blob.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: encaminhar eventos de armazenamento de Blob para um ponto de extremidade da Web personalizado com a CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de armazenamento de blob para um WebHook. |
| [Início Rápido: encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blobs para um WebHook. |
| [Início Rápido: criar e rotear eventos personalizados de armazenamento de Blobs com o portal do Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento de blob para um WebHook. |
| [CLI do Azure: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-cli-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [PowerShell: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-powershell-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [Modelo do Resource Manager: Criar armazenamento de blobs e assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir aos eventos de armazenamento de Blobs](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do Armazenamento de Blobs com a Grade de Eventos. |
| [Esquema do evento](event-schema-blob-storage.md) | Mostra campos nos eventos de Armazenamento de Blobs. |

## <a name="maps"></a>Mapas
Assine eventos do Azure Mapas para responder a eventos de cerca geográfica. Por exemplo, um aplicativo poderia enviar uma notificação por email sempre que um dispositivo entra ou sai de uma cerca geográfica.

|Título  |DESCRIÇÃO  |
|---------|---------|
| [Reagir a eventos do Azure Mapas usando a Grade de Eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração do Azure Mapas com a Grade de Eventos. |
| [Tutorial: Configurar uma cerca geográfica](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial orienta você pelas etapas básicas para configurar uma cerca geográfica usando o Azure Mapas. Você usa a Grade de Eventos do Azure para transmitir os resultados da cerca geográfica e configurar uma notificação de acordo com os resultados desse limite. |
| [Esquema do evento](event-schema-azure-maps.md) | Mostra campos nos eventos do Azure Mapas. |

## <a name="app-configuration"></a>Configuração de aplicativos
Assine eventos de configuração de aplicativo do Azure para responder a eventos de modificação de chave-valor.

|Título | DESCRIÇÃO |
|---------|---------|
| [Reagir a eventos de configuração de aplicativo do Azure usando a grade de eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração de configuração de aplicativo do Azure com a grade de eventos. |
| [Guia de início rápido: encaminhar eventos de configuração de aplicativo do Azure para um ponto de extremidade da web personalizado com CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de configuração de aplicativo do Azure para um WebHook. |
| [Esquema do evento](event-schema-app-configuration.md) | Mostra campos nos eventos de configuração de aplicativo do Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Assine eventos de serviço do Azure SignalR para responder a eventos de conexão do cliente.

|Título | DESCRIÇÃO |
|---------|---------|
| [Reagir a eventos de serviço do Azure SignalR usando a grade de eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral de integração do serviço do Azure SignalR com a grade de eventos. |
| [Como enviar eventos de serviço do Azure SignalR à grade de eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos de serviço do Azure SignalR para um aplicativo por meio da grade de eventos. |
| [Esquema do evento](event-schema-azure-signalr.md) | Mostra campos nos eventos de serviço do Azure SignalR. |



## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
