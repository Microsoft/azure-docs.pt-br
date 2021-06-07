---
title: Erros e exceções (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, acesso condicional e desafios de declarações em aplicativos Android MSAL.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761389"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Tratar exceções e erros no MSAL para Android

As exceções na biblioteca de autenticação da Microsoft (MSAL) destinam-se a ajudar os desenvolvedores de aplicativos a solucionarem problemas de seu aplicativo. Mensagens de exceção não são localizadas.

Ao processar exceções e erros, é possível usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [Códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de entrada, você pode encontrar erros sobre consentimentos, Acesso Condicional (MFA, Gerenciamento de Dispositivo, Restrições baseadas em local), emissão e resgate de token e propriedades de usuário.


|Classe de erro | Causa/erro de cadeia de caracteres| Como tratar |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: O token de atualização usado para resgatar o token de acesso é inválido, expirou ou revogado. Essa exceção pode ser devido a uma alteração de senha. </li><li>`NO_TOKENS_FOUND`: O token de acesso não existe e nenhum token de atualização pode ser encontrado para resgatar o token de acesso.</li> <li>Etapa necessária<ul><li>MFA</li><li>Declarações ausentes</li></ul></li><li>Bloqueado pelo acesso condicional (por exemplo, instalação [do agente de autenticação](./msal-android-single-sign-on.md) necessário)</li><li>`NO_ACCOUNT_FOUND`: Nenhuma conta disponível no cache para autenticação silenciosa.</li></ul> |Chame `acquireToken()` para solicitar que o usuário insira seu nome de usuário e senha e possivelmente consentir e executar a autenticação multifator.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: O usuário ou o servidor não aceitou todos os escopos. O servidor pode recusar um escopo se o escopo solicitado não tiver suporte, não reconhecido ou não tiver suporte para uma conta específica. </li></ul>| O desenvolvedor deve decidir se deseja continuar a autenticação com os escopos concedidos ou finalizar o processo de autenticação. Opção para reenviar a solicitação de token de aquisição somente para os escopos concedidos e fornecer dicas para quais permissões foram concedidas passando `silentParametersForGrantedScopes` e chamando `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: Esta solicitação não tem um parâmetro obrigatório, inclui um parâmetro inválido, inclui um parâmetro mais de uma vez ou está malformada. </li><li>`SERVICE_NOT_AVAILABLE`: Representa os códigos de erro 500/503/506 devido ao serviço estar inoperante. </li><li>`UNAUTHORIZED_REQUEST`: O cliente não está autorizado a solicitar um código de autorização.</li><li>`ACCESS_DENIED`: O proprietário do recurso ou o servidor de autorização negou a solicitação.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` falha na validação</li><li>`UNKNOWN_ERROR`: A solicitação para o servidor falhou, mas nenhum erro e `error_description` retorna de volta do serviço.</li><ul>| Essa classe de exceção representa erros ao se comunicar com o serviço, pode ser dos pontos de extremidade de autorização ou token. MSAL lê o erro e error_description da resposta do servidor. Em geral, esses erros são resolvidos corrigindo as configurações de aplicativo no código ou no portal de registro de aplicativo. Raramente uma interrupção do serviço pode disparar esse aviso, que só pode ser mitigado aguardando a recuperação do serviço.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Há várias entradas de cache encontradas e o SDK não pode identificar o acesso correto ou o token de atualização do cache. Essa exceção geralmente indica um bug no SDK para armazenar tokens ou que a autoridade não é fornecida na solicitação silenciosa e vários tokens correspondentes são encontrados. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Nenhuma rede ativa está disponível no dispositivo. </li><li>`JSON_PARSE_FAILURE`: O SDK falhou ao analisar o formato JSON.</li><li>`IO_ERROR`: `IOException` aconteceu, pode ser um erro de dispositivo ou de rede. </li><li>`MALFORMED_URL`: A URL está malformada. Provavelmente causado ao construir a solicitação de autenticação, a autoridade ou o URI de redirecionamento. </li><li>`UNSUPPORTED_ENCODING`: O dispositivo não dá suporte à codificação. </li><li>`NO_SUCH_ALGORITHM`: Não há suporte para o algoritmo usado para gerar o desafio [PKCE](https://tools.ietf.org/html/rfc7636) . </li><li>`INVALID_JWT`: `JWT` retornado pelo servidor não é válido ou está vazio ou malformado. </li><li>`STATE_MISMATCH`: O estado da resposta de autorização não correspondeu ao estado na solicitação de autorização. Para solicitações de autorização, o SDK verificará o estado retornado do redirecionamento e aquele enviado na solicitação. </li><li>`UNSUPPORTED_URL`: URL sem suporte, não é possível executar a validação de autoridade do ADFS. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: A autoridade não tem suporte para validação de autoridade. O SDK oferece suporte a autoridades B2C, mas não dá suporte à validação de autoridade B2C. Somente o host conhecido terá suporte. </li><li>`CHROME_NOT_INSTALLED`: O Chrome não está instalado no dispositivo. O SDK usa a guia personalizada do Chrome para solicitações de autorização, se disponível, e fará fallback para o navegador Chrome. </li><li>`USER_MISMATCH`: O usuário fornecido na solicitação de token de aquisição não corresponde ao usuário retornado do servidor.</li></ul>|Essa classe de exceção representa erros gerais que são locais para a biblioteca. Essas exceções podem ser tratadas com a correção da solicitação.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: O usuário iniciou o fluxo interativo e, antes de receber tokens, cancelou a solicitação. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: A autoridade deve ser especificada para `acquireTokenSilent` .</li></ul>|Esses erros podem ser mitigados pelo desenvolvedor corrigindo argumentos e garantindo que a atividade de autenticação interativa, retorno de chamada de conclusão, escopos e uma conta com uma ID válida tenha sido fornecida.|


## <a name="catching-errors"></a>Captura de erros

O trecho de código a seguir mostra um exemplo de erros de captura no caso de chamadas silenciosas `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [registro em log no MSAL para Android](msal-logging-android.md).