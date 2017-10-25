---
title: "Autenticação e Autorização do Barramento de Serviço do Azure | Microsoft Docs"
description: "Autentique aplicativos no Barramento de Serviço com a autenticação SAS (Assinatura de Acesso Compartilhado)."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.openlocfilehash: b4b9d5d272bdb172f1d40db379a519a4f617550a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Barramento de Serviço

Os aplicativos obtêm acesso às funções do Barramento de Serviço do Azure usando a autenticação de token SAS (Assinatura de Acesso Compartilhado). Com a SAS, aplicativos apresentam ao Barramento de Serviço um token que foi assinado com uma chave simétrica conhecida tanto pelo emissor do token quanto pelo Barramento de Serviço ("compartilhada"), e essa chave é diretamente associada a uma regra de concessão de direitos de acesso específicos, tais como a permissão para receber/escutar ou enviar mensagens. Regras SAS são configuradas no namespace ou então diretamente em entidades, como uma fila ou tópico, permitindo o controle de acesso refinado.

Tokens SAS podem ser gerados por um cliente do barramento de serviço diretamente ou podem ser gerados por algum ponto de extremidade intermediário emissor de token com o qual o cliente interage. Por exemplo, um sistema poderá exigir que o cliente chame um ponto de extremidade de serviço Web protegido por autorização do Active Directory para provar a respectiva identidade e direitos de acesso ao sistema, e o serviço Web retornará em seguida o token de Barramento de Serviço apropriado. Esse token SAS pode ser facilmente gerado usando o provedor de token do Barramento de Serviço incluído no SDK. 

> [!IMPORTANT]
> Se você está usando o Controle de Acesso do Azure Active Directory (também conhecido como Serviço de Controle de Acesso ou ACS) em conjunto com o Barramento de Serviço, observe que o suporte para este método agora é limitado e que você deve migrar seu aplicativo para o uso de SAS. Para saber mais, confira [esta postagem no blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Autenticação SAS

[autenticação SAS](service-bus-sas.md) permite que você conceda a um usuário acesso a recursos de Barramento de Serviço com direitos específicos. A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace do Barramento de Serviço. A chave se aplica a todas as entidades de mensagens nesse namespace. Também é possível configurar chaves em tópicos e filas do Barramento de Serviço. Também há suporte para SAS na [Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace, uma fila ou um tópico. Essa regra consiste nos seguintes elementos:

* *KeyName* , que identifica a regra.
* *PrimaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *Direitos* , que representa a coleção de direitos de Escuta, Envio ou Gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a tecla correspondente. Até 12 regras de autorização podem ser configuradas em um namespace, fila ou tópico do Barramento de Serviço. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Próximas etapas

- Continue lendo [Autenticação de Barramento de Serviço com Assinaturas de Acesso Compartilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.
- [Alterações em namespaces habilitados para ACS.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Para informações correspondentes sobre a autenticação e autorização da Retransmissão do Azure, confira [Autenticação e autorização da Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md). 

