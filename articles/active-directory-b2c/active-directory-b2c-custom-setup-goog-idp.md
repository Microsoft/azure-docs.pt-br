---
title: "Azure Active Directory B2C: Adicionar Google+ como um provedor de identidade OAuth2 usando políticas personalizadas"
description: Exemplo de como usar o Google + como provedor de identidade usando o protocolo OAuth2
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: e0aaf710d230f7667fff32b50ddb64104509d740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Adicionar Google+ como um provedor de identidade OAuth2 usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este guia mostra como habilitar a entrada para usuários da conta do Google+ por meio de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:

1.  Criar um aplicativo de conta do Google+.
2.  Adicionar a chave de aplicativo da conta do Google+ ao Azure AD B2C
3.  Adicionar o provedor de declarações a uma política
4.  Registrar o provedor de declarações da conta do Google+ a um percurso do usuário
5.  Carregar a política para um locatário do Azure AD B2C e testá-la

## <a name="create-a-google-account-application"></a>Criar um aplicativo de conta do Google+
Para usar o Google+ como um provedor de identidade no Azure AD (Azure Active Directory B2C), você precisará criar um aplicativo do Google+ e fornecer a ele os parâmetros certos. Você pode registrar um aplicativo do Google+ aqui: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Acesse o [Console de Desenvolvedores do Google](https://console.developers.google.com/) e entre com suas credenciais de conta do Google+.
2.  Clique em **Criar projeto**, insira um **Nome de projeto** e clique em **Criar**.

3.  Clique no **menu Projetos**.

    ![Conta do Google+ - Selecionar o projeto](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Clique no botão **+**.

    ![Conta do Google+ - Criar novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Insira um **Nome de projeto** e clique em **Criar**.

    ![Conta do Google+ - Novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Aguarde até que o projeto esteja pronto e clique no **menu Projetos**.

    ![Conta do Google+ - Aguarde até que o novo projeto esteja pronto para uso](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Clique no nome do seu projeto.

    ![Conta do Google+ - Selecionar o novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Clique em **Gerenciador de API** e em **Credenciais** no painel de navegação à esquerda.
9.  Clique na guia **OAuth consent screen (Tela de consentimento do OAuth)** na parte superior.

    ![Conta do Google+ - Definir tela de consentimento de OAuth](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Selecione ou especifique um **Endereço de email** válido, forneça um **Nome de produto** e clique em **Salvar**.

    ![Google+ - Credenciais do aplicativo](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Clique em **Novas credenciais** e escolha **ID do cliente OAuth**.

    ![Google+ - Criar novas credenciais do aplicativo](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  Em **Tipo de aplicativo**, selecione **Aplicativo Web**.

    ![Google+ - Selecione o tipo de aplicativo](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Forneça um **Nome** para seu aplicativo, insira `https://login.microsoftonline.com` no campo **Origens de JavaScript autorizadas** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URIs de redirecionamento autorizados**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). O valor **{tenant}** diferencia letras maiúsculas de minúsculas. Clique em **Criar**.

    ![Google + - Forneça origens de JavaScript autorizadas e URIs de redirecionamento](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisa de ambos para configurar o Google+ como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

    ![Google+ - Copie os valores de Id do cliente e Segredo do cliente](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Adicionar a chave de aplicativo da conta do Google+ ao Azure AD B2C
A federação com contas do Google+ exige um segredo do cliente para a conta do Google+ para confiar no Azure AD B2C em nome do aplicativo. Você precisa armazenar seu segredo de aplicativo doa Google+ no locatário do Azure AD B2C:  

1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `GoogleSecret`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, insira o segredo de aplicativo da Microsoft de https://apps.dev.microsoft.com
7.  Para o **Uso da chave**, use **Assinatura**.
8.  Clique em **Criar**
9.  Confirme que você criou a chave `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um provedor de declarações à política de extensão

Se quiser que os usuários entrem usando a conta do Google+, você precisará definir a conta do Google+ como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunica. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina a Conta do Google+ como um provedor de declarações adicionando o nó `<ClaimsProvider>` em seu arquivo de política da extensão:

1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
2.  Localize a seção `<ClaimsProviders>`
3.  Adicione o seguinte trecho XML sob o elemento `ClaimsProviders` e substitua o valor `client_id` por sua ID de cliente de aplicativo da conta do Google + antes de salvar o arquivo.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrar o provedor de declarações da conta do Google+ para um percurso do usuário de Inscrição ou Entrada

O provedor de identidade foi configurado.  No entanto, ele não está disponível em qualquer uma das telas de inscrição/entrada. Adicione o provedor de identidade da conta do Google+ ao percurso de usuário `SignUpOrSignIn` do usuário. Para disponibilizá-lo, criamos uma duplicata de um percurso de usuário do modelo existente.  Em seguida, adicionamos o provedor de identidade da conta do Google+:

>[!NOTE]
>
>Se você copiou o elemento `<UserJourneys>` do arquivo de base de sua política para o arquivo de extensão (TrustFrameworkExtensions.xml), pule para esta seção.

1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<UserJourneys>` e copie todo o conteúdo do nó `<UserJourneys>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<UserJournesy>` copiado como um filho do elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Exibir o botão
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem.  O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento `<ClaimsProviderSelection>` para a conta do Google+, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1.  Localize o nó `<UserJourney>` que inclui `Id="SignUpOrSignIn"` no percurso do usuário que você acabou de copiar.
2.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
3.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação
Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com a conta do Google+ para receber um token.

1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior
> * Certifique-se de que a ID `TechnicalProfileReferenceId` esteja definida como o perfil técnico criado anteriormente (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política ao seu locatário
1.  No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Abra a folha **Todas as Políticas**.
4.  Selecione **Carregar Política**.
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada usando a opção Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >
    >    A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. 
    >    Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.


2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do Google+.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registrar o provedor de declarações da conta do Google+ ao percurso de usuário de Edição de Perfil
Convém adicionar também o provedor de identidade da conta do Google+ ao percurso de usuário `ProfileEdit` do usuário. Para disponibilizá-lo, repetimos as duas últimas etapas:

### <a name="display-the-button"></a>Exibir o botão
1.  Abra o arquivo de extensão de sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localize o nó `<UserJourney>` que inclui `Id="ProfileEdit"` no percurso do usuário que você acabou de copiar.
3.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
4.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação
1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política personalizada de Edição de Perfil usando Executar Agora

1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do Google+.

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
