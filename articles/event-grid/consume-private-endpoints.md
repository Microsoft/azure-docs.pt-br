---
title: Entregar eventos usando o serviço de vínculo privado
description: Este artigo descreve como solucionar a limitação de não poder entregar eventos usando o serviço de vínculo privado.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 9df78e1cc7734ba9e455ed686286658006f9445e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629284"
---
# <a name="deliver-events-using-private-link-service"></a>Entregar eventos usando o serviço de vínculo privado
Atualmente, não é possível entregar eventos usando pontos de [extremidade privados](../private-link/private-endpoint-overview.md). Ou seja, não haverá suporte se você tiver requisitos estritos de isolamento de rede em que o tráfego de eventos entregues não deve sair do espaço IP privado. 

## <a name="use-managed-identity"></a>Usar identidade gerenciada
No entanto, se seus requisitos chamarem uma maneira segura de enviar eventos usando um canal criptografado e uma identidade conhecida do remetente (nesse caso, a grade de eventos) usando o espaço IP público, você poderá entregar eventos para hubs de eventos, barramento de serviço ou serviço de armazenamento do Azure usando um tópico personalizado da grade de eventos do Azure ou um domínio com identidade gerenciada pelo sistema. Para obter detalhes sobre como fornecer eventos usando identidade gerenciada, consulte [entrega de eventos usando uma identidade gerenciada](managed-service-identity.md). 

Em seguida, você pode usar um link privado configurado em Azure Functions ou seu webhook implantado em sua rede virtual para efetuar pull de eventos. Consulte o exemplo: [conectar-se a pontos de extremidade privados com Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Entregar por meio do serviço de link privado":::


Nessa configuração, o tráfego passa pelo IP público/Internet da grade de eventos para os hubs de eventos, barramento de serviço ou armazenamento do Azure, mas o canal pode ser criptografado e uma identidade gerenciada da grade de eventos é usada. Se você configurar seu Azure Functions ou webhook implantado em sua rede virtual para usar os hubs de eventos, o barramento de serviço ou o armazenamento do Azure por meio do link privado, essa seção do tráfego permanecerá, de forma evidente, no Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Entregar eventos aos hubs de eventos usando identidade gerenciada
Para entregar eventos aos hubs de eventos em seu namespace de hubs de eventos usando a identidade gerenciada, siga estas etapas:

1. Habilitar identidade atribuída pelo sistema: [Tópicos do sistema](enable-identity-system-topics.md), [Tópicos personalizados e domínios](enable-identity-custom-topics-domains.md).  
1. [Adicione a identidade à função de **remetente de dados dos hubs de eventos do Azure** no namespace de hubs de eventos](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Habilite **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall em seu namespace de hubs de eventos](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Configure a assinatura de evento](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) que usa um hub de eventos como um ponto de extremidade para usar a identidade atribuída pelo sistema.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Entregar eventos ao barramento de serviço usando a identidade gerenciada
Para entregar eventos a filas ou tópicos do barramento de serviço em seu namespace do barramento de serviço usando a identidade gerenciada, siga estas etapas:

1. Habilitar identidade atribuída pelo sistema: [Tópicos do sistema](enable-identity-system-topics.md), [Tópicos personalizados e domínios](enable-identity-custom-topics-domains.md). 
1. [Adicionar a identidade à função de **remetente de dados do barramento de serviço do Azure**](/service-bus-messaging/service-bus-managed-service-identity#azure-built-in-roles-for-azure-service-bus) no namespace do barramento de serviço
1. [Habilite **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall no namespace do barramento de serviço](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Configure a assinatura de evento](managed-service-identity.md) que usa uma fila ou um tópico do barramento de serviço como um ponto de extremidade para usar a identidade atribuída pelo sistema.

## <a name="deliver-events-to-storage"></a>Entregar eventos ao armazenamento 
Para entregar eventos às filas de armazenamento usando a identidade gerenciada, siga estas etapas:

1. Habilitar identidade atribuída pelo sistema: [Tópicos do sistema](enable-identity-system-topics.md), [Tópicos personalizados e domínios](enable-identity-custom-topics-domains.md). 
1. [Adicione a identidade à função de **remetente da mensagem de dados da fila de armazenamento**](../storage/common/storage-auth-aad-rbac-portal.md) na fila de armazenamento do Azure.
1. [Configure a assinatura de evento](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) que usa uma fila ou um tópico do barramento de serviço como um ponto de extremidade para usar a identidade atribuída pelo sistema.


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como fornecer eventos usando uma identidade gerenciada, consulte [entrega de eventos usando uma identidade gerenciada](managed-service-identity.md). 
