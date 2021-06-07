---
title: Autorizar o acesso aos Hubs de Eventos do Azure
description: Este artigo fornece informações sobre diferentes opções para autorizar o acesso aos recursos dos hubs de eventos do Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92332375"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizar o acesso aos Hubs de Eventos do Azure
Sempre que você publica ou consome eventos/dados de um hub de eventos, seu cliente está tentando acessar os recursos dos hubs de eventos. Cada solicitação para um recurso seguro deve ser autorizada para que o serviço possa garantir que o cliente tenha as permissões necessárias para publicar/consumir os dados. 

Os hubs de eventos do Azure oferecem as seguintes opções para autorizar o acesso a recursos seguros:

- Azure Active Directory
- Assinatura de acesso compartilhado

> [!NOTE]
> Este artigo se aplica a cenários de hubs de eventos e [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) . 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Active Directory (Azure AD) para os recursos dos hubs de eventos fornece controle de acesso baseado em função do Azure (RBAC do Azure) para um controle refinado sobre o acesso de um cliente aos recursos. Você pode usar o RBAC do Azure para conceder permissões à entidade de segurança, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação para acessar um recurso de hubs de eventos.

Para obter mais informações sobre como autenticar com o Azure AD, consulte os seguintes artigos:

- [Autenticar solicitações para hubs de eventos do Azure usando Azure Active Directory](authenticate-application.md)
- [Autorize o acesso aos recursos dos hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado 
As SAS (assinaturas de acesso compartilhado) para os recursos dos hubs de eventos fornecem acesso delegado limitado aos recursos dos hubs de eventos. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões que ela concede fornece flexibilidade no gerenciamento de recursos. Para obter mais informações, consulte [autenticar usando SAS (assinaturas de acesso compartilhado)](authenticate-shared-access-signature.md). 

A autorização de usuários ou aplicativos que usam um token OAuth 2,0 retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não há necessidade de armazenar os tokens de acesso com seu código e arriscar as vulnerabilidades de segurança potenciais. Embora você possa continuar a usar as SAS (assinaturas de acesso compartilhado) para conceder acesso refinado aos recursos dos hubs de eventos, o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar com a revogação de uma SAS comprometida. 

Por padrão, todos os recursos de hubs de eventos são protegidos e estão disponíveis somente para o proprietário da conta. Embora você possa usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes acesso aos recursos do hub de eventos. A Microsoft recomenda usar o Azure AD quando possível para segurança máxima e facilidade de uso.

Para obter mais informações sobre a autorização usando SAS, consulte [autorizando o acesso aos recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Próximas etapas
- Examine os [exemplos do RBAC do Azure](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publicados em nosso repositório github. 
- Veja os artigos a seguir:
    - [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
    - [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
    - [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

