---
title: Referência de API de Portal do Cloud Partner-Microsoft Commercial Marketplace
description: Descrição dos pré-requisitos a serem usados e lista de operações de API do Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425775"
---
# <a name="cloud-partner-portal-api-reference"></a>Referência de API do Portal do Cloud Partner

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as [alterações nas APIs de CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) listadas neste documento para garantir que seu código continue a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

As APIs REST do Portal do Cloud Partner permitem a recuperação programática e a manipulação de cargas de trabalho, ofertas e perfis de publicação. As APIs usam o Azure RBAC (controle de acesso baseado em função) para impor as permissões corretas no momento do processamento.

Essa referência fornece os detalhes técnicos para as APIs REST do Portal do Cloud Partner. Os exemplos de conteúdo deste documento são apenas para referência e estão sujeitos a alterações à medida que novas funcionalidades são adicionadas.

## <a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações

Antes de usar as APIs, você deverá revisar:

- O artigo de [Pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para saber como adicionar uma entidade de serviço à sua conta e obter um token de acesso do Azure AD (Azure Active Directory) para autenticação.
- As duas estratégias de [controle de simultaneidade](./cloud-partner-portal-api-concurrency-control.md) disponíveis para chamar essas APIs.
- As [considerações](./cloud-partner-portal-api-considerations.md) adicionais da API como controle de versão e tratamento de erros.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Alterações nas APIs de CPP após a migração para o Partner Center

| **API** | **Descrição da alteração** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POSTAR publicação, GoLive, cancelar | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o status da operação. | Ao enviar qualquer uma das solicitações POST correspondentes para uma oferta, o cabeçalho Location terá um dos dois formatos, dependendo do status de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operação GET | Para tipos de oferta que anteriormente eram compatíveis com o campo ' Notification-email ' na resposta, esse campo será preterido e não retornará mais para ofertas migradas. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nas solicitações. Em vez disso, o serviço de API será alinhado com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na seção informações de contato do vendedor de suas configurações de conta no Partner Center, para notificá-lo sobre o andamento da operação.<br><br>Examine o endereço de email definido na seção informações de contato do vendedor de suas [configurações de conta](https://partner.microsoft.com/dashboard/account/management) no Partner Center para garantir que o email correto seja fornecido para notificações.  |

## <a name="common-tasks"></a>Tarefas comuns

Essa referência detalha as APIs para executar as seguintes tarefas comuns.

### <a name="offers"></a>Ofertas

- [Recuperar todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
- [Recuperar uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Recuperar status da oferta](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Criar uma oferta](./cloud-partner-portal-api-creating-offer.md)
- [Publicar uma oferta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operações

- [Recuperar operações](./cloud-partner-portal-api-retrieve-operations.md)
- [Cancelar operações](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicar um aplicativo

- [Entrar no ar](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

- [Definir preços para ofertas de máquinas virtuais](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Solução de problemas

- [Solucionando problemas de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
