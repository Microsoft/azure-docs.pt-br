---
title: Tratar erros e exceções em MSAL4J
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, desafios de declarações de acesso condicional e novas tentativas em aplicativos MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760701"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Tratar erros e exceções em MSAL para Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Tratamento de erro em MSAL para Java

Na MSAL para Java, há três tipos de exceções: `MsalClientException`, `MsalServiceException`e `MsalInteractionRequiredException`; todas que herdam de `MsalException`.

- `MsalClientException` será gerado quando ocorrer um erro local em relação à biblioteca ou ao dispositivo.
- `MsalServiceException` será gerado quando o STS (serviço de token de segurança) retornar uma resposta de erro ou ocorrer outro erro de rede.
- `MsalInteractionRequiredException` será gerado quando a interação da interface do usuário for necessária para que a autenticação seja realizada com sucesso.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expõe cabeçalhos HTTP retornados nas solicitações para STS. Acesse-os via `MsalServiceException.headers()`.

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um código de status comum que retornado da MSAL para Java ao chamar `AcquireTokenSilently()` é `InvalidGrantError`. Isso indica que é preciso ter uma interação adicional do usuário antes que um token de autenticação possa ser emitido. Seu aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo, por meio do envio de `AuthorizationCodeParameters` ou `DeviceCodeParameters` para aplicativos cliente públicos.

Na maioria das vezes em que `AcquireTokenSilently` falha, deve-se ao fato de o cache de token não ter um token que correspondam à sua solicitação. Os tokens de acesso expiram em uma hora, e `AcquireTokenSilently` tentará obter um novo com base em um token de atualização. Em termos de OAuth2, esse é o fluxo do Token de atualização. Esse fluxo também pode falhar por vários motivos, como quando um administrador de locatários configurar políticas de logon mais rigorosas.

Algumas condições que resultam nesse erro são de fácil resolução pro parte dos usuários. Por exemplo, talvez seja necessário aceitar os termos de uso ou a solicitação não pode ser atendida com a configuração atual porque a máquina precisa se conectar a uma rede corporativa específica.

A MSAL expõe um campo `reason`, que pode ser usado para fornecer uma melhor experiência do usuário. Por exemplo, o campo `reason` pode levar você a informar ao usuário que a senha dele expirou ou que ele precisará fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da enumeração de `InteractionRequiredExceptionReason`:

| Motivo | Significado | Ações recomendadas |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos. |
| `AdditionalAction` | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a ser tomada. O aplicativo da chamada pode optar por ocultar fluxos que requeiram uma ação adicional caso seja improvável que o usuário conclua a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida atualmente. Inicie o fluxo da autenticação interativa para exibir uma mensagem explicando a condição. | Chame `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken` retornará o erro `UserCanceled` depois que o usuário ler a mensagem e fechar a janela. O aplicativo pode optar por ocultar os fluxos que resultam em mensagem caso seja improvável que o usuário se beneficie dela. |
| `ConsentRequired`| O consentimento do usuário está ausente ou foi revogado. |Chame `acquireToken` com parâmetros interativos para que o usuário possa dar seu consentimento. |
| `UserPasswordExpired` | A senha do usuário expirou. | Chame `acquireToken` com o parâmetro Interactive para que o usuário possa redefinir sua senha. |
| `None` |  Mais detalhes são fornecidos abaixo. A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos. |

### <a name="code-example"></a>Exemplo de código

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Próximas etapas

Considere habilitar o [registro em log no MSAL para Java](msal-logging-java.md) para ajudá-lo a diagnosticar e depurar problemas.
