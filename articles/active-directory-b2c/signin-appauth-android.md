---
title: Adquirir um token em um aplicativo Android
titleSuffix: Azure AD B2C
description: Como criar um aplicativo Android que usa AppAuth com o Azure Active Directory B2C para autenticar e gerenciar identidades de usuários.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acbd2918bd311cec1c27018763ad10771d779d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953314"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Entrar usando um aplicativo do Android no Azure Active Directory B2C

A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. Esses padrões permitem utilizar qualquer biblioteca que você queira integrar com o Azure Active Directory B2C. Para ajudar você a usar outras bibliotecas, use um passo a passo como este para demonstrar como configurar bibliotecas de terceiros para que elas se conectem à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementa [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) pode se conectar à plataforma de identidade da Microsoft.

> [!WARNING]
> A Microsoft não fornece correções nem análises para bibliotecas de terceiros. Esse exemplo usa uma biblioteca de terceiros, AppAuth, que foi testada com relação à compatibilidade em cenários básicos com o Azure AD B2C. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca. Para saber mais, confira [este artigo](../active-directory/develop/reference-v2-libraries.md).
>
>

Se você ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo poderá não fazer muito sentido para você. Recomendamos que você examine uma breve [visão geral do protocolo que documentamos aqui](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](tutorial-create-tenant.md) antes de prosseguir.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, registre um aplicativo no locatário do Azure AD B2C. Isso fornece ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Registre também seu URI de Redirecionamento Personalizado para uso em uma etapa posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Criar seus fluxos de usuário

No Azure Active Directory B2C, cada experiência do usuário é definida por uma [fluxo de usuário](user-flow-overview.md), que é um conjunto de políticas que controlam o comportamento do Azure Active Directory. Este aplicativo requer um fluxo de usuário de entrada e de inscrição. Ao criar as duas políticas, não se esqueça de:

* Escolha o **Nome de exibição** e os atributos de inscrição no seu fluxo de usuário.
* Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em cada fluxo de usuário. Você pode escolher outras declarações também.
* Copie o **Nome** de cada usuário após criá-lo. Ele deve ter o prefixo `b2c_1_`.  Você precisará do nome de fluxo do usuário mais tarde.

Depois de criar os fluxos de usuário, você estará pronto para compilar o aplicativo.

## <a name="download-the-sample-code"></a>Baixe o código de exemplo

Nós fornecemos um exemplo funcional que usa o AppAuth com o Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Você pode baixar o código e executá-lo. Você pode começar rapidamente com seu próprio aplicativo usando uma configuração personalizada do Azure AD B2C, para isso, basta seguir as instruções de [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

O exemplo é uma modificação de exemplo fornecido pelo [AppAuth](https://openid.github.io/AppAuth-Android/). Acesse a página para saber mais sobre o AppAuth e seus recursos.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificando seu aplicativo para usar o Azure AD B2C com AppAuth

> [!NOTE]
> O AppAuth oferece suporte à API Android 16 (Jellybean) e versões superiores. Recomendamos usar a API 23 ou superior.
>

### <a name="configuration"></a>Configuração

Você pode configurar a comunicação com o Azure AD B2C especificando o URI de descoberta, ou especificando o ponto de extremidade de autorização e os URIs de ponto de extremidade de token. Em ambos os casos, você precisará das informações a seguir:

* ID do locatário (por exemplo, contoso.onmicrosoft.com)
* Nome do fluxo de usuário (por exemplo, B2C\_1\_SignUpIn)

Se optar por descobrir automaticamente a autorização e os URIs de ponto de extremidade de token, você terá que buscar informações do URI de descoberta. O URI de descoberta pode ser gerado ao substituir o `<tenant-id>` e o `<policy-name>` na seguinte URL:

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
```

Para adquirir a autorização e os URIs de ponto de extremidade de token e criar um objeto AuthorizationServiceConfiguration, execute:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Em vez de usar a descoberta para obter a autorização e os URIs de ponto de extremidade de token, você também pode especificá-los explicitamente, substituindo o `<tenant-id>` e o `<policy-name>` nas URLs a seguir:

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
```

Execute o seguinte código para criar o objeto AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizando

Depois de configurar ou recuperar uma configuração de serviço de autorização, uma solicitação de autorização pode ser criada. Para criar a solicitação, você precisará das informações a seguir:

* ID do Cliente (ID DO APLICATIVO) que você registrou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* URI de Redirecionamento Personalizado que você registrou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Os dois itens devem ter sido salvos quando você estava [registrando seu aplicativo](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte o [guia AppAuth](https://openid.github.io/AppAuth-Android/) para concluir o processo. Se quiser trabalhar logo com o seu aplicativo, veja o [nosso exemplo](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Siga as etapas em [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para inserir sua própria configuração do Azure AD B2C.