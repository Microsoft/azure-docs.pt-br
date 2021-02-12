---
title: Autenticar a entrega de eventos para manipuladores de eventos (grade de eventos do Azure)
description: Este artigo descreve diferentes maneiras de autenticar a entrega para manipuladores de eventos na grade de eventos do Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 98d7a4a0dee6c355ec340668bef7d8b306f97496
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633113"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autenticar a entrega de eventos para manipuladores de eventos (grade de eventos do Azure)
Este artigo fornece informações sobre como autenticar a entrega de eventos para manipuladores de eventos. Ele também mostra como proteger os pontos de extremidade do webhook que são usados para receber eventos da grade de eventos usando Azure Active Directory (Azure AD) ou um segredo compartilhado.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Usar identidades atribuídas pelo sistema para entrega de eventos
Você pode habilitar uma identidade gerenciada atribuída pelo sistema para um tópico ou domínio e usar a identidade para encaminhar eventos para destinos com suporte, como filas e tópicos do barramento de serviço, hubs de eventos e contas de armazenamento.

Siga estas etapas: 

1. Crie um tópico ou domínio com uma identidade atribuída pelo sistema ou atualize um tópico ou domínio existente para habilitar a identidade. 
1. Adicione a identidade a uma função apropriada (por exemplo, remetente de dados do barramento de serviço) no destino (por exemplo, uma fila do barramento de serviço).
1. Ao criar assinaturas de evento, habilite o uso da identidade para entregar eventos ao destino. 

Para obter instruções passo a passo detalhadas, consulte [entrega de eventos com uma identidade gerenciada](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar a entrega de eventos em pontos de extremidade de webhook
As seções a seguir descrevem como autenticar a entrega de eventos em pontos de extremidade do webhook. Você precisa usar um mecanismo de handshake de validação, independentemente do método usado. Confira [Entrega de eventos do webhook](webhook-event-delivery.md) para obter detalhes. 


### <a name="using-azure-active-directory-azure-ad"></a>Usando o Azure Active Directory (Azure AD)
Você pode proteger o ponto de extremidade do webhook que é usado para receber eventos da Grade de Eventos usando o Azure AD. Será necessário criar um aplicativo do Azure AD, uma função e uma entidade de serviço em seu aplicativo autorizando a Grade de Eventos e configurar a assinatura do evento para usar o aplicativo do Azure AD. Saiba como [Configurar o Azure Active Directory com a Grade de Eventos](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como um parâmetro de consulta
Também é possível proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL de destino do webhook como parte da criação de uma Assinatura de Evento. Defina um dos parâmetros de consulta como o segredo do cliente; por exemplo, um [token de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo compartilhado. O serviço da Grade de Eventos inclui todos esses parâmetros de consulta em cada solicitação de entrega de evento para o webhook. O serviço de webhook pode recuperar e validar o segredo. Se o segredo do cliente for atualizado, a assinatura do evento também precisará de atualização. Para evitar falhas na entrega durante essa rotação de segredo, faça com que o webhook aceite segredos novos e antigos por um período limitado antes de atualizar a assinatura do evento com o novo segredo. 

Como os parâmetros de consulta podem conter segredos do cliente, eles são tratados com cuidado extra. Eles são armazenados criptografados e não ficam acessíveis a operadores de serviço. Eles não são registrados como parte dos logs/rastreamentos do serviço. Ao recuperar as propriedades da Assinatura do Evento, os parâmetros de consulta de destino não são retornados por padrão. Por exemplo: o parâmetro [--include-Full-Endpoint-URL](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-show) deve ser usado na [CLI](/cli/azure) do Azure.

Para obter mais informações sobre como entregar eventos a webhooks, confira [Entrega de eventos do webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A Grade de Eventos do Azure é compatível somente com pontos de extremidade **HTTPS** do webhook. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto de extremidade com CloudEvents v 1.0
Se você já estiver familiarizado com a grade de eventos, talvez esteja ciente do handshake de validação do ponto de extremidade para evitar abusos. O CloudEvents v 1.0 implementa sua própria [semântica de proteção de abuso](webhook-event-delivery.md) usando o método de **Opções http** . Para ler mais sobre isso, confira [ganchos da Web HTTP 1,1 para entrega de eventos-versão 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando você usa o esquema CloudEvents para saída, a grade de eventos usa a proteção de abuso do CloudEvents v 1.0 em vez do mecanismo de evento de validação da grade de eventos. Para obter mais informações, consulte [usar o esquema CloudEvents v 1.0 com a grade de eventos](cloudevents-schema.md). 


## <a name="next-steps"></a>Próximas etapas
Consulte [autenticar clientes de publicação](security-authenticate-publishing-clients.md) para saber mais sobre como autenticar eventos de publicação de clientes em tópicos ou domínios. 
