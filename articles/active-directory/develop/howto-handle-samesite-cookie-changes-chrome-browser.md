---
title: Como lidar com alterações de cookie SameSite no navegador Chrome | Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com alterações de cookie SameSite no navegador Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94444904"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Manipular alterações de cookie SameSite no navegador Chrome

## <a name="what-is-samesite"></a>O que é o SameSite?

`SameSite` é uma propriedade que pode ser definida em cookies HTTP para evitar ataques CSRF (falsificação de solicitação entre sites) em aplicativos Web:

- Quando `SameSite` é definido como **LAX**, o cookie é enviado em solicitações no mesmo site e em solicitações GET de outros sites. Ele não é enviado em solicitações GET que são entre domínios.
- Um valor **estrito** garante que o cookie seja enviado em solicitações somente dentro do mesmo site.

Por padrão, o `SameSite` valor não é definido em navegadores e é por isso que não há restrições em cookies enviados em solicitações. Um aplicativo precisaria aceitar a proteção CSRF definindo **LAX** ou **estrito** de acordo com seus requisitos.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite alterações e impacto na autenticação

[As atualizações recentes dos padrões no SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propõem a proteção de aplicativos, tornando o comportamento padrão de `SameSite` quando nenhum valor for definido como LAX. Essa mitigação significa que os cookies serão restritos em solicitações HTTP, exceto por serem obtidos de outros sites. Além disso, um valor de **nenhum** é introduzido para remover restrições em cookies que estão sendo enviados. Em breve, essas atualizações serão lançadas em uma versão futura do navegador Chrome.

Quando os aplicativos Web são autenticados com a plataforma de identidade da Microsoft usando o modo de resposta "form_post", o servidor de logon responde ao aplicativo usando um HTTP POST para enviar os tokens ou o código de autenticação. Como essa solicitação é uma solicitação entre domínios (do `login.microsoftonline.com` ao seu domínio, por exemplo `https://contoso.com/auth` ), os cookies que foram definidos pelo seu aplicativo agora se enquadram nas novas regras no Chrome. Os cookies que precisam ser usados em cenários entre sites são cookies que contêm os valores de *estado* e *nonce* , que também são enviados na solicitação de logon. Há outros cookies descartados pelo Azure AD para manter a sessão.

Se você não atualizar seus aplicativos Web, esse novo comportamento resultará em falhas de autenticação.

## <a name="mitigation-and-samples"></a>Mitigação e amostras

Para superar as falhas de autenticação, os aplicativos Web que se autenticam com a plataforma Microsoft Identity podem definir a `SameSite` propriedade como `None` para cookies que são usados em cenários entre domínios quando executados no navegador Chrome.
Outros navegadores (consulte [aqui](https://www.chromium.org/updates/same-site/incompatible-clients) para obter uma lista completa) seguem o comportamento anterior de `SameSite` e não incluirá os cookies se `SameSite=None` estiver definido.
É por isso que, para dar suporte à autenticação em vários navegadores, os aplicativos Web precisarão definir o `SameSite` valor para `None` somente no Chrome e deixar o valor vazio em outros navegadores.

Essa abordagem é demonstrada em nossos exemplos de código abaixo.

# <a name="net"></a>[.NET](#tab/dotnet)

A tabela a seguir apresenta as solicitações pull que trabalharam nas alterações SameSite em nossos exemplos de ASP.NET e de ASP.NET Core.

| Amostra | Solicitação de pull |
| ------ | ------------ |
|  [Tutorial incremental do aplicativo Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Mesma correção de cookie de site #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Exemplo de aplicativo Web do ASP.NET MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Mesma correção de cookie de site #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-dotnet-admin-Restricted-escopos-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Mesma correção de cookie de site #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

para obter detalhes sobre como lidar com cookies SameSite em ASP.NET e ASP.NET Core, consulte também:

- [Trabalhar com SameSite cookies no ASP.NET Core](/aspnet/core/security/samesite) .
- [Blog do ASP.NET sobre o problema do SameSite](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Amostra |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Amostra | Solicitação de pull |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Mesma correção de cookie de site #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-webAPI](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Mesma correção de cookie de site #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SameSite e o cenário do aplicativo Web:

- [Perguntas frequentes sobre o Google Chrome no SameSite](https://www.chromium.org/updates/same-site/faq)

- [Página Chromium SameSite](https://www.chromium.org/updates/same-site)

- [Cenário: Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md)