---
title: Práticas recomendadas de tratamento de erros para clientes da biblioteca de autenticação do Azure AD (ADAL)
description: Apresenta diretrizes e práticas recomendadas em tratamento de erro para aplicativos cliente ADAL.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e3ef8e32c3472f7a3861250f1845ce2e60ac868
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381000"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Práticas recomendadas em tratamento de erro para clientes da Biblioteca de autenticação do Azure Active Directory (ADAL)

Este artigo apresenta diretrizes sobre o tipo de erro que desenvolvedores podem encontrar ao usar ADAL para autenticar usuários. Ao usar ADAL, há vários casos em que um desenvolvedor pode precisar entrar e tratar erros. Um tratamento de erro apropriado garante uma ótima experiência do usuário final e limita o número de vezes em que o usuário final precisa entrar.

Neste artigo, exploramos os casos específicos de cada plataforma suportada por ADAL e como o aplicativo pode tratar cada caso corretamente. As diretrizes de erro estão divididas em duas categorias mais amplas, com base nos padrões de aquisição de token fornecidos por APIs da ADAL:

- **AcquireTokenSilent**: O cliente tenta obter um token silenciosamente (sem interface do usuário) e poderá falhar se a ADAL não for bem-sucedida. 
- **AcquireToken**: O cliente pode tentar uma aquisição silenciosa, mas também pode realizar solicitações interativas que exijam credenciais.

> [!TIP]
> É uma boa ideia registrar todos os erros e exceções ao usar a ADAL e o Azure AD. Os logs não apenas são úteis para entender a integridade geral do aplicativo, mas também são importantes durante a depuração de problemas mais amplos. Embora o aplicativo possa se recuperar de determinados erros, eles podem indicar problemas de design mais amplos que exijam alterações no código para serem resolvidos. 
> 
> Ao implementar as condições de erro abordadas neste documento, você deve registrar o código de erro e a descrição pelos motivos abordados anteriormente. Consulte a [Referência de erro e registro em log](#error-and-logging-reference) para obter exemplos de código de registro em log. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent tenta obter um token com a garantia de que o usuário final não veja uma interface de usuário (UI). Há vários casos em que a aquisição silenciosa pode falhar e precisa ser tratada por meio de solicitações interativas ou por um manipulador padrão. Mergulhamos nas especificações de quando e como usar cada caso nas seções a seguir.

Há um conjunto de erros gerados pelo sistema operacional, que pode exigir um tratamento de erro específico para o aplicativo. Para obter mais informações, consulte a seção de erros "Sistema operacional" em [Referência de erro e registro em log](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicativos

- Aplicativos de [cliente nativo](developer-glossary.md#native-client) (iOS, Android, .NET Desktop ou Xamarin)
- Aplicativos de [cliente da Web](developer-glossary.md#web-client) que chamam um [recurso](developer-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Casos de erro e etapas práticas

Basicamente, há dois casos de erros AcquireTokenSilent:

| Caso | Descrição |
|------|-------------|
| **Caso 1**: O erro pode ser resolvido com credenciais interativas | Para erros causados pela falta de tokens válidos, uma solicitação interativa é necessária. Mais especificamente, a pesquisa de cache e um token de atualização inválido/expirado exigem uma chamada AcquireToken para serem resolvidos.<br><br>Nesses casos, o usuário final precisa ser solicitado a entrar. O aplicativo pode optar por fazer uma solicitação interativa imediatamente, após a interação do usuário final (como apertar um botão de entrada) ou posterior. A escolha depende do comportamento desejado do aplicativo.<br><br>Consulte o código na seção a seguir para esse caso específico e os erros que fazem o diagnóstico dele.|
| **Caso 2**: O erro não pode ser resolvido com credenciais interativas | Para erros de rede e transitórios/temporários ou outras falhas, a execução de uma solicitação AcquireToken interativa não resolve o problema. Avisos de entrada interativa desnecessários também podem frustrar usuários finais. A ADAL tenta automaticamente uma nova tentativa única para a maioria dos erros em falha AcquireTokenSilent.<br><br>O aplicativo cliente também poderá tentar novamente depois, mas quando e como fazer isso depende do comportamento do aplicativo e da experiência do usuário final desejada. Por exemplo, o aplicativo poderá fazer uma nova tentativa AcquireTokenSilent depois de alguns minutos, ou em resposta a uma ação do usuário final. Uma repetição imediata resultará na limitação do aplicativo e não deve ser tentada.<br><br>Uma falha em nova tentativa subsequente com o mesmo erro não significa que o cliente deva fazer uma solicitação interativa usando AcquireToken, pois isso não resolve o erro.<br><br>Consulte o código na seção a seguir para esse caso específico e os erros que fazem o diagnóstico dele. |

### <a name="net"></a>.NET

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(…) 
- [preterido] acquireTokenSilent(…)
- [preterido] acquireTokenByRefreshToken(…) 

O código seria implementado da seguinte maneira:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [preterido] acquireTokenSilent(…)

O código seria implementado da seguinte maneira:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- acquireTokenSilentWithResource(…)

O código deve ser implementado da seguinte maneira:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken é o método ADAL padrão usado para obter tokens. Em casos em que a identidade do usuário seja obrigatória, AcquireToken tenta obter um token silenciosamente primeiro e, em seguida, exibe a interface do usuário, se necessário (a menos que PromptBehavior.Never seja passado). Em casos em que a identidade do aplicativo seja obrigatória, AcquireToken tenta obter um token, mas não mostra a interface do usuário, pois não há usuário final. 

Durante a manipulação de erros AcquireToken, o tratamento de erro depende da plataforma e do cenário que o aplicativo está tentando atingir. 

O sistema operacional também pode gerar um conjunto de erros, que exigem o tratamento de erro de acordo com o aplicativo específico. Para obter mais informações, consulte "Erros de sistema operacional" em [Referência de erro registro em log](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicativos

- Aplicativos de cliente nativo (iOS, Android, .NET Desktop ou Xamarin)
- Aplicativos Web que chamam uma API de recurso (.NET)
- Aplicativos de página única (JavaScript)
- Aplicativos de serviço a serviço (.NET, Java)
  - Todos os cenários, inclusive em nome de
  - Cenários específicos em nome de

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casos de erro e etapas práticas: Aplicativos cliente nativos

Se você estiver criando um aplicativo de cliente nativo, há alguns casos de tratamento de erro a serem considerados que estão relacionados a problemas de rede, falhas transitórias e outros erros específicos da plataforma. Na maioria dos casos, um aplicativo não deve realizar novas tentativas imediatas, e sim aguardar a interação do usuário final que solicita uma entrada. 

Há alguns casos especiais em que uma única repetição pode resolver o problema. Por exemplo, quando um usuário precisa habilitar dados em um dispositivo ou concluir o download do agente do Azure Active Directory depois da falha inicial. 

Em casos de falha, um aplicativo pode apresentar a interface do usuário para permitir que o usuário final realize uma interação que solicita uma nova tentativa. Por exemplo, se o dispositivo tiver falhado com um erro offline, um botão "Tente entrar novamente" solicitando uma nova tentativa AcquireToken, em vez de repetir imediatamente a falha. 

O tratamento de erro em aplicativos nativos podem ser definido por dois casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Erro sem nova tentativa (a maioria dos casos) | 1. Não tente uma nova tentativa imediata. Apresente ao usuário final a interface do usuário final com base no erro específico que invoca uma nova tentativa ("Tentar entrar novamente", "Baixar aplicativo do agente do Azure Active Directory", etc.). |
| **Caso 2**:<br>Erro com nova tentativa | 1. Realize uma única nova tentativa porque o usuário final pode ter informado um estado que resulta em um êxito.<br><br>2. Se a repetição falhar, apresente ao usuário final a interface do usuário com base no erro específico que invoca uma nova tentativa ("Tentar entrar novamente", "Baixar aplicativo do agente do Azure Active Directory" etc.). |

> [!IMPORTANT]
> Se uma conta de usuário for passada para ADAL em uma chamada silenciosa e falhar, a solicitação interativa subsequente permitirá que o usuário final entre usando uma conta diferente. Depois de um AcquireToken bem-sucedido usando uma conta de usuário, o aplicativo deverá verificar se o usuário conectado corresponde ao objeto de usuário local do aplicativo. Uma incompatibilidade não gera uma exceção (exceto no Objetivo C), mas deve ser considerada em casos em que um usuário seja conhecido localmente antes das solicitações de autenticação (como uma chamada silenciosa com falha).
>

#### <a name="net"></a>.NET

As seguintes diretrizes fornecem exemplos para o tratamento de erro com todos os métodos ADAL AcquireToken(...) não silenciosos, *exceto*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

O código seria implementado da seguinte maneira:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> O ADAL .NET tem uma consideração extra porque dá suporte a PromptBehavior.Never, que tem comportamento como AcquireTokenSilent.
>

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- acquireToken(…, PromptBehavior.Never)

O código seria implementado da seguinte maneira:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

As diretrizes a seguir fornecem exemplos de tratamento de erro com todos os métodos ADAL AcquireToken(...) não silenciosos. 

O código seria implementado da seguinte maneira:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

As diretrizes a seguir fornecem exemplos de tratamento de erro com todos os métodos ADAL AcquireToken(...) não silenciosos. 

O código seria implementado da seguinte maneira:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casos de erro e etapas práticas: Aplicativos Web que chamam uma API de recurso (.NET)

Se você estiver criando um aplicativo Web .NET que receba um token usando um código de autorização para um recurso, o único código obrigatório será o manipulador padrão para o caso genérico. 

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

O código seria implementado da seguinte maneira:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casos de erro e etapas práticas: Aplicativos de página única (adal.js)

Se estiver criando um aplicativo de página única usando adal.js com AcquireToken, o código de tratamento de erro será semelhante ao de uma chamada silenciosa típica. Mais especificamente em adal.js, AcquireToken jamais mostra uma interface do usuário. 

Um AcquireToken com falha tem os seguintes casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Pode ser resolvido com uma solicitação interativa | 1. Se login() falhar, não realize nova tentativa imediata. Repita somente após a ação do usuário solicitar uma nova tentativa.|
| **Caso 2**:<br>Não pode ser resolvido com uma solicitação interativa. O erro pode sofrer nova tentativa. | 1. Realize uma única nova tentativa porque o usuário final entrou em um estado que resulta em um êxito.<br><br>2. Se a nova tentativa falhar, apresente ao usuário final uma ação de acordo com o erro específico que pode invocar uma nova tentativa ("Tentar entrar novamente"). |
| **Caso 3**:<br>Não pode ser resolvido com uma solicitação interativa. O erro não pode sofrer nova tentativa. | 1. Não tente uma nova tentativa imediata. Apresente ao usuário final uma ação com base no erro específico que pode invocar uma nova tentativa ("Tentar entrar novamente"). |

O código seria implementado da seguinte maneira:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casos de erro e etapas práticas: aplicativos de serviço a serviço (somente .NET)

Se você estiver criando um aplicativo de serviço a serviço que use AcquireToken, haverá alguns erros de chave que o código deve tratar. O único recurso a uma falha é retornar o erro ao aplicativo de chamada (para casos em nome de) ou aplicar uma estratégia de nova tentativa. 

#### <a name="all-scenarios"></a>Todos os cenários

Para *todos* os cenários de aplicativo de serviço a serviço, inclusive em nome de:

- Não faça uma nova tentativa imediata. A ADAL faz uma única tentativa para determinadas solicitações com falha. 
- Só continue fazendo novas tentativas depois que uma ação do usuário ou do aplicativo solicitar. Por exemplo, um aplicativo daemon que funcione em um intervalo definido deve aguardar o próximo intervalo para tentar novamente.

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

O código seria implementado da seguinte maneira:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Cenários em nome de

Para cenários de aplicativo de serviço a serviço *em nome de*.

As seguintes diretrizes fornecem exemplos de tratamento de erro com métodos ADAL: 

- AcquireTokenAsync(…, UserAssertion, …)

O código seria implementado da seguinte maneira:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Criamos um [exemplo completo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) que demonstra esse cenário.

## <a name="error-and-logging-reference"></a>Referência de erro e registro em log

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Registrando em log o PII (Informações de Identificação Pessoal) e o OII (Informações de Identificação Organizacional)
Por padrão, o log do ADAL não captura nem registra nenhum PII ou OII. A biblioteca permite que os desenvolvedores de aplicativos ativem esse recurso por meio de um setter na classe de Agente. Ao ativar o PII ou OII, o aplicativo assume a responsabilidade pela manipulação de dados altamente confidenciais com segurança e conformidade com os requisitos regulamentares.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar erros ADAL específicos, o código-fonte no [repositório azure-activedirectory-library-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) é a melhor referência de erro.

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de registro em log com erro

As alterações feitas no registro em log do ADAL .NET dependem da plataforma de trabalho. Veja o [wiki do log](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) para obter um código para habilitar o log.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar erros de ADAL específicos, o código-fonte no [repositório azure-activedirectory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros de sistema operacional

Os erros de sistema operacional Android são expostos por meio de AuthenticationException em ADAL, identificados como "SERVER_INVALID_REQUEST", e podem ser mais granulares por meio das descrições de erro. 

Para obter uma lista completa de erros comuns e quais etapas executar quando o aplicativo ou os usuários finais encontrá-los, veja o [Wiki do Android para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de registro em log com erro

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar erros ADAL específicos, o código-fonte no [repositório azure-activedirectory-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros de sistema operacional

Os erros de iOS podem surgir durante a entrada quando os usuários utilizam exibições da Web e a natureza da autenticação. Isso pode ser causado por condições como erros SSL, tempos limite ou erros de rede:

- Para compartilhamento de direitos, os logons não são persistentes e o cache aparece vazio. Você pode resolver adicionando a seguinte linha de código ao conjunto de chaves:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Para o conjunto de erros NsUrlDomain, a ação muda de acordo com a lógica do aplicativo. Consulte a [documentação de referência NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) para obter instâncias específicas que possam ser tratadas.
- Consulte [Problemas comuns de ADAL Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) para obter a lista de erros comuns mantidos pela equipe do ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de registro em log com erro

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Diretrizes para código de registro em log com erro - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Conteúdo relacionado

* [Guia do desenvolvedor do Azure AD][AAD-Dev-Guide]
* [Bibliotecas de autenticação do Azure AD][AAD-Auth-Libraries]
* [Cenários de autenticação do Azure AD][AAD-Auth-Scenarios]
* [Integrar aplicativos com Azure Active Directory][AAD-Integrating-Apps]

Use a seção de comentários a seguir para fazer comentários e nos ajudar a refinar e modelar o conteúdo.

[![Mostra o botão "Entrar com a conta da Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

