---
title: Guia de migração do ADAL para MSAL para Android | Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar seu aplicativo Android da ADAL (biblioteca de autenticação do Azure Active Directory) para a biblioteca de autenticação da Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: ba639bc023affc7c2e6b2b675cdedc1229636893
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581033"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guia de migração do ADAL para MSAL para Android

Este artigo realça as alterações que você precisa fazer para migrar um aplicativo que usa a ADAL (biblioteca de autenticação Azure Active Directory) para usar a MSAL (biblioteca de autenticação da Microsoft).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL funciona com o ponto de extremidade do Azure Active Directory v 1.0. A MSAL (biblioteca de autenticação da Microsoft) funciona com a plataforma de identidade da Microsoft, conhecida anteriormente como ponto de extremidade do Azure Active Directory v 2.0. A plataforma Microsoft Identity é diferente do Azure Active Directory v 1.0, pois:

Oferece suporte a:
  - Identidade organizacional (Azure Active Directory)
  - Identidades não organizacionais, como Outlook.com, Xbox Live e assim por diante
  - (Somente Azure AD B2C) Logon federado com Google, Facebook, Twitter e Amazon

- Os padrões são compatíveis com:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

A API pública do MSAL introduz alterações importantes, incluindo:

- Um novo modelo para acessar tokens:
  - A ADAL fornece acesso a tokens por meio do `AuthenticationContext` , que representa o servidor. O MSAL fornece acesso a tokens por meio do `PublicClientApplication` , que representa o cliente. Os desenvolvedores de cliente não precisam criar uma nova `PublicClientApplication` instância para cada autoridade com a qual precisam interagir. Apenas uma `PublicClientApplication` configuração é necessária.
  - Suporte para solicitar tokens de acesso usando escopos além de identificadores de recurso.
  - Suporte para consentimento incremental. Os desenvolvedores podem solicitar escopos à medida que o usuário acessa cada vez mais funcionalidades no aplicativo, incluindo aquelas não incluídas durante o registro do aplicativo.
  - As autoridades não são mais validadas em tempo de execução. Em vez disso, o desenvolvedor declara uma lista de "autoridades conhecidas" durante o desenvolvimento.
- Alterações de API de token:
  - No ADAL, `AcquireToken()` o primeiro faz uma solicitação silenciosa. Falhando, isso faz uma solicitação interativa. Esse comportamento resultou em alguns desenvolvedores que confiam apenas em `AcquireToken` , o que resultou na inesperação de credenciais do usuário em momentos. O MSAL exige que os desenvolvedores sejam intencionais sobre quando o usuário recebe um prompt de interface de usuário.
    - `AcquireTokenSilent` sempre resulta em uma solicitação silenciosa que seja bem-sucedida ou falhe.
    - `AcquireToken` sempre resulta em uma solicitação que solicita ao usuário por meio da IU.
- O MSAL dá suporte à entrada de um navegador padrão ou de uma exibição da Web inserida:
  - Por padrão, o navegador padrão no dispositivo é usado. Isso permite que o MSAL use o estado de autenticação (cookies) que pode já estar presente para uma ou mais contas conectadas. Se nenhum estado de autenticação estiver presente, a autenticação durante a autorização por meio de MSAL resultará na criação de um estado de autenticação (cookies) para o benefício de outros aplicativos Web que serão usados no mesmo navegador.
- Novo modelo de exceção:
  - As exceções definem de forma mais clara o tipo de erro que ocorreu e o que o desenvolvedor precisa fazer para resolvê-lo.
- O MSAL dá suporte a objetos de parâmetro para `AcquireToken` `AcquireTokenSilent` chamadas e.
- O MSAL dá suporte à configuração declarativa para:
  - ID do cliente, URI de redirecionamento.
  - Navegador padrão vs inserido
  - Pelas
  - Configurações de HTTP, como tempo limite de leitura e conexão

## <a name="your-app-registration-and-migration-to-msal"></a>O registro e a migração do aplicativo para o MSAL

Você não precisa alterar seu registro de aplicativo existente para usar o MSAL. Se você quiser aproveitar o consentimento incremental/progressivo, talvez seja necessário examinar o registro para identificar os escopos específicos que você deseja solicitar de forma incremental. A seguir, mais informações sobre escopos e o consentimento incremental.

Em seu registro de aplicativo no portal, você verá uma guia **permissões de API** . Isso fornece uma lista das APIs e permissões (escopos) às quais seu aplicativo está atualmente configurado para solicitar acesso. Ele também mostra uma lista dos nomes de escopo associados a cada permissão de API.

### <a name="user-consent"></a>Consentimento do usuário

Com a ADAL e o ponto de extremidade do Azure AD v1, o consentimento do usuário para os recursos de sua propriedade foi concedido no primeiro uso. Com o MSAL e a plataforma de identidade da Microsoft, o consentimento pode ser solicitado de forma incremental. O consentimento incremental é útil para permissões que um usuário pode considerar alto privilégio ou, caso contrário, pergunta se não foi fornecida uma explicação clara de por que a permissão é necessária. No ADAL, essas permissões podem ter resultado no usuário abandonar a entrada em seu aplicativo.

> [!TIP]
> Use o consentimento incremental para fornecer contexto adicional aos usuários sobre por que seu aplicativo precisa de uma permissão.

### <a name="admin-consent"></a>Consentimento do administrador

Os administradores da organização podem consentir as permissões que seu aplicativo requer em nome de todos os membros de sua organização. Algumas organizações só permitem que os administradores consentim com aplicativos. O consentimento do administrador exige que você inclua todas as permissões e escopos de API usados pelo seu aplicativo no registro do aplicativo.

> [!TIP]
> Embora você possa solicitar um escopo usando o MSAL para algo não incluído no registro do aplicativo, recomendamos que você atualize o registro do aplicativo para incluir todos os recursos e escopos aos quais um usuário pode conceder permissão.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrando de IDs de recurso para escopos

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticar e solicitar autorização para todas as permissões no primeiro uso

Se você estiver usando a ADAL e não precisar usar o consentimento incremental, a maneira mais simples de começar a usar o MSAL é fazer uma `acquireToken` solicitação usando o novo `AcquireTokenParameter` objeto e definindo o valor da ID do recurso.

> [!CAUTION]
> Não é possível definir os escopos e uma ID de recurso. A tentativa de definir ambos resultará em um `IllegalArgumentException` .

Isso resultará no mesmo comportamento v1 que você está acostumado. Todas as permissões solicitadas no registro do aplicativo são solicitadas pelo usuário durante sua primeira interação.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autenticar e solicitar permissões somente quando necessário

Para aproveitar o consentimento incremental, faça uma lista de permissões (escopos) que seu aplicativo usa do registro do aplicativo e organize-os em duas listas com base em:

- Quais escopos você deseja solicitar durante a primeira interação do usuário com seu aplicativo durante a entrada.
- As permissões associadas a um recurso importante de seu aplicativo que você também precisará explicar ao usuário.

Depois de organizar os escopos, organize cada lista de acordo com o recurso (API) para o qual você deseja solicitar um token. Bem como quaisquer outros escopos que você deseja que o usuário autorize ao mesmo tempo.

O objeto Parameters usado para fazer sua solicitação para o MSAL dá suporte a:

- `Scope`: A lista de escopos para os quais você deseja solicitar autorização e receber um token de acesso.
- `ExtraScopesToConsent`: Uma lista adicional de escopos para os quais você deseja solicitar autorização enquanto estiver solicitando um token de acesso para outro recurso. Essa lista de escopos permite minimizar o número de vezes que você precisa solicitar a autorização do usuário. Isso significa menos autorização de usuário ou prompts de consentimento.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrar do AuthenticationContext para o PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Construindo PublicClientApplication

Ao usar o MSAL, você instancia um `PublicClientApplication` . Esse objeto modela a identidade do aplicativo e é usado para fazer solicitações a uma ou mais autoridades. Com esse objeto, você configurará a identidade do cliente, o URI de redirecionamento, a autoridade padrão, se deseja usar o navegador do dispositivo versus a exibição da Web incorporada, o nível de log e muito mais.

Você pode configurar esse objeto declarativamente com JSON, que você fornece como um arquivo ou armazenamento como um recurso em seu APK.

Embora esse objeto não seja um singleton, internamente ele usa compartilhado `Executors` para solicitações interativas e silenciosas.

### <a name="business-to-business"></a>Negócios para os negócios

Na ADAL, todas as organizações para as quais você solicita tokens de acesso exigem uma instância separada do `AuthenticationContext` . No MSAL, isso não é mais um requisito. Você pode especificar a autoridade da qual deseja solicitar um token como parte de sua solicitação silenciosa ou interativa.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrar da validação de autoridade para autoridades conhecidas

MSAL não tem um sinalizador para habilitar ou desabilitar a validação de autoridade. A validação de autoridade é um recurso do ADAL e, nas primeiras versões do MSAL, isso impede que seu código solicite tokens de uma autoridade potencialmente mal-intencionada. MSAL agora recupera uma lista de autoridades conhecidas da Microsoft e mescla essa lista com as autoridades que você especificou em sua configuração.

> [!TIP]
> Se você for um usuário do Azure Business to Consumer (B2C), isso significa que você não precisa mais desabilitar a validação de autoridade. Em vez disso, inclua cada uma das políticas de Azure AD B2C com suporte como autoridades em sua configuração do MSAL.

Se você tentar usar uma autoridade que não seja conhecida pela Microsoft e não estiver incluída na sua configuração, obterá um `UnknownAuthorityException` .

### <a name="logging"></a>Registro em log
Agora você pode configurar o log de forma declarativa como parte de sua configuração, como esta:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migrar de UserInfo para conta

No ADAL, o `AuthenticationResult` fornece um `UserInfo` objeto usado para recuperar informações sobre a conta autenticada. O termo "usuário", que significava um agente humano ou de software, foi aplicado de forma que dificulte a comunicação de que alguns aplicativos dão suporte a um único usuário (seja um agente humano ou de software) que tenha várias contas.

Considere uma conta bancária. Você pode ter mais de uma conta em mais de uma instituição financeira. Quando você abre uma conta, você (o usuário) recebe credenciais, como uma placa ATM & PIN, que são usadas para acessar seu saldo, pagamentos de cobrança e assim por diante, para cada conta. Essas credenciais só podem ser usadas na instituição financeira que as emitiu.

Por analogia, como contas em uma instituição financeira, as contas na plataforma Microsoft Identity são acessadas usando credenciais. Essas credenciais são registradas com o, ou emitidas pela Microsoft. Ou pela Microsoft em nome de uma organização.

Onde a plataforma de identidade da Microsoft difere de uma instituição financeira, nessa analogia, é que a plataforma de identidade da Microsoft fornece uma estrutura que permite que um usuário use uma conta e suas credenciais associadas para acessar recursos que pertencem a vários indivíduos e organizações. Isso é como ser capaz de usar um cartão emitido por um banco, no entanto, em outra instituição financeira. Isso funciona porque todas as organizações em questão estão usando a plataforma de identidade da Microsoft, que permite que uma conta seja usada em várias organizações. Aqui está um exemplo:

O Sam funciona para Contoso.com, mas gerencia as máquinas virtuais do Azure que pertencem ao Fabrikam.com. Para que o Sam gerencie as máquinas virtuais da Fabrikam, ele precisa estar autorizado a acessá-las. Esse acesso pode ser concedido adicionando a conta de Sam ao Fabrikam.com e concedendo a ela uma função que permita que ele trabalhe com as máquinas virtuais. Isso seria feito com o portal do Azure.

Adicionar a conta Contoso.com do Sam como um membro de Fabrikam.com resultaria na criação de um novo registro na Azure Active Directory da Fabrikam. com para Sam. O registro de Sam no Azure Active Directory é conhecido como um objeto de usuário. Nesse caso, esse objeto de usuário apontaria de volta para o objeto de usuário do Sam em Contoso.com. O objeto de usuário da Fabrikam Sam é a representação local do Sam e seria usado para armazenar informações sobre a conta associada ao Sam no contexto de Fabrikam.com. No Contoso.com, o título de Sam é consultor sênior de DevOps. Na Fabrikam, o título de Sam é Contractor-Virtual computadores. No Contoso.com, Sam não é responsável, nem autorizado, a gerenciar máquinas virtuais. No Fabrikam.com, essa é sua única função de trabalho. No entanto, o Sam ainda tem apenas um conjunto de credenciais para controlar, que são as credenciais emitidas pelo Contoso.com.

Quando uma `acquireToken` chamada bem-sucedida for feita, você verá uma referência a um `IAccount` objeto que pode ser usado em solicitações posteriores `acquireTokenSilent` .

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Se você tiver um aplicativo que acessa declarações sobre uma conta de cada locatário no qual a conta é representada, você pode converter `IAccount` objetos no `IMultiTenantAccount` . Essa interface fornece um mapa de `ITenantProfiles` , com chave por ID de locatário, que permite que você acesse as declarações que pertencem à conta em cada um dos locatários dos quais você solicitou um token, em relação à conta atual.

As declarações na raiz do `IAccount` e `IMultiTenantAccount` sempre contêm as declarações do locatário inicial. Se você ainda não fez uma solicitação para um token no locatário inicial, essa coleção estará vazia.

## <a name="other-changes"></a>Outras alterações

### <a name="use-the-new-authenticationcallback"></a>Usar o novo AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migrar para as novas exceções

No ADAL, há um tipo de exceção, `AuthenticationException` , que inclui um método para recuperar o valor de `ADALError` enumeração.
No MSAL, há uma hierarquia de exceções e cada uma tem seu próprio conjunto de códigos de erro específicos associados.

| Exceção                                        | Descrição                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Gerado se um ou mais argumentos de entrada forem inválidos.                 |
| `MsalClientException`                            | Gerado se o erro for do lado do cliente.                                 |
| `MsalDeclinedScopeException`                     | Gerado se um ou mais escopos solicitados foram recusados pelo servidor. |
| `MsalException`                                  | Exceção verificada padrão lançada por MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Gerado se o recurso tiver a política de proteção MAMCA habilitada.         |
| `MsalServiceException`                           | Gerado se o erro for do lado do servidor.                                 |
| `MsalUiRequiredException`                        | Gerado se o token não puder ser atualizado silenciosamente.                    |
| `MsalUserCancelException`                        | Gerado se o usuário cancelou o fluxo de autenticação.                |

### <a name="adalerror-to-msalexception-translation"></a>Tradução de ADALError para MsalException

| Se você estiver capturando esses erros no ADAL...  | ... Pegue estas exceções de MSAL:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Nenhum ADALError equivalente* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Nenhum ADALError equivalente* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Nenhum ADALError equivalente* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Nenhum ADALError equivalente* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>Log de ADAL no log de MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
