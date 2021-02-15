---
title: Autenticação e Autorização do Barramento de Serviço do Azure | Microsoft Docs
description: Autentique aplicativos no Barramento de Serviço com a autenticação SAS (Assinatura de Acesso Compartilhado).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7b287b209fbcd5bc2782505095aeae4390107803
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060207"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Barramento de Serviço
Há duas maneiras de autenticar e autorizar o acesso aos recursos do barramento de serviço do Azure: diretório de atividades do Azure (Azure AD) e SAS (assinaturas de acesso compartilhado). Este artigo fornece detalhes sobre como usar esses dois tipos de mecanismos de segurança. 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure AD para recursos do barramento de serviço fornece controle de acesso baseado em função do Azure (RBAC do Azure) para um controle refinado sobre o acesso de um cliente aos recursos. Você pode usar o Azure RBAC para conceder permissões a uma entidade de segurança, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação para acessar um recurso do barramento de serviço (fila, tópico, etc.).

Para obter mais informações sobre como autenticar com o Azure AD, consulte os seguintes artigos:

- [Autenticar com identidades gerenciadas](service-bus-managed-service-identity.md)
- [Autenticar a partir de um aplicativo](authenticate-application.md)

> [!NOTE]
> A [API REST do barramento de serviço](/rest/api/servicebus/) dá suporte à autenticação OAuth com o Azure AD.

> [!IMPORTANT]
> A autorização de usuários ou aplicativos usando o token 2,0 do OAuth retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar os tokens no código e arriscar as vulnerabilidades de segurança potenciais. É recomendável que você use o Azure AD com seus aplicativos do barramento de serviço do Azure quando possível. 

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado
A [autenticação SAS](service-bus-sas.md) permite que você conceda a um usuário o acesso aos recursos do Barramento de Serviço, com direitos específicos. A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace do Barramento de Serviço. A chave se aplica a todas as entidades de mensagens nesse namespace. Também é possível configurar chaves em tópicos e filas do Barramento de Serviço. Também há suporte para SAS na [Retransmissão do Azure](../azure-relay/relay-authentication-and-authorization.md).

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace, uma fila ou um tópico. Essa regra consiste nos seguintes elementos:

* *KeyName*: identifica a regra.
* *PrimaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *Rights*: representa a coleção de direitos de **Escuta**, **Envio** ou **Gerenciamento** concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a tecla correspondente. Configure até 12 regras de autorização como essas em um namespace, uma fila ou um tópico do Barramento de Serviço. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

> [!IMPORTANT]
> Se você estiver usando o controle de acesso Azure Active Directory (também conhecido como serviço de controle de acesso ou ACS) com o barramento de serviço, observe que o suporte para esse método agora é limitado e você deve [migrar seu aplicativo para usar SAS](service-bus-migrate-acs-sas.md) ou usar a autenticação OAuth 2,0 com o Azure AD (recomendado). Para obter mais informações sobre a substituição do ACS, consulte [esta postagem no blog](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como autenticar com o Azure AD, consulte os seguintes artigos:

- [Autenticação com identidades gerenciadas](service-bus-managed-service-identity.md)
- [Autenticação de um aplicativo](authenticate-application.md)

Para obter mais informações sobre como autenticar com SAS, consulte os seguintes artigos:

- [Autenticação com SAS](service-bus-sas.md)
