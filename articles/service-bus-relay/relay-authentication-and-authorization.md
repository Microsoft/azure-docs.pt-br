---
title: "Autenticação e autorização de Retransmissão do Azure | Microsoft Docs"
description: "Visão geral da autenticação SAS (Assinatura de Acesso Compartilhado) na Retransmissão do Azure"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 4ef8cbf22f2fcd7017af16083240608e5ca0fb5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticação e autorização da Retransmissão do Azure

Os aplicativos podem autenticar a Retransmissão do Azure usando a autenticação de SAS (Assinatura de Acesso Compartilhado). A autenticação SAS permite que os aplicativos se autentiquem no serviço de Retransmissão do Azure usando uma chave de acesso configurada no namespace da Retransmissão. Em seguida, você pode usar essa chave para gerar um token de Assinatura de Acesso Compartilhado que os clientes podem usar para se autenticar no serviço de retransmissão.

## <a name="shared-access-signature-authentication"></a>Autenticação SAS
A [autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite que você conceda a um usuário acesso a recursos de Retransmissão do Azure com direitos específicos. A autenticação SAS envolve a configuração de uma chave de criptografia com direitos associados em um recurso. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a chave configurada.

É possível configurar chaves para SAS em um namespace da Retransmissão. Ao contrário do sistema de mensagens do Barramento de Serviço, as [Conexões Híbridas de Retransmissão](relay-hybrid-connections-protocol.md) dão suporte a remetentes anônimos ou não autorizados. Você pode habilitar o acesso anônimo para a entidade quando você a criar, conforme mostrado na seguinte imagem capturada do portal:

![][0]

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace de retransmissão que consista no seguinte:

* *KeyName* , que identifica a regra.
* *PrimaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *Direitos* , que representa a coleção de direitos de Escuta, Envio ou Gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as conexões de retransmissão em um namespace para clientes com tokens assinados usando a chave correspondente. Até 12 regras de autorização podem ser configuradas em um namespace de Retransmissão. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para a Retransmissão do Azure está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Próximas etapas
- Continue lendo [Autenticação de Barramento de Serviço com Assinaturas de Acesso Compartilhado](../service-bus-messaging/service-bus-sas.md) para obter mais detalhes sobre SAS.
- Consulte o [guia de protocolo de Conexões Híbridas de Retransmissão do Azure](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a funcionalidade de Conexões Híbridas.
- Para informações correspondentes sobre a autenticação do sistema de mensagens do Barramento de Serviço, confira [Autenticação e autorização do Barramento de Serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png