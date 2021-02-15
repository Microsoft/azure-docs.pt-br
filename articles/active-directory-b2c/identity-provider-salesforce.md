---
title: Configurar a inscrição e a entrada com uma conta do Salesforce
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas do Salesforce em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0981687b03344daf7a447cc4d9e50f0923341340
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952284"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Salesforce usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Criar um aplicativo do Salesforce

Para habilitar a entrada para usuários com uma conta do Salesforce no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no [Gerenciador de aplicativos](https://login.salesforce.com/)do Salesforce. Para obter mais informações, consulte [definir configurações básicas do aplicativo conectado](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)e [habilitar as configurações do OAuth para integração de API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Entre no Salesforce](https://login.salesforce.com/).
1. No menu, selecione **instalação**.
1.  Expanda **aplicativos** e, em seguida, selecione **Gerenciador de aplicativos**.
1. Selecione **novo aplicativo conectado**.
1. Sob as **informações básicas**, digite:
    1. **Nome do aplicativo conectado** -o nome do aplicativo conectado é exibido no Gerenciador de aplicativos e no bloco do inicializador de aplicativo. O nome deve ser exclusivo em sua organização. 
    1. **Nome da API** 
    1. **Email de contato** -o email de contato do Salesforce
1. Em **API (Habilitar configurações do OAuth)**, selecione **Habilitar configurações do OAuth**
    1. Em **URL de retorno de chamada**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
    1. Nos **escopos de OAuth selecionados**, selecione **acessar suas informações básicas (ID, perfil, email, endereço, telefone)** e **permitir acesso ao seu identificador exclusivo (OpenID)**.
    1. Selecione **exigir segredo para o fluxo do servidor Web**.
1. Selecione **Configurar token de ID** 
    1. Defina o **token válido por** 5 minutos.
    1. Selecione **incluir declarações padrão**.
1. Clique em **Salvar**.
1. Copie os valores da **chave do consumidor** e do **segredo do consumidor**. Você precisará de ambos para configurar o Salesforce como um provedor de identidade em seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Salesforce*.
1. Para **URL de metadados**, insira a URL do [documento de configuração do Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Para uma área restrita, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, o login.salesforce.com é substituído pela URL da Comunidade, como username.force.com/.well-known/openid-configuration. A URL deve ser HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **Segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. No **Escopo**, insira o `openid id profile email`.
1. Deixe os valores padrão para **Tipo de resposta** e **Modo de resposta**.
1. (Opcional) Em **Dica de domínio**, insira `contoso.com`. Para obter mais informações, confira [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Em **Mapeamento de declarações do provedor de identidade**, selecione as seguintes declarações:

    - **ID de usuário**: *sub*
    - **Nome de exibição**: *name*
    - **Nome fornecido**: *given_name*
    - **Sobrenome**: *family_name*
    - **Email**: *email*

1. Selecione **Salvar**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Salesforce a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual você deseja adicionar o provedor de identidade do Salesforce.
1. Em **provedores de identidade social**, selecione **Salesforce**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `SalesforceSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

Para permitir que os usuários entrem usando uma conta do Salesforce, você precisa definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta da Salesforce como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Os **metadados** são definidos para a URL do [documento de configuração do Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Para uma área restrita, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, o login.salesforce.com é substituído pela URL da Comunidade, como username.force.com/.well-known/openid-configuration. A URL deve ser HTTPS.
5. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
6. Salve o arquivo.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar o token do Salesforce para seu aplicativo](idp-pass-through-user-flow.md).
