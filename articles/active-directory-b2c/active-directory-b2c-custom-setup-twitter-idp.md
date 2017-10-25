---
title: "Azure Active Directory B2C: adicionar o Twitter como um provedor de identidade OAuth1 usando políticas Personalizadas"
description: Exemplo de como usar o Twitter como provedor de identidade usando o protocolo OAuth1
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
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adicionar o Twitter como um provedor de identidade OAuth1 usando políticas Personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários da conta do Twitter por meio de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:

1.  Criar um aplicativo da conta do Twitter.
2.  Adicionar a chave de aplicativo da conta do Twitter ao Azure AD B2C
3.  Adicionar o provedor de declarações a uma política
4.  Registrar o provedor de declarações da conta do Twitter a um percurso do usuário
5.  Carregar a política para um locatário do Azure AD B2C e testá-la

## <a name="step-1-create-a-twitter-account-application"></a>Etapa 1: Criar um aplicativo de conta do Twitter
Para usar o Twitter como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisa criar um aplicativo Twitter e fornecer a ele os parâmetros certos. Você pode registrar um aplicativo do Twitter aqui: [https://twitter.com/signup](https://twitter.com/signup)

1.  Navegue até o site de [Desenvolvedores do Twitter,](https://apps.twitter.com/) entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.

    ![Conta do Twitter – criar novo aplicativo](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  Digite o **Nome** e uma **Descrição** para o novo aplicativo. Cole em `https://login.microsoftonline.com` para o valor do **Site**. Em seguida, para a **URL de Retorno de Chamada**, cole o `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Substitua {tenant} pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). e verifique se você está usando o esquema HTTPS. Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**.

    ![Conta do Twitter – adicionar um novo aplicativo](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.
4.  Selecione a guia **Chaves e Tokens de Acesso** . Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.

    ![Conta do Twitter – definir propriedades do aplicativo](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>O Segredo do Consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>Etapa 2: Adicionar a chave de aplicativo da conta do Twitter ao Azure AD B2C
A federação com contas do Twitter exige um segredo do cliente para a conta do Twitter confiar no Azure AD B2C em nome do aplicativo. Você precisa armazenar o segredo do consumidor do aplicativo do Twitter no seu locatário do Azure AD B2C:  

1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `TwitterSecret`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, insira o segredo de aplicativo da Microsoft de https://apps.dev.microsoft.com
7.  Para o **Uso da chave**, use **Criptografia**.
8.  Clique em **Criar**
9.  Confirme que você criou a chave `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Etapa 3: adicionar um provedor de declarações à sua política de extensão

Se quiser que os usuários entrem usando a conta do Twitter, você precisará definir o Twitter como um provedor de declarações. Em outras palavras, você precisa especificar pontos de extremidade com os quais o Azure AD B2C se comunique. Os pontos de extremidade fornecem um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina o Twitter como um provedor de declarações adicionando o nó `<ClaimsProvider>` em seu arquivo de política da extensão:

1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. 
2.  Localize a seção `<ClaimsProviders>`
3.  Adicione o seguinte trecho XML no nó `<ClaimsProviders>`:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            </OutputClaims>
            <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

4.  Substitua o valor `client_id` pela chave do consumidor do aplicativo da conta do Twitter

5.  Salve o arquivo.

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Etapa 4: Registrar o provedor de declarações da conta do Twitter para um percurso do usuário de Inscrição ou Entrada
Neste ponto, o provedor de identidade foi configurado. No entanto, ele não está disponível em qualquer uma das telas de inscrição/entrada. Agora você precisa adicionar o provedor de identidade da conta do Twitter ao percurso de usuário `SignUpOrSignIn` do usuário.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Etapa 4.1 Fazer uma cópia do jornada de usuário
Para disponibilizá-la, você cria uma duplicata de um percurso do usuário do modelo existente. Em seguida, adicione o provedor de identidade do Twitter:

> [!NOTE]
>
>Se você tiver copiado o elemento `<UserJourneys>` do arquivo de base da sua política para o arquivo de extensão (TrustFrameworkExtensions.xml), poderá ir para a seção seguinte.

1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<UserJourneys>` e copie todo o conteúdo do nó `<UserJourneys>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<UserJournesy>` copiado como um filho do elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Etapa 4.2 Exibir o “botão”
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem.  O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento `<ClaimsProviderSelection>` à conta do Twitter, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1.  Localize o nó `<UserJourney>` que inclui `Id="SignUpOrSignIn"` no percurso do usuário que você acabou de copiar.
2.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
3.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Etapa 4.3 Vincular o botão a uma ação
Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C se comunicar com a conta do Twitter para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da conta do Twitter:

1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior
    > * Verifique se a ID `TechnicalProfileReferenceId` está definida para o perfil técnico criado anteriormente (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: Carregar a política para o seu locatário
1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e clique em **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Clique em **Todas as Políticas**.
4.  Selecione **Carregar Política**
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: Testar a política personalizada usando Executar Agora

1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    > [!NOTE]
    >
    >A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>Etapa 7: [Opcional] Registrar o provedor de declarações da conta do Twitter para o percurso de usuário de Edição de Perfil
Convém adicionar também o provedor de identidade da conta do Twitter ao percurso de usuário `ProfileEdit` do usuário. Para disponibilizá-lo, você deve repetir a etapa nº 4. Neste momento, selecione o nó `<UserJourney>` que inclui `Id="ProfileEdit"`. Salve, carregue e teste sua política.


## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)