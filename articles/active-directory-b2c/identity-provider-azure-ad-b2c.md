---
title: Configurar a inscrição e a entrada com uma conta do Azure AD B2C de outro locatário Azure AD B2C
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos clientes com contas do Azure AD B2C de outro locatário nos seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4b357213f4e552fd791fb575d8b7a287b924c7f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489063"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configurar a inscrição e a entrada com uma conta do Azure AD B2C de outro locatário Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Visão geral

Este artigo descreve como configurar uma federação com outro locatário do Azure AD B2C. Quando seus aplicativos estiverem protegidos com seu Azure AD B2C, isso permitirá que os usuários de outras contas do Azure AD B2C façam logon com as contas existentes. No diagrama a seguir, os usuários podem entrar em um aplicativo protegido pelo Azure AD B2C da *Contoso*, com uma conta gerenciada pelo locatário do Azure AD B2C da *Fabrikam* 

![Federação do Azure AD B2C com outro locatário do Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

Para habilitar a entrada de usuários com uma conta de outro locatário do Azure AD B2C (por exemplo, Fabrikam), no seu Azure AD B2C (por exemplo, Contoso):

1. Crie um [fluxo de usuário](tutorial-create-user-flows.md) ou uma [política personalizada](custom-policy-get-started.md).
1. Em seguida, crie um aplicativo no Azure AD B2C, como descrito nesta seção. 

Para criar um aplicativo.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu outro locatário do Azure AD B2C (por exemplo, Fabrikam.com).
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *ContosoApp*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos dos usuários)** .
1. Em **URI de Redirecionamento**, selecione **Web** e insira a URL a seguir com todas as letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário do Azure AD B2C (por exemplo, Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Se você usar um [domínio personalizado](custom-domain.md), insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-domain-name` pelo seu domínio personalizado e `your-tenant-name` pelo nome do seu locatário.

1. Em Permissões, marque a caixa de seleção **Dar consentimento do administrador às permissões OpenID e offline_access**.
1. Selecione **Registrar**.
1. Na página **Azure AD B2C – Registros de aplicativo**, selecione o aplicativo criado, por exemplo *ContosoApp*.
1. Registre a **ID do aplicativo (do cliente)** mostrada na página Visão geral do aplicativo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.
1. No menu à esquerda, em **Gerenciar**, selecione **Certificados e segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição**. Por exemplo, *clientsecret1*.
1. Em **Expirar**, selecione um período durante o qual o segredo será válido e clique em **Adicionar**.
1. Registre o **Valor** do segredo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurar o Azure AD B2C como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C que deseja configurar a federação (por exemplo, Contoso). Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C (por exemplo, Contoso).
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Fabrikam*.
1. Para a **URL dos metadados**, insira a URL a seguir, substituindo `{tenant}` pelo nome de domínio do locatário do Azure AD B2C (for exemplo, Fabrikam). Substitua o `{policy}` pelo nome da política que você configurou no outro locatário:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **Segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. No **Escopo**, insira o `openid`.
1. Deixe os valores padrão para **Tipo de resposta** e **Modo de resposta**.
1. (Opcional) Na **Dica de domínio**, insira o nome de domínio que você deseja usar para a [entrada direta](direct-signin.md#redirect-sign-in-to-a-social-provider). Por exemplo, *fabrikam.com*.
1. Em **Mapeamento de declarações do provedor de identidade**, selecione as seguintes declarações:

    - **ID de usuário**: *sub*
    - **Nome de exibição**: *name*
    - **Nome fornecido**: *given_name*
    - **Sobrenome**: *family_name*
    - **Email**: *email*

1. Clique em **Salvar**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Azure AD B2C a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual você deseja adicionar o provedor de identidade do Azure AD B2C.
1. Em **Provedores de identidade social**, selecione **Fabrikam**.
1. Clique em **Salvar**.
1. Para testar sua política, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar fluxo de usuário**.
1. Na página de inscrição ou de entrada, selecione **Fabrikam** para entrar com o outro locatário do Azure AD B2C.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que criou anteriormente no seu locatário do Azure AD B2C.

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C que deseja configurar a federação (por exemplo, Contoso). Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C (por exemplo, Contoso).
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **Chaves de Política** e escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `FabrikamAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, a referência dele no XML na seção a seguir é *B2C_1A_FabrikamAppSecret*.
1. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurar o Azure AD B2C como um provedor de identidade

Para permitir que os usuários entrem usando uma conta de outro locatário do Azure AD B2C (Fabrikam), você precisa definir o outro Azure AD B2C como um provedor de declarações com o qual o Azure AD B2C possa se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD B2C como um provedor de declarações adicionando o Azure AD B2C ao elemento **ClaimsProvider** no arquivo de extensão da sua política.

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
    |Metadata\client_id|O identificador do aplicativo do provedor de identidade. Atualize a ID do cliente com a ID do aplicativo que você criou anteriormente no outro locatário do Azure AD B2C.|
    |Metadata\METADATA|Uma URL que aponta para um documento de configuração do provedor de identidade do OpenID Connect, que também é conhecido como ponto de extremidade de configuração bem conhecido do OpenID. Insira a URL a seguir substituindo `{tenant}` pelo nome de domínio do outro locatário do Azure AD B2C (Fabrikam). Substitua a `{tenant}` pelo nome da política que você configurou no outro locatário e `{policy]` pelo nome da política: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration`. Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
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

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin`.
1. Para **Aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar agora**.
1. Na página de inscrição ou de entrada, selecione **Fabrikam** para entrar com o outro locatário do Azure AD B2C.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar o outro token do Azure AD B2C para seu aplicativo](idp-pass-through-user-flow.md).
