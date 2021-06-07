---
title: Autenticar nos serviços de comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar nos serviços de comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110858"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar nos serviços de comunicação do Azure

Cada interação do cliente com os serviços de comunicação do Azure precisa ser autenticada. Em uma arquitetura típica, consulte [arquitetura de cliente e servidor](./client-and-server-architecture.md), *chaves de acesso* ou *identidades gerenciadas* são usadas para autenticação.

Outro tipo de autenticação usa *tokens de acesso do usuário* para se autenticar em serviços que exigem a participação do usuário. Por exemplo, o serviço de chat ou de chamada utiliza *tokens de acesso do usuário* para permitir que os usuários sejam adicionados em um thread e tenham conversas entre si.

## <a name="authentication-options"></a>Opções de Autenticação

A tabela a seguir mostra os SDKs dos serviços de comunicação do Azure e suas opções de autenticação:

| .    | Opção de autenticação                               |
| ----------------- | ----------------------------------------------------|
| Identidade          | Chave de acesso ou identidade gerenciada                      |
| sms               | Chave de acesso ou identidade gerenciada                      |
| Números de telefone     | Chave de acesso ou identidade gerenciada                      |
| Chamando           | Token de acesso do usuário                                   |
| Chat              | Token de acesso do usuário                                   |

Cada opção de autorização é descrita brevemente abaixo:

### <a name="access-key"></a>Chave de Acesso

A autenticação de chave de acesso é adequada para aplicativos de serviço em execução em um ambiente de serviço confiável. Sua chave de acesso pode ser encontrada no portal dos serviços de comunicação do Azure. O aplicativo de serviço o usa como uma credencial para inicializar os SDKs correspondentes. Veja um exemplo de como ele é usado no [SDK de identidade](../quickstarts/access-tokens.md). 

Como a chave de acesso faz parte da cadeia de conexão do recurso, a autenticação com uma cadeia de conexão é equivalente à autenticação com uma chave de acesso.

Se você quiser chamar as APIs do ACS manualmente usando uma chave de acesso, será necessário assinar a solicitação. A assinatura da solicitação é explicada em detalhes, em um [tutorial](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Identidade Gerenciada

Identidades gerenciadas, fornece segurança superior e facilidade de uso sobre outras opções de autorização. Por exemplo, usando o Azure AD, você evita ter que armazenar a chave de acesso da conta em seu código, como você faz com a autorização de chave de acesso. Embora você possa continuar a usar a autorização de chave de acesso com aplicativos de serviços de comunicação, a Microsoft recomenda migrar para o Azure AD sempre que possível. 

Para configurar uma identidade gerenciada, [crie um aplicativo registrado do CLI do Azure](../quickstarts/managed-identity-from-cli.md). Em seguida, o ponto de extremidade e as credenciais podem ser usados para autenticar os SDKs. Veja exemplos de como a [identidade gerenciada](../quickstarts/managed-identity.md) é usada.

### <a name="user-access-tokens"></a>Tokens de acesso do usuário

Os tokens de acesso do usuário são gerados usando o SDK de identidade e são associados aos usuários criados no SDK de identidade. Consulte um exemplo de como [criar usuários e gerar tokens](../quickstarts/access-tokens.md). Em seguida, os tokens de acesso do usuário são usados para autenticar os participantes adicionados às conversas no chat ou no SDK de chamada. Para obter mais informações, consulte [Adicionar chat ao seu aplicativo](../quickstarts/chat/get-started.md). A autenticação de token de acesso do usuário é diferente em comparação com a chave de acesso e a autenticação de identidade gerenciada, pois ela é usada para autenticar um usuário em vez de um recurso protegido do Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e gerenciar recursos](../quickstarts/create-communication-resource.md) 
>  de serviços de comunicação [Criar um aplicativo de identidade gerenciada Azure Active Directory do CLI do Azure](../quickstarts/managed-identity-from-cli.md) 
>  [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)

Para obter mais informações, consulte os seguintes artigos:
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
