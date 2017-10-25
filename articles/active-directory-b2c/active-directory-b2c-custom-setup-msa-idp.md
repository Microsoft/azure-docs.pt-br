---
title: "Azure Active Directory B2C: Adicionar MSA (Conta da Microsoft) como um provedor de identidade usando Políticas personalizadas"
description: Exemplo usando Microsoft como provedor de identidade usando o protocolo OIDC (OpenID Connect)
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
ms.openlocfilehash: 8c981046ff41d3927ff60d6dc4f40366ae25ba74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Adicionar MSA (Conta da Microsoft) como um provedor de identidade usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários da MSA (conta da Microsoft) por meio de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:

1.  Criar um aplicativo de conta da Microsoft.
2.  Adicionar a chave de aplicativo da conta da Microsoft ao Azure AD B2C
3.  Adicionar o provedor de declarações a uma política
4.  Registrar o provedor de declarações da Conta da Microsoft a um percurso do usuário
5.  Carregar a política para um locatário do Azure AD B2C e testá-la

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft
Para usar a conta da Microsoft como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo de conta da Microsoft e fornecer a ele os parâmetros certos. Você precisa de uma conta da Microsoft. Se você não tiver uma, visite [https://www.live.com/](https://www.live.com/).

1.  Vá para o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e entre com suas credenciais da conta da Microsoft.
2.  Clique em **Adicionar um aplicativo**.

    ![Conta da Microsoft – Adicionar um aplicativo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Forneça um **Nome** para seu aplicativo, **Email de contato**, desmarque **Deixe-nos ajudar você a começar** e clique em **Criar**.

    ![Conta da Microsoft - Registrar seu aplicativo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Copie o valor de **ID do Aplicativo**. Você precisa dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário.

    ![Conta da Microsoft - Copiar o valor da ID do Aplicativo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Clique em **Adicionar plataforma**

    ![Conta da Microsoft - Adicionar plataforma](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Na lista de plataformas, escolha **Web**.

    ![Conta da Microsoft - Na lista de plataformas, escolha Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Redirecionamento** . Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com).

    ![Conta da Microsoft – Definir URLs de redirecionamento](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Clique em **Gerar Nova Senha** na seção **Segredos do Aplicativo**. Copie a nova senha exibida na tela. Você precisa dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário. A senha é uma credencial de segurança importante.

    ![Conta da Microsoft - Gerar nova senha](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Conta da Microsoft - Copiar a nova senha](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Marque a caixa que diz **suporte do SDK do Live** na seção **Opções Avançadas**. Clique em **Salvar**.

    ![Conta da Microsoft - Suporte ao SDK do Live](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Adicionar a chave de aplicativo da conta da Microsoft ao Azure AD B2C
A federação com contas da Microsoft exige um segredo do cliente para a conta da Microsoft para confiar no Azure AD B2C em nome do aplicativo. Você precisa armazenar o segredo de aplicativo de sua conta da Microsoft no locatário do Azure AD B2C:   

1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `MSASecret`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, insira o segredo de aplicativo da Microsoft de https://apps.dev.microsoft.com
7.  Para o **Uso da chave**, use **Assinatura**.
8.  Clique em **Criar**
9.  Confirme que você criou a chave `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um provedor de declarações à política de extensão
Se quiser que os usuários entrem usando a conta da Microsoft, você precisará definir a conta da Microsoft como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunica. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina a Conta da Microsoft como um provedor de declarações adicionando o nó `<ClaimsProvider>` em seu arquivo de política da extensão:

1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
2.  Localize a seção `<ClaimsProviders>`
3.  Adicione o seguinte trecho XML no elemento `ClaimsProviders`:

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
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

4.  Substitua o valor `client_id` pela Id de cliente do aplicativo da Conta da Microsoft

5.  Salve o arquivo.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrar o provedor de declarações da Conta da Microsoft a um percurso do usuário de Inscrição ou Entrada

Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Agora você precisa adicionar o provedor de identidade da Conta da Microsoft ao percurso de usuário `SignUpOrSignIn` do usuário. Para disponibilizá-lo, criamos uma duplicata de um percurso de usuário do modelo existente.  Em seguida, adicionamos o provedor de identidade da Conta da Microsoft:

> [!NOTE]
>
>Se você copiou anteriormente o elemento `<UserJourneys>` do arquivo de base de sua política para o arquivo de extensão `TrustFrameworkExtensions.xml`, pule para esta seção.

1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<UserJourneys>` e copie todo o conteúdo do nó `<UserJourneys>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<UserJournesy>` copiado como um filho do elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Exibir o botão
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem.  O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento `<ClaimsProviderSelection>` para a conta da Microsoft, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1.  Localize o nó `<UserJourney>` que inclui `Id="SignUpOrSignIn"` no percurso do usuário que você acabou de copiar.
2.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
3.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação
Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com a Conta da Microsoft para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da Conta da Microsoft:

1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior
>   * Certifique-se de que a ID `TechnicalProfileReferenceId` esteja definida como o perfil técnico criado anteriormente (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política ao seu locatário
1.  No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Abra a folha **Todas as Políticas**.
4.  Selecione **Carregar Política**.
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada usando a opção Executar Agora

1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
> [!NOTE]
>
>A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.
2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta da Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registrar o provedor de declarações da Conta da Microsoft ao percurso de usuário de Edição de Perfil
Convém adicionar também o provedor de identidade da Conta da Microsoft ao percurso de usuário `ProfileEdit` do usuário. Para disponibilizá-lo, repetimos as duas últimas etapas:

### <a name="display-the-button"></a>Exibir o botão
1.  Abra o arquivo de extensão de sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localize o nó `<UserJourney>` que inclui `Id="ProfileEdit"` no percurso do usuário que você acabou de copiar.
3.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
4.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação
1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política personalizada de Edição de Perfil usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta da Microsoft.

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
