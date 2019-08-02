---
title: Adicionar conta da Microsoft (MSA) como um provedor de identidade usando políticas personalizadas no Azure Active Directory B2C
description: Exemplo de como usar Microsoft como provedor de identidade usando o protocolo OIDC (OpenID Connect).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654144"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta Microsoft usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma conta Microsoft usando as [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se você ainda não tiver uma conta Microsoft, crie uma em [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Adicionar um aplicativo

Para habilitar a entrada para usuários com uma conta da Microsoft, você precisa registrar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD clicando nos **filtros de assinatura e diretório** no menu superior e escolhendo o diretório que contém o locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Sob **suporte para tipos de conta**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, o Skype, Xbox, Outlook.com)** .
1. Sob **URI de redirecionamento (opcional)** , selecione **Web** e digite `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` pelo nome do locatário do Azure AD B2C.
1. Selecione **registrar**
1. Registro a **ID do aplicativo (cliente)** mostrada na página de visão geral do aplicativo. Você precisará dela quando você configura o provedor de declarações em uma seção posterior.
1. Selecione **certificados e segredos**
1. Clique em **novo segredo do cliente**
1. Insira um **descrição** para o segredo, por exemplo *segredo do cliente de aplicativo MSA*e, em seguida, clique em **adicionar**.
1. Registre a senha de aplicativo, mostrada na **valor** coluna. Use esse valor na próxima seção.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que você criou o aplicativo em seu locatário do Azure AD, você precisa armazenar o segredo do cliente do aplicativo em seu locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Na **segredo**, insira o segredo do cliente que você registrou na seção anterior.
1. Para **Uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Para permitir que seus usuários façam logon usando uma conta da Microsoft, você precisará definir a conta como um provedor de declarações que podem se comunicar com o Azure AD B2C por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações, adicionando o elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o *trustframeworkextensions. XML* arquivo de política.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

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
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
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

1. Substitua o valor de **client_id** com o aplicativo do Azure AD *ID do aplicativo (cliente)* que você anotou anteriormente.
1. Salve o arquivo.

Agora você configurou sua política para que o Azure AD B2C Saiba como se comunicar com seu aplicativo de conta da Microsoft no Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Antes de continuar, carregue a política modificada para confirmar que ele não apresenta problemas até o momento.

1. Navegue até seu locatário do Azure AD B2C no portal do Azure e selecione **Identity Experience Framework**.
1. Sobre o **políticas personalizadas** página, selecione **carregue a política personalizada**.
1. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
1. Clique em **Carregar**.

Se nenhum erro for exibido no portal, continue na próxima seção.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, você configurou o provedor de identidade, mas ele ainda não está disponível em nenhuma das telas de inscrição ou entrada. Para disponibilizá-lo, cria uma duplicata de um percurso de usuário do modelo existente e, em seguida, modificá-lo para que ele também tenha o provedor de identidade de conta da Microsoft.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
1. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
1. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um **ClaimsProviderSelection** elemento para uma conta da Microsoft, um novo botão será exibido quando um usuário chega à página.

1. No arquivo *TrustFrameworkExtensions.xml*, localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
1. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. A ação, nesse caso, é para o Azure AD B2C se comunicar com uma conta da Microsoft para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualizar o valor de **TechnicalProfileReferenceId** para coincidir com o `Id` o valor a **TechnicalProfile** elemento do provedor de declarações que você adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria no seu locatário do Azure AD B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo, por exemplo *testapp1*.
1. Para **Aplicativo Web/API Web**, selecione `Yes` e, em seguida, insira `https://jwt.ms` para a **URL de resposta**.
1. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInMSA.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualizar o valor da **ReferenceId** atributo no **DefaultUserJourney** para coincidir com a ID do percurso do usuário que você criou anteriormente (SignUpSignInMSA).
1. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Verifique se o aplicativo do Azure AD B2C que você criou na seção anterior (ou ao concluir os pré-requisitos, por exemplo *webapp1* ou *testapp1*) está selecionado no **selecione aplicativo** campo e, em seguida, testá-lo clicando **executar agora**.
1. Selecione o **Account da Microsoft** botão e entre.

    Se a operação for bem-sucedida, você será redirecionado para `jwt.ms` que exibe o decodificado Token, semelhante a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
