---
title: Mover um aplicativo de daemon que chama APIs da Web para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como mover um aplicativo daemon que chama APIs da Web para produção
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578440"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplicativo daemon que chama APIs da Web – mover para produção

Agora que você sabe como adquirir e usar um token para uma chamada de serviço a serviço, saiba como mover seu aplicativo para produção.

## <a name="deployment---multitenant-daemon-apps"></a>Implantação – aplicativos de daemon multilocatário

Se você for um ISV criando um aplicativo daemon que pode ser executado em vários locatários, certifique-se de que o administrador do locatário:

- Provisiona uma entidade de serviço para o aplicativo.
- Concede consentimento ao aplicativo.

Você precisará explicar aos seus clientes como executar essas operações. Para obter mais informações, consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Exemplos de código

# <a name="net"></a>[.NET](#tab/dotnet)

- Documentação de referência para:
  - Criando uma instância de [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chamando [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Outros exemplos/tutoriais:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta um pequeno aplicativo de console de daemon do .NET Core que exibe os usuários de uma consulta de locatário Microsoft Graph.

    ![Topologia de aplicativo de daemon de exemplo](media/scenario-daemon-app/daemon-app-sample.svg)

    O mesmo exemplo também ilustra uma variação com certificados:

    ![Topologia de aplicativo daemon de exemplo-certificados](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta um aplicativo Web ASP.NET MVC que sincroniza dados de Microsoft Graph usando a identidade do aplicativo em vez de em nome de um usuário. Este exemplo também ilustra o processo de consentimento do administrador.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console Java usando a identidade do aplicativo](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Para obter mais informações, consulte:
  - Compreendendo a [configuração](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - Instanciando [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [perguntas frequentes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Outros exemplos/tutoriais:
  - [Exemplo de daemon de console de nó MSAL](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console do Python usando a identidade do aplicativo](quickstart-v2-python-daemon.md).

---

## <a name="next-steps"></a>Próximas etapas

Aqui estão alguns links para ajudá-lo a saber mais:

# <a name="net"></a>[.NET](#tab/dotnet)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console do .NET Core usando a identidade do aplicativo](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console Java usando a identidade do aplicativo](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console Node.js usando a identidade do aplicativo](quickstart-v2-nodejs-console.md).

# <a name="python"></a>[Python](#tab/python)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console do Python usando a identidade do aplicativo](quickstart-v2-python-daemon.md).

---
