---
title: Adquirir tokens de cache & com MSAL (biblioteca de autenticação da Microsoft) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre como adquirir e armazenar em cache tokens usando o MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755064"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Adquirir e armazenar tokens em cache usando a MSAL (biblioteca de autenticação da Microsoft)

Os [tokens de acesso](access-tokens.md) permitem que os clientes chamem com segurança as APIs Web protegidas pelo Azure. Há várias maneiras de adquirir um token usando a MSAL (biblioteca de autenticação da Microsoft). Alguns exigem a interação do usuário por meio de um navegador da Web, enquanto outros não exigem interação do usuário. Em geral, o método usado para adquirir um token depende de o aplicativo ser um aplicativo cliente público, como desktop ou aplicativo móvel, ou um aplicativo cliente confidencial, como aplicativo Web, API da Web ou aplicativos daemon.

MSAL armazena em cache um token depois que ele é adquirido. O código do aplicativo deve primeiro tentar obter um token silenciosamente do cache antes de tentar adquirir um token por outros meios.

Também é possível limpar o cache do token removendo as contas do cache. No entanto, isso não remove o cookie de sessão que está no navegador.

## <a name="scopes-when-acquiring-tokens"></a>Escopos ao adquirir tokens

[Escopos](v2-permissions-and-consent.md) são as permissões que uma API Web expõe para a qual os aplicativos cliente podem solicitar acesso. Os aplicativos cliente solicitam o consentimento do usuário para esses escopos quando fazem solicitações de autenticação para obter tokens para acessar as APIs da Web. O MSAL permite que você obtenha tokens para acessar o Azure AD para desenvolvedores (v 1.0) e as APIs da plataforma de identidade da Microsoft. O protocolo v2.0 usa escopos em vez de recursos nas solicitações. Confira mais informações na [comparação entre a v1.0 e a v2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Com base na configuração da API Web da versão do token que ela aceita, o ponto de extremidade da v2.0 retorna o token de acesso à MSAL.

Vários métodos de aquisição de token do MSAL exigem um `scopes` parâmetro. O `scopes` parâmetro é uma lista de cadeias de caracteres que declaram as permissões desejadas e os recursos solicitados. Os escopos bem conhecidos são as [permissões de Microsoft Graph](/graph/permissions-reference).

Na MSAL, também é possível acessar recursos da versão 1.0. Para obter mais informações, consulte [escopos para um aplicativo v 1.0](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Escopos de solicitação para uma API Web

Quando seu aplicativo precisar solicitar um token de acesso com permissões específicas para uma API de recurso, passe os escopos que contêm o URI de ID do aplicativo da API no formato `<app ID URI>/<scope>` .

Alguns exemplos de valores de escopo para recursos diferentes:

- API de Microsoft Graph: `https://graph.microsoft.com/User.Read`
- API Web personalizada: `api://11111111-1111-1111-1111-111111111111/api.read`

O formato do valor do escopo varia de acordo com o recurso (a API) que recebe o token de acesso e os `aud` valores de declaração que ele aceita.

Somente para Microsoft Graph, o `user.read` escopo é mapeado para `https://graph.microsoft.com/User.Read` , e ambos os formatos de escopo podem ser usados de forma intercambiável.

Determinadas APIs da Web, como a API de Azure Resource Manager ( https://management.core.windows.net/) espere uma barra "/") na declaração de público ( `aud` ) do token de acesso. Nesse caso, passe o escopo como `https://management.core.windows.net//user_impersonation` , incluindo a barra dupla ('//').

Outras APIs podem exigir que *nenhum esquema ou host* esteja incluído no valor do escopo e espere apenas a ID do aplicativo (um GUID) e o nome do escopo, por exemplo:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Se o recurso downstream não estiver sob seu controle, talvez seja necessário tentar diferentes formatos de valor de escopo (por exemplo, com/sem esquema e host) se você receber `401` ou outros erros ao passar o token de acesso para o recurso.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Solicitar escopos dinâmicos de consentimento incremental

Conforme os recursos fornecidos pelo seu aplicativo ou seus requisitos mudam, você pode solicitar permissões adicionais, conforme necessário, usando o parâmetro de escopo. Esses *escopos dinâmicos* permitem que os usuários forneçam consentimento incremental para os escopos.

Por exemplo, você pode entrar no usuário, mas inicialmente negar acesso a todos os recursos. Posteriormente, você pode dar a eles a capacidade de exibir seus calendários solicitando o escopo do calendário no método de token de aquisição e obtendo o consentimento do usuário para fazer isso. Por exemplo, solicitando os `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` escopos e.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Adquirir tokens silenciosamente (do cache)

O MSAL mantém um cache de token (ou dois caches para aplicativos cliente confidenciais) e armazena em cache um token depois que ele é adquirido. Em muitos casos, a tentativa de obter um token silenciosamente adquirirá outro token com mais escopos tendo como base um token armazenado no cache. Além disso, a MSAL também pode atualizar um token quando a expiração estiver próxima (pois o cache de tokens também contém um token de atualização).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendado para aplicativos cliente públicos

O código do aplicativo deve primeiro tentar obter um token silenciosamente do cache. Se a chamada do método retornar um erro ou exceção "IU necessária", tente adquirir um token por outros meios.

Há dois fluxos, no entanto, em que você **não deve** tentar adquirir um token silenciosamente:

- [Fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials), que não usa o cache de token de usuário, mas um cache de token de aplicativo. Esse método cuida da verificação do cache do token do aplicativo antes de enviar uma solicitação ao serviço de token de segurança (STS).
- O [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web, pois ele resgata um código que o aplicativo obteve ao assinar o usuário e que os consentiu em mais escopos. Como um código e não uma conta é passado como um parâmetro, o método não pode procurar no cache antes de resgatar o código, o que invoca uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Padrão de chamada recomendado em aplicativos Web usando o fluxo de código de autorização

Para aplicativos Web que usam o [fluxo de código de autorização do OpenID Connect](v2-protocols-oidc.md), o padrão recomendado nos controladores é:

- Instancie um aplicativo cliente confidencial com um cache de tokens com serialização personalizada.
- Adquira o token usando o fluxo do código de autorização

## <a name="acquiring-tokens"></a>Adquirir tokens

Em geral, o método de aquisição de um token variará se ele for um aplicativo cliente público ou confidencial.

### <a name="public-client-applications"></a>Aplicativos cliente públicos

Para aplicativos cliente públicos (área de trabalho ou aplicativo móvel), você:

- Geralmente adquire tokens interativamente fazendo com que o usuário faça conexão pela interface do usuário ou por uma janela pop-up.
- Pode [obter um token silenciosamente para o usuário conectado](msal-authentication-flows.md#integrated-windows-authentication) usando a Autenticação Integrada do Windows (IWA/Kerberos) se o aplicativo da área de trabalho estiver sendo executado em um computador Windows associado a um domínio ou ao Azure.
- Pode [obter um token com um nome de usuário e senha](msal-authentication-flows.md#usernamepassword) em aplicativos cliente de desktop do .NET Framework (não recomendado). Não use o nome de usuário e senha em aplicativos cliente confidenciais.
- O pode adquirir um token por meio do [fluxo de código do dispositivo](msal-authentication-flows.md#device-code) em aplicativos em execução em dispositivos que não têm um navegador da Web. O usuário recebe uma URL e um código. Depois, ele acessa um navegador da Web em outro dispositivo, insere o código e entra. Em seguida, o Azure AD envia um token de volta ao dispositivo sem navegador.

### <a name="confidential-client-applications"></a>Aplicativos cliente confidenciais

Para aplicativos cliente confidenciais (aplicativo Web, API da Web ou um aplicativo daemon como um serviço do Windows), você:

- Adquire tokens **para o próprio aplicativo** e não para um usuário utilizando o [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials). Essa técnica pode ser usada para ferramentas de sincronização ou ferramentas que processam usuários em geral e não um usuário específico.
- Use o [fluxo em nome de](msal-authentication-flows.md#on-behalf-of) de uma API da Web para chamar uma API em nome do usuário. O aplicativo é identificado com credenciais de cliente para adquirir um token com base em uma asserção de usuário (SAML, por exemplo, ou um token JWT). Esse fluxo é usado por aplicativos que precisam acessar recursos de um usuário específico em chamadas de serviço a serviço.
- Adquira tokens usando o [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web depois que o usuário entrar usando a URL de solicitação de autorização. O aplicativo do OpenID Connect geralmente usa esse mecanismo que permite ao usuário entrar usando o Open ID Connect e acessar as APIs Web em nome do usuário.

## <a name="authentication-results"></a>Resultados da autenticação

Quando o cliente solicita um token de acesso, o AD do Azure também retorna um resultado de autenticação que inclui metadados sobre o token de acesso. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem ao aplicativo realizar o cache inteligente dos tokens de acesso sem precisar analisar o token de acesso em si. O resultado da autenticação expõe:

- O [token de acesso](access-tokens.md) da API Web para acessar os recursos. Essa cadeia de caracteres é geralmente um JWT codificado em base64, mas o cliente nunca deve olhar para dentro do token de acesso. O formato não tem garantia de permanecer estável e pode ser criptografado para o recurso. As pessoas que escrevem código dependendo do conteúdo do token de acesso no cliente são uma das fontes mais comuns de erros e quebra de lógica do cliente.
- O [token de ID](id-tokens.md) para o usuário (um JWT).
- A expiração do token, que informa a data/hora da expiração do token.
- A ID do locatário contém o locatário no qual o usuário foi encontrado. Para usuários convidados (cenários B2B do Azure AD), a ID do locatário é o locatário convidado e não o locatário exclusivo. Quando o token é entregue em nome de um usuário, o resultado da autenticação também contém informações sobre esse usuário. Para fluxos de clientes confidenciais em que os tokens são solicitados sem nenhum usuário (para o aplicativo), essas informações sobre o usuário são nulas.
- Os escopos para os quais o token foi emitido.
- A ID exclusiva para o usuário.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Avançadas Acessando os tokens em cache do usuário em aplicativos e serviços em segundo plano

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Próximas etapas

Várias das plataformas com suporte do MSAL têm informações adicionais relacionadas ao cache de token na documentação da biblioteca dessa plataforma. Por exemplo:
- [Obter um token do cache de token usando MSAL.NET](msal-net-acquire-token-silently.md)
- [Logon único com o MSAL.js](msal-js-sso.md)
- [Serialização de cache de token personalizada em MSAL para Python](msal-python-token-cache-serialization.md)
- [Serialização de cache de token personalizada em MSAL para Java](msal-java-token-cache-serialization.md)
