---
title: Tratar erros e exceções em MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, desafios de declarações de acesso condicional e repetições no MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584037"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Tratar erros e exceções em MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Tratamento de erro em MSAL.NET

Ao processar exceções .NET, é possível usar o próprio tipo de exceção e o membro `ErrorCode` para distinguir entre exceções. Os valores `ErrorCode` são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Você também pode dar uma olhada nos campos [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Caso [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) seja gerado, tente [Códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-net-exceptions"></a>Exceções .NET comuns

Aqui são apresentadas as exceções comuns que podem ser geradas e algumas possíveis mitigações:  

| Exceção | Código do erro | Atenuação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: O usuário ou administrador não consentiu em usar o aplicativo com a ID '{appId}' nomeada '{appName}'. Envie uma solicitação de autorização interativa para esse usuário e recurso.| Obtenha consentimento do usuário primeiro. Caso não esteja usando o .NET Core (que não tem nenhuma interface do usuário da Web), chame (apenas uma vez) `AcquireTokeninteractive`. Se você estiver usando .NET Core ou não quiser fazer um `AcquireTokenInteractive`, o usuário poderá navegar em uma URL para dar o consentimento: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. para chamar `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: O usuário deverá usar a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md).| Não há mitigação. Se a MFA estiver configurada para seu locatário e Azure Active Directory (AAD) decidir imaplicá-la, volte para um fluxo interativo, como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: Não há suporte para o tipo de concessão nos pontos de extremidade */common* ou */consumers*. Use o ponto de extremidade */organizations* ou específico do locatário. Você usou */common*.| Conforme explicado na mensagem do Azure AD, a autoridade deve ter um locatário ou, caso contrário, */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: O corpo da solicitação deve conter o seguinte parâmetro: `client_secret or client_assertion`.| Essa exceção poderá ser gerada caso o aplicativo não esteja registrado como um aplicativo cliente público no Azure AD. No portal do Azure, edite o manifesto do aplicativo e defina `allowPublicClient` como `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Não foi possível identificar o usuário conectado| A biblioteca não pôde consultar o usuário conectado do Windows atual ou esse usuário não é um AD ou ingresso no Azure AD (não há suporte para usuários ingressados no local de trabalho). Mitigação 1: na Plataforma Universal do Windows, verifique se o aplicativo tem os seguintes recursos: Autenticação Empresarial, Redes Privadas (Cliente e Servidor), Informações sobre a Conta de Usuário. Mitigação 2: Implemente sua própria lógica para buscar o nome de usuário (por exemplo, john@contoso.com) e use o formulário `AcquireTokenByIntegratedWindowsAuth` que recebe o nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Esse método depende de um protocolo exposto pelo AD (Active Directory). Se um usuário tiver sido criado no Azure AD sem backup de AD (usuário "gerenciado"), esse método falhará. Os usuários criados no AD e apoiados pelo Azure AD (usuários "federados") podem se beneficiar desse método de autenticação não interativo. Mitigação: Use autenticação interativa.|

### `MsalUiRequiredException`

Um código de status comum que retornado do MSAL.NET ao chamar `AcquireTokenSilent()` é `MsalError.InvalidGrantError`. Esse código de status significa que o aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicativos cliente públicos, tem um desafio em aplicativos Web). Isso acontece porque é preciso ter a interação adicional do usuário antes que o token de autenticação possa ser emitido.

Na maioria das vezes em que `AcquireTokenSilent` falha, deve-se ao fato de o cache de token não ter tokens que correspondam à sua solicitação. Os tokens de acesso expiram em 1 hora e `AcquireTokenSilent` tentará buscar um novo com base em um token de atualização (em termos de OAuth2, esse é o fluxo do "Token de atualização"). Esse fluxo também pode falhar por vários motivos; por exemplo, caso um administrador de locatários configure políticas de logon mais rigorosas. 

A interação tem como objetivo fazer com que o usuário realize uma ação. Algumas dessas condições são de fácil resolução por parte dos usuários (por exemplo, aceitar os Termos de Uso com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, o computador em questão precisa se conectar a uma rede corporativa específica). Algumas ajudam a configurar o usuário na autenticação multifator ou a instalar o Microsoft Authenticator em seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>Enumeração da classificação de `MsalUiRequiredException`

O MSAL expõe um `Classification` campo, que você pode ler para fornecer uma melhor experiência do usuário. Por exemplo, para informar ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da enumeração de `UiRequiredExceptionClassification`:

| classificação    | Significado           | Ações recomendadas |
|-------------------|-------------------|----------------------|
| BasicAction | A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame AcquireTokenInteractively(). |
| AdditionalAction | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo da autenticação interativa. | Chame AcquireTokenInteractively() para mostrar uma mensagem que explique a ação corretiva. O aplicativo da chamada pode optar por ocultar fluxos que exigem additional_action caso seja improvável que o usuário conclua a ação corretiva. |
| MessageOnly      | A condição não pode ser resolvida atualmente. Ao iniciar o fluxo da autenticação interativa, será exibida uma mensagem explicando a condição. | Chame AcquireTokenInteractively() para mostrar uma mensagem que explique a condição. AcquireTokenInteractively() retornará um erro UserCanceled depois que o usuário ler a mensagem e fechar a janela. O aplicativo da chamada pode optar por ocultar os fluxos que resultam em message_only caso seja improvável que o usuário se beneficie da mensagem.|
| ConsentRequired  | O consentimento do usuário está ausente ou foi revogado. | Chame AcquireTokenInteractively() para que o usuário dê consentimento. |
| UserPasswordExpired | A senha do usuário expirou. | Chame AcquireTokenInteractively() para que o usuário possa redefinir sua senha. |
| PromptNeverFailed| A autenticação interativa foi chamada com o parâmetro prompt=never, forçando a MSAL a depender de cookies do navegador e não exibir o navegador. Isso falhou. | Chame AcquireTokenInteractively() sem Prompt.None |
| AcquireTokenSilentFailed | O SDK da MSAL não tem informações suficientes para buscar um token do cache. Isso pode ocorrer porque nenhum token está no cache ou porque uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Chame AcquireTokenInteractively(). |
| Nenhum    | Nenhum detalhe adicional é fornecido. A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Exemplo de código do .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ao chamar uma API que exige acesso condicional do MSAL.NET, o aplicativo deverá tratar as exceções de desafio de declaração. Isso aparecerá como [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) em que a propriedade [Declarações](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) não estará vazia.

Para tratar o desafio de declaração, será necessário usar o método `.WithClaim()` da classe `PublicClientApplicationBuilder`.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

O MSAL.NET implementa um mecanismo de nova tentativa única simples para erros com códigos de erro HTTP 500-600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) é exibida `System.Net.Http.Headers.HttpResponseHeaders` como uma propriedade `namedHeaders`. É possível usar informações adicionais do código de erro para melhorar a confiabilidade dos aplicativos. No caso descrito, é possível usar o `RetryAfterproperty` (do tipo `RetryConditionHeaderValue`) e calcular quando tentar novamente.

Veja um exemplo de um aplicativo daemon usando o fluxo de credenciais do cliente. Você pode adaptá-lo a qualquer um dos métodos para adquirir um token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Próximas etapas

Considere habilitar o [registro em log no MSAL.net](msal-logging-dotnet.md) para ajudá-lo a diagnosticar e depurar problemas.
