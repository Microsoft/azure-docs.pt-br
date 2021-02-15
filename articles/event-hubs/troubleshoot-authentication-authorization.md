---
title: Solucionar problemas de autenticação e autorização-hubs de eventos do Azure
description: Este artigo fornece informações sobre como solucionar problemas de autenticação e autorização com os hubs de eventos do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329603"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Solucionar problemas de autenticação e autorização-hubs de eventos do Azure
O artigo [solucionar problemas de conectividade](troubleshooting-guide.md) fornece dicas para solucionar problemas de conectividade com os hubs de eventos do Azure. Este artigo fornece dicas e recomendações para solucionar problemas de autenticação e autorização com os hubs de eventos do Azure. 

## <a name="if-you-are-using-azure-active-directory"></a>Se você estiver usando Azure Active Directory
Se você estiver usando Azure Active Directory (Azure AD) para autenticar e autorizar com os hubs de eventos do Azure, confirme se a identidade que acessa o Hub de eventos é membro da função certa do **Azure** no **escopo de recursos** correto (grupo de consumidores, Hub de eventos, namespace, grupo de recursos ou assinatura).

### <a name="azure-roles"></a>Funções do Azure
- O [proprietário dos dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) para acesso completo aos recursos dos hubs de eventos.
- [Remetente de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) para o acesso de envio.
- [Receptor de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) para o acesso de recebimento.

Para funções internas do registro de esquema, consulte [funções de registro de esquema](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Escopos de recursos
- **Grupo de consumidores**: nesse escopo, a atribuição de função se aplica somente a essa entidade. Atualmente, o portal do Azure não dá suporte à atribuição de uma função do Azure a uma entidade de segurança nesse nível. 
- **Hub de eventos**: a atribuição de função se aplica à entidade do hub de eventos e ao grupo de consumidores sob ela.
- **Namespace**: a atribuição de função abrange toda a topologia de hubs de eventos no namespace e no grupo de consumidores associado a ela.
- **Grupo de recursos**: a atribuição de função se aplica a todos os recursos de hubs de eventos no grupo de recursos.
- **Assinatura**: a atribuição de função se aplica a todos os recursos de hubs de eventos em todos os grupos de recursos na assinatura.

Para obter mais informações, consulte os seguintes artigos:

- [Autenticar um aplicativo com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-application.md)
- [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Se você estiver usando SAS (assinaturas de acesso compartilhado)
Se você estiver usando a [SAS](authenticate-shared-access-signature.md), siga estas etapas: 

- Verifique se a chave SAS que você está usando está correta. Caso contrário, use a chave SAS correta.
- Verifique se a chave tem as permissões corretas (enviar, receber ou gerenciar). Caso contrário, use uma chave que tenha a permissão necessária. 
- Verifique se a chave expirou. Recomendamos que você Renove a SAS bem antes da expiração. Se houver uma distorção de relógio entre o cliente e os nós de serviço dos hubs de eventos, o token de autenticação poderá expirar antes que o cliente o perceba. As contas de implementação atuais distorcem até 5 minutos, ou seja, o cliente renova o token 5 minutos antes de expirar. Portanto, se a distorção do relógio for maior que 5 minutos, o cliente poderá observar falhas intermitentes de autenticação.
- Se a **hora de início da SAS** for definida como **agora**, você poderá ver falhas intermitentes nos primeiros minutos devido à distorção do relógio (diferenças no tempo atual em máquinas diferentes). Defina a hora de início como no mínimo 15 minutos no passado ou não a defina. O mesmo geralmente se aplica ao tempo de expiração também. 

Para obter mais informações, consulte os seguintes artigos: 

- [Autenticar usando SAS (assinaturas de acesso compartilhado)](authenticate-shared-access-signature.md). 
- [Autorizando o acesso aos recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

* [Solucionar problemas de conectividade](troubleshooting-guide.md)
