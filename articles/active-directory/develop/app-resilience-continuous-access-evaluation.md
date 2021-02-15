---
title: Como usar APIs habilitadas para avaliação de acesso contínuo em seus aplicativos | Azure
titleSuffix: Microsoft identity platform
description: Como aumentar a segurança e a resiliência do aplicativo adicionando suporte para avaliação de acesso contínuo, habilitando tokens de acesso de vida longa que podem ser revogados com base em eventos críticos e na avaliação da política.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652051"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Como usar APIs habilitadas para avaliação de acesso contínuo em seus aplicativos

A [Continuous Access Evaluation](../conditional-access/concept-continuous-access-evaluation.md) (CAE) é um padrão industrial emergente que permite que os tokens de acesso sejam revogados com base em [eventos críticos](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) e na [avaliação da política](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) , em vez de depender da expiração do token com base no tempo de vida. Para algumas APIs de recursos, como o risco e a política são avaliados em tempo real, isso pode aumentar a vida útil de token até 28 horas. Esses tokens de vida útil serão atualizados de forma proativa pela MSAL (biblioteca de autenticação da Microsoft), aumentando a resiliência de seus aplicativos.

Este artigo mostra como usar APIs habilitadas para CAE em seus aplicativos.

## <a name="implementation-considerations"></a>Considerações sobre a implementação

Para usar a avaliação de acesso contínuo, seu aplicativo e a API de recurso que ele está acessando devem ser habilitados para CAE. No entanto, preparar seu código para usar um recurso habilitado para CAE não impedirá o uso de APIs que não sejam CAE habilitadas.

Se uma API de recurso implementar o CAE e seu aplicativo declará-lo pode tratar de CAE, seu aplicativo obterá tokens CAE para esse recurso. Por esse motivo, se você declarar seu aplicativo CAE pronto, seu aplicativo deverá lidar com o desafio de declaração CAE para todas as APIs de recurso que aceitam tokens de acesso de identidade da Microsoft. Se você não tratar as respostas CAE nessas chamadas à API, seu aplicativo poderá terminar em um loop repetindo uma chamada à API com um token que ainda está no ciclo de vida retornado do token, mas que foi revogado devido a CAE.

## <a name="the-code"></a>O código

A primeira etapa é adicionar código para manipular uma resposta da API de recursos rejeitando a chamada devido a CAE. Com o CAE, as APIs retornarão um status 401 e um cabeçalho WWW-Authenticate quando o token de acesso tiver sido revogado ou a API detectar uma alteração no endereço IP usado. O cabeçalho de WWW-Authenticate contém um desafio de declarações que o aplicativo pode usar para adquirir um novo token de acesso.

Por exemplo:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Seu aplicativo verificaria:

- a chamada à API que retorna o status 401
- a existência de um cabeçalho de WWW-Authenticate que contém:
  - um parâmetro "Error" com o valor "insufficient_claims"
  - um parâmetro "Claims"

Quando essas condições são atendidas, o aplicativo pode extrair e decodificar o desafio de declarações.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Em seguida, seu aplicativo usaria o desafio de declarações para adquirir um novo token de acesso para o recurso.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Quando seu aplicativo estiver pronto para lidar com o desafio de declaração retornado por um recurso CAE habilitado, você poderá informar ao Microsoft Identity seu aplicativo está pronto para ser preparado. Para fazer isso em seu aplicativo MSAL, crie seu cliente público usando os recursos de cliente do "CP1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Você pode testar seu aplicativo conectando um usuário ao aplicativo usando o portal do Azure para revogar as sessões do usuário. Na próxima vez que o aplicativo chamar a API habilitada para CAE, o usuário será solicitado a autenticar novamente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte [avaliação de acesso contínuo](../conditional-access/concept-continuous-access-evaluation.md).
