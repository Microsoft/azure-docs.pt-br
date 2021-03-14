---
title: Configurar a inscrição e a entrada com uma conta de Azure AD B2C de outro locatário Azure AD B2C
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas de Azure AD B2C de outro locatário em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c9a20305f05b285b29d4a5eaf75116c862f3a6d4
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448482"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configurar a inscrição e a entrada com uma conta de Azure AD B2C de outro locatário Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Visão geral

Este artigo descreve como configurar uma federação com outro locatário Azure AD B2C. Quando seus aplicativos estiverem protegidos com seu Azure AD B2C, isso permitirá que os usuários de outros B2C do Azure AD façam logon com suas contas existentes. No diagrama a seguir, os usuários podem entrar em um aplicativo protegido pelo Azure AD B2C da *contoso*, com uma conta gerenciada pelo locatário do Azure ad B2C da *Fabrikam* 

![Azure AD B2C Federação com outro locatário de Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

Para habilitar a entrada para usuários com uma conta de outro locatário Azure AD B2C (por exemplo, fabrikam), em seu Azure AD B2C (por exemplo, contoso):

1. Crie um [fluxo de usuário](tutorial-create-user-flows.md)ou uma [política personalizada](custom-policy-get-started.md).
1. Em seguida, crie um aplicativo no Azure AD B2C, como descrito nesta seção. 

Para criar um aplicativo.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu outro locatário de Azure AD B2C (por exemplo, Fabrikam.com).
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *ContosoApp*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos dos usuários)** .
1. Em **URI de redirecionamento**, selecione **Web** e, em seguida, insira a URL a seguir em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu locatário de Azure ad B2C (por exemplo, contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Em permissões, marque a caixa de seleção **conceder consentimento de administrador às permissões OpenID e offline_access** .
1. Selecione **Registrar**.
1. Na página **Azure AD B2C registros de aplicativo** , selecione o aplicativo que você criou, por exemplo *ContosoApp*.
1. Registre a **ID do aplicativo (do cliente)** mostrada na página Visão geral do aplicativo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.
1. No menu à esquerda, em **Gerenciar**, selecione **Certificados e segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição**. Por exemplo, *clientsecret1*.
1. Em **Expirar**, selecione um período durante o qual o segredo será válido e clique em **Adicionar**.
1. Registre o **Valor** do segredo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurar Azure AD B2C como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o Azure AD B2C locatário que deseja configurar a Federação (por exemplo, contoso). Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C (por exemplo, contoso).
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, digite *Fabrikam*.
1. Para **URL de metadados**, insira a seguinte URL substituindo `{tenant}` pelo nome de domínio do seu locatário de Azure ad B2C (por exemplo, fabrikam). Substitua o `{policy}` pelo nome da política que você configurou no outro locatário:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **Segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. No **Escopo**, insira o `openid`.
1. Deixe os valores padrão para **Tipo de resposta** e **Modo de resposta**.
1. Adicional Para a **dica de domínio**, insira o nome de domínio que você deseja usar para a [entrada direta](direct-signin.md#redirect-sign-in-to-a-social-provider). Por exemplo, *fabrikam.com*.
1. Em **Mapeamento de declarações do provedor de identidade**, selecione as seguintes declarações:

    - **ID de usuário**: *sub*
    - **Nome de exibição**: *name*
    - **Nome fornecido**: *given_name*
    - **Sobrenome**: *family_name*
    - **Email**: *email*

1. Clique em **Salvar**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Adicionar Azure AD B2C provedor de identidade a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual você deseja adicionar o provedor de identidade Azure AD B2C.
1. Em **provedores de identidade social**, selecione **Fabrikam**.
1. Clique em **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Na página inscrever-se ou entrar, selecione **Fabrikam** para entrar com o outro locatário Azure ad B2C.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que você criou anteriormente em seu locatário Azure AD B2C.

1. Verifique se você está usando o diretório que contém o Azure AD B2C locatário que deseja configurar a Federação (por exemplo, contoso). Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C (por exemplo, contoso).
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `FabrikamAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, sua referência no XML na seção a seguir é *B2C_1A_FabrikamAppSecret*.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurar Azure AD B2C como um provedor de identidade

Para permitir que os usuários entrem usando uma conta de outro locatário do Azure AD B2C (Fabrikam), você precisa definir os outros Azure AD B2C como um provedor de declarações que Azure AD B2C possa se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir Azure ad B2C como um provedor de declarações adicionando Azure ad B2C ao elemento **Claims** no arquivo de extensão da política.

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualize os seguintes elementos XML com o valor relevante:

    |Elemento XML  |Valor  |
    |---------|---------|
    |ClaimsProvider\Domain  | O nome de domínio que é usado para [entrada direta](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Insira o nome de domínio que você deseja usar na entrada direta. Por exemplo, *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Esse valor será exibido no botão de entrada em sua tela de entrada. Por exemplo, *Fabrikam*. |
    |Metadados \ client_id|O identificador do aplicativo do provedor de identidade. Atualize a ID do cliente com a ID do aplicativo que você criou anteriormente no outro locatário do Azure AD B2C.|
    |Metadata\METADATA|Uma URL que aponta para um documento de configuração do provedor de identidade do OpenID Connect, que também é conhecido como ponto de extremidade de configuração bem conhecido do OpenID. Insira a URL a seguir substituindo `{tenant}` pelo nome de domínio do outro locatário de Azure ad B2C (Fabrikam). Substitua o `{tenant}` pelo nome da política que você configurou no outro locatário e `{policy]` com o nome da política: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Atualize o valor de **StorageReferenceId** para o nome da chave de política que você criou anteriormente. Por exemplo, `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **Fabrikam** para entrar com o outro locatário Azure ad B2C.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar o outro token Azure ad B2C para seu aplicativo](idp-pass-through-user-flow.md).
