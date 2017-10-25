---
title: "Azure Active Directory B2C: adicionar LinkedIn como um provedor de identidade OAuth2 usando políticas personalizadas"
description: "Um artigo de instruções sobre como configurar o aplicativo LinkedIn usando o protocolo OAuth2 e políticas personalizadas"
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adicionar LinkedIn como um provedor de identidade usando políticas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários da conta do LinkedIn por meio de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:
1.  Crie um aplicativo de conta do LinkedIn.
2.  Adicionar a chave de aplicativo da conta do LinkedIn ao Azure AD B2C
3.  Adicionar o provedor de declarações a uma política
4.  Registrar o provedor de declarações da conta do LinkedIn a um percurso do usuário
5.  Carregar a política para um locatário do Azure AD B2C e testá-la

## <a name="step-1-create-a-linkedin-account-application"></a>Etapa 1: Criar um aplicativo de conta do LinkedIn
Para usar o LinkedIn como provedor de identidade no Azure AD (Azure Active Directory) B2C, será necessário primeiro criar um aplicativo do LinkedIn  e fornecê-lo com os parâmetros corretos. Você pode registrar um aplicativo do LinkedIn aqui: [https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  Navegue para o site [Gerenciamento de aplicativos do LinkedIn](https://www.linkedin.com/secure/developer?newapp=), entre com suas credenciais de conta do LinkedIn e clique em **Criar Aplicativo**.

    ![Conta do LinkedIn – criar aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  Digite o **Nome da Empresa**, forneça um **Nome** descritivo e uma **Descrição** para seu novo aplicativo.
    2.  Carregar o **Logotipo do aplicativo**
    3.  Selecione **Uso do Aplicativo**
    4.  Cole em `https://login.microsoftonline.com` para o valor da **URL do site**.
    5.  Digite seu **Email Comercial** e seu **Telefone Comercial**
    6.  Na parte inferior da página, leia e aceite os termos de uso. Então clique em **Enviar**

    ![Conta do LinkedIn – configurar propriedades do aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  No menu, clique em **Autenticação**. Tome nota dos valores de **ID do Cliente** e **Segredo do Cliente**.

    Para as **URLs de redirecionamento autorizadas**, cole o `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Substitua {tenant} pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). verifique se você está usando o esquema HTTPS e clique em **Adicionar**

    ![Conta do LinkedIn – definir URLs de redirecionamento autorizadas](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >O Segredo do Cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

4.  Clique na guia **Configurações**, altere o **Status do Aplicativo** para **Live** e clique em **Atualização**.

    ![Conta do LinkedIn – definir o status do aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Etapa 2: Adicionar a chave de aplicativo do LinkedIn ao Azure AD B2C
A federação com contas do LinkedIn exige um segredo do cliente para a conta do LinkedIn confiar no Azure AD B2C em nome do aplicativo. Você precisa armazenar o segredo do aplicativo do LinkdIn no seu locatário do Azure AD B2C:  

1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `LinkedInSecret`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, insira o segredo de aplicativo do LinkdIn de https://apps.dev.microsoft.com
7.  Para o **Uso da chave**, use **Criptografia**.
8.  Clique em **Criar** 
9.  Confirme que você criou a chave `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Etapa 3: adicionar um provedor de declarações à sua política de extensão
Se quiser que os usuários entrem usando a conta do LinkedIn, você precisará definir o LinkedIn como um provedor de declarações. Em outras palavras, você precisa especificar pontos de extremidade com os quais o Azure AD B2C se comunique. Os pontos de extremidade fornecem um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina o LinkedIn como um provedor de declarações adicionando o nó `<ClaimsProvider>` ao seu arquivo de política da extensão:

1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. 
2.  Localize a seção `<ClaimsProviders>`
3.  Adicione o seguinte trecho XML no nó `<ClaimsProviders>`:  

  ```xml
  <ClaimsProvider>
    <Domain>linkedin.com</Domain>
    <DisplayName>LinkedIn</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="LinkedIn-OAUTH">
        <DisplayName>LinkedIn</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">linkedin</Item>
          <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
          <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
          <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
          <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
          <Item Key="ClaimsEndpointFormatName">format</Item>
          <Item Key="ClaimsEndpointFormat">json</Item>
          <Item Key="scope">r_emailaddress r_basicprofile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your LinkedIn application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
          <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4.  Substitua o valor `client_id` pela ID de cliente do aplicativo do LinkedIn
5.  Salve o arquivo.

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Etapa 4: Registrar o provedor de declarações da conta do LinkedIn para um percurso do usuário de Inscrição ou Entrada
Neste ponto, o provedor de identidade foi configurado. No entanto, ele não está disponível em qualquer uma das telas de inscrição/entrada. Agora você precisa adicionar o provedor de identidade da conta do LinkedIn ao percurso de usuário `SignUpOrSignIn` do usuário.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Etapa 4.1 Fazer uma cópia do jornada de usuário
Para disponibilizá-la, você cria uma duplicata de um percurso do usuário do modelo existente. Em seguida, adicione o provedor de identidade do LinkedIn:

> [!NOTE]
>
>Se você tiver copiado o elemento `<UserJourneys>` do arquivo de base de sua política para o arquivo de extensão (TrustFrameworkExtensions.xml), poderá ignorar esta seção.

1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<UserJourneys>` e copie todo o conteúdo do nó `<UserJourneys>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<UserJournesy>` copiado como um filho do elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Etapa 4.2 Exibir o “botão”
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem.  O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento `<ClaimsProviderSelection>` à do LinkedIn, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1.  Localize o nó `<UserJourney>` que inclui `Id="SignUpOrSignIn"` no percurso do usuário que você acabou de copiar.
2.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
3.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>Etapa 4.3 Vincular o botão a uma ação
Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C se comunicar com a conta do LinkedIn para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da conta do LinkedIn:

1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior
    > * Garanta que a ID `TechnicalProfileReferenceId` esteja definida para o perfil técnico criado anteriormente (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: Carregar a política para o seu locatário
1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e clique em **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Clique em **Todas as Políticas**.
4.  Selecione **Carregar Política**.
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    > [!NOTE]
    >
    >A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>Etapa 7: [Opcional] Registrar o provedor de declarações da conta do LinkedIn para o percurso de usuário de Edição de Perfil
Convém adicionar também o provedor de identidade da conta do LinkedIn ao percurso de usuário `ProfileEdit` do usuário. Para disponibilizá-lo, você deve repetir a etapa nº 4. Neste momento, selecione o nó `<UserJourney>` que inclui `Id="ProfileEdit"`. Salve, carregue e teste sua política.

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)