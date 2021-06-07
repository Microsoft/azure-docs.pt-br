---
title: Configurar a inscrição e a entrada com uma conta do Salesforce
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes que usam contas do Salesforce em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f87ef03a3af110ec17b7d907d4c2eac1bc1ca58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579919"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Salesforce usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Criar um aplicativo do Salesforce

Para habilitar a entrada de usuários com um conta do Salesforce no Azure AD B2C (Azure Active Directory B2C), você precisa criar um aplicativo do Salesforce no [Gerenciador de Aplicativos](https://login.salesforce.com/). Para obter mais informações, confira [Definir configurações básicas do aplicativo conectado](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm) e [Habilitar configurações de OAuth para integração de API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Entre no Salesforce](https://login.salesforce.com/).
1. No menu, selecione **Instalação**.
1.  Expanda **Aplicativos** e selecione **Gerenciador de Aplicativos**.
1. Selecione **Novo Aplicativo Conectado**.
1. Nas **Informações Básicas**, insira:
    1. **Nome do Aplicativo Conectado** – o nome do aplicativo conectado é exibido no Gerenciador de Aplicativos e no bloco Inicializador de Aplicativos. O nome precisa ser exclusivo dentro da organização. 
    1. **Nome da API** 
    1. **Email de Contato** – o email de contato do Salesforce
1. Em **API (Habilitar Configurações de OAuth)** , selecione **Habilitar Configurações de OAuth**
    1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` como o valor da **URL de Retorno de Chamada**. Se você usa um [domínio personalizado](custom-domain.md), insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do locatário e `your-domain-name` pelo nome do domínio personalizado. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
    1. Nos **Escopos de OAuth Selecionados**, selecione **Acessar suas informações básicas (ID, perfil, email, endereço, telefone)** e **Permitir acesso ao seu identificador exclusivo (OpenID)** .
    1. Selecione **Exigir Segredo para Fluxo do Servidor Web**.
1. Selecione **Configurar Token de ID** 
    1. Defina **Token Válido por** 5 minutos.
    1. Selecione **Incluir Declarações Padrão**.
1. Clique em **Salvar**.
1. Copie os valores de **Chave do Consumidor** e **Segredo do Consumidor**. Você precisará de ambas para configurar o Salesforce como provedor de identidade em seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como provedor de identidade

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Salesforce*.
1. Para **URL de Metadados**, insira a URL do [Documento de configuração do OpenID Connect do Salesforce](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Para uma área restrita, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, login.salesforce.com é substituído pela URL da comunidade, por exemplo, username.force.com/.well-known/openid-configuration. A URL deve ser HTTPS.

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

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Adicionar provedor de identidade do Salesforce a um fluxo de usuário 

Neste ponto, o provedor de identidade do Salesforce foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Para adicionar o provedor de identidade do Salesforce a um fluxo de usuário:

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual deseja adicionar o provedor de identidade do Salesforce.
1. Em **Provedores de identidade social**, selecione **Salesforce**.
1. Selecione **Salvar**.
1. Para testar a política, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *testapp1* registrado anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar fluxo de usuário**.
1. Na página de entrada ou de inscrição, selecione **Salesforce** para entrar com a conta do Salesforce.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

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

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como provedor de identidade

Para permitir que os usuários entrem usando uma conta do Salesforce, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

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

4. Os **METADADOS** são definidos como a URL do [Documento de configuração do OpenID Connect do Salesforce](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Para uma área restrita, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, login.salesforce.com é substituído pela URL da comunidade, por exemplo, username.force.com/.well-known/openid-configuration. A URL deve ser HTTPS.
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

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo, `B2C_1A_signup_signin`.
1. Para **Aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar agora**.
1. Na página de entrada ou de inscrição, selecione **Salesforce** para entrar com a conta do Salesforce.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar um token do Salesforce para seu aplicativo](idp-pass-through-user-flow.md).
