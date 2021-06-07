---
title: Serialização de cache de token personalizada (Python MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como serializar o cache de token para MSAL para Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846183"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serialização de cache de token personalizada em MSAL para Python

No MSAL Python, um cache de token na memória que persiste pela duração da sessão de aplicativo, é fornecido por padrão quando você cria uma instância de [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serialização do cache de token, para que diferentes sessões do seu aplicativo possam acessá-lo, não é fornecido "pronto para uso". Isso ocorre porque o MSAL Python pode ser usado em tipos de aplicativos que não têm acesso ao sistema de arquivos, como aplicativos Web. Para ter um cache de token persistente em um aplicativo Python MSAL, você deve fornecer a serialização de cache de token personalizada.

As estratégias para serialização do cache de tokens diferem dependendo se você está escrevendo um aplicativo cliente público (Desktop) ou um aplicativo cliente confidencial (aplicativo Web, API Web ou aplicativo daemon).

## <a name="token-cache-for-a-public-client-application"></a>Cache de token para um aplicativo cliente público

Os aplicativos cliente públicos são executados no dispositivo de um usuário e gerenciam tokens para um único usuário. Nesse caso, você poderia serializar todo o cache em um arquivo. Lembre-se de fornecer bloqueio de arquivo se seu aplicativo ou outro aplicativo puder acessar o cache simultaneamente. Para obter um exemplo simples de como serializar um cache de token para um arquivo sem bloqueio, consulte o exemplo na documentação de referência da classe [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache de token para um aplicativo Web (aplicativo cliente confidencial)

Para aplicativos Web ou APIs Web, você pode usar a sessão ou um cache Redis ou um banco de dados para armazenar o cache de token. Deve haver um cache de token por usuário (por conta), portanto, certifique-se de serializar o cache de token por conta.

## <a name="next-steps"></a>Próximas etapas

Consulte [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) para obter um exemplo de como usar o cache de token para um aplicativo Web ou uma API Web do Windows ou Linux. O exemplo é para um aplicativo Web que chama a API de Microsoft Graph.
