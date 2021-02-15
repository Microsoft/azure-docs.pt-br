---
title: Autenticação e autorização de Retransmissão do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da autenticação SAS (assinatura de acesso compartilhado) com o serviço de retransmissão do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033370"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticação e autorização da Retransmissão do Azure

Os aplicativos podem autenticar a Retransmissão do Azure usando a autenticação de SAS (Assinatura de Acesso Compartilhado). A autenticação SAS permite que os aplicativos se autentiquem no serviço de Retransmissão do Azure usando uma chave de acesso configurada no namespace da Retransmissão. Em seguida, você pode usar essa chave para gerar um token de Assinatura de Acesso Compartilhado que os clientes podem usar para se autenticar no serviço de retransmissão.

## <a name="shared-access-signature-authentication"></a>Autenticação SAS

A [autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite que você conceda a um usuário acesso a recursos de Retransmissão do Azure com direitos específicos. A autenticação SAS envolve a configuração de uma chave de criptografia com direitos associados em um recurso. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace da Retransmissão. Ao contrário do sistema de mensagens do Barramento de Serviço, as [Conexões Híbridas de Retransmissão](relay-hybrid-connections-protocol.md) dão suporte a remetentes anônimos ou não autorizados. Você pode habilitar o acesso anônimo para a entidade ao criá-la, conforme mostrado na seguinte captura de tela do portal:

![Uma caixa de diálogo intitulada "criar conexão híbrida" tem uma caixa de texto "nome" e uma caixa de seleção rotulada "requer autenticação de cliente", que está marcada.][0]

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace de retransmissão que consista no seguinte:

* *KeyName* que identifica a regra.
* *PrimaryKey* é uma chave criptográfica usada para assinar/validar tokens SAS.
* *SecondaryKey* é uma chave criptográfica usada para assinar/validar tokens SAS.
* *Rights* representando a coleção de direitos de escuta, envio ou gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as conexões de retransmissão em um namespace para clientes com tokens assinados usando a chave correspondente. Até 12 regras de autorização podem ser configuradas em um namespace de Retransmissão. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para a Retransmissão do Azure está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Próximas etapas

- Continue lendo [Autenticação de Barramento de Serviço com Assinaturas de Acesso Compartilhado](../service-bus-messaging/service-bus-sas.md) para obter mais detalhes sobre SAS.
- Consulte o [guia de protocolo de Conexões Híbridas de Retransmissão do Azure](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a funcionalidade de Conexões Híbridas.
- Para informações correspondentes sobre a autenticação do sistema de mensagens do Barramento de Serviço, confira [Autenticação e autorização do Barramento de Serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png