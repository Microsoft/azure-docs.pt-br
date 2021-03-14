---
title: Autenticar nos serviços de comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar nos serviços de comunicação.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485954"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar nos serviços de comunicação do Azure

Cada interação do cliente com os serviços de comunicação do Azure precisa ser autenticada. Em uma arquitetura típica, consulte [arquitetura de cliente e servidor](./client-and-server-architecture.md), *chaves de acesso* ou *identidade gerenciada* é usada no serviço de acesso de usuário confiável para criar usuários e emitir tokens. E o *token de acesso do usuário* emitido pelo serviço de acesso de usuário confiável é usado para que aplicativos cliente acessem outros serviços de comunicação, por exemplo, chat ou serviço de chamada.

O serviço SMS de serviços de comunicação do Azure também aceita *chaves de acesso* ou *identidade gerenciada* para autenticação. Isso normalmente acontece em um aplicativo de serviço em execução em um ambiente de serviço confiável.

Cada opção de autorização é descrita brevemente abaixo:

- Autenticação de **chave de acesso** para operações de SMS e de identidade. A autenticação de chave de acesso é adequada para aplicativos de serviço em execução em um ambiente de serviço confiável. A chave de acesso pode ser encontrada no portal dos serviços de comunicação do Azure. Para autenticar com uma chave de acesso, um aplicativo de serviço usa a chave de acesso como credencial para inicializar as bibliotecas de cliente de identidade ou SMS correspondentes, consulte [criar e gerenciar tokens de acesso](../quickstarts/access-tokens.md). Como a chave de acesso faz parte da cadeia de conexão do recurso, consulte [criar e gerenciar recursos dos serviços de comunicação](../quickstarts/create-communication-resource.md), a autenticação com a cadeia de conexão é equivalente à autenticação com a chave de acesso.
- Autenticação de **identidade gerenciada** para operações de SMS e de identidade. Identidade gerenciada, consulte [identidade gerenciada](../quickstarts/managed-identity.md), é adequada para aplicativos de serviço em execução em um ambiente de serviço confiável. Para autenticar com uma identidade gerenciada, um aplicativo de serviço cria uma credencial com a ID e um segredo da identidade gerenciada e, em seguida, inicializa as bibliotecas de cliente de identidade ou SMS correspondentes, consulte [criar e gerenciar tokens de acesso](../quickstarts/access-tokens.md).
- Autenticação de **token de acesso do usuário** para chat e chamada. Os tokens de acesso do usuário permitem que seus aplicativos cliente se autentiquem no chat de comunicação do Azure e serviços de chamada. Esses tokens são gerados em um "serviço de acesso de usuário confiável" que você cria. Em seguida, eles são fornecidos aos dispositivos cliente que usam o token para inicializar o chat e chamar as bibliotecas de cliente. Para obter mais informações, consulte [Adicionar chat ao seu aplicativo](../quickstarts/chat/get-started.md) por exemplo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e gerenciar recursos](../quickstarts/create-communication-resource.md) 
>  de serviços de comunicação [Criar um aplicativo de identidade gerenciada Azure Active Directory do CLI do Azure](../quickstarts/managed-identity-from-cli.md) 
>  [Criando tokens de acesso do usuário](../quickstarts/access-tokens.md)

Para obter mais informações, consulte os seguintes artigos:
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
