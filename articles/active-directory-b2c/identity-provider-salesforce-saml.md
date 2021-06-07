---
title: Configurar a entrada com um provedor de SAML do Salesforce usando o protocolo SAML
titleSuffix: Azure AD B2C
description: Configure a entrada com um provedor de SAML do Salesforce usando o protocolo SAML no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e740fdb9cd232892dadfe98c4d739759be66bf55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488713"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Configurar a entrada com um provedor de SAML do Salesforce usando o protocolo SAML no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma organização da Salesforce usando [políticas personalizadas](custom-policy-overview.md) no Azure AD B2C (Azure Active Directory B2C). Você habilita a entrada adicionando um provedor de [identidade SAML](identity-provider-generic-saml.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Se você ainda não fez isso, inscreva-se para obter uma [conta gratuita da Developer Edition](https://developer.salesforce.com/signup). Este artigo usa o [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configurou Meu Domínio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para sua organização do Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

1. [Entre no Salesforce](https://login.salesforce.com/).
2. No menu à esquerda, em **Configurações**, expanda **Identidade** e clique em **Provedor de Identidade**.
3. Selecione **Habilitar Provedor de Identidade**.
4. Em **Selecione um certificado**, selecione o certificado que você quer que o Salesforce use para se comunicar com o Azure AD B2C. Você pode usar o certificado padrão.
5. Clique em **Save** (Salvar).

### <a name="create-a-connected-app-in-salesforce"></a>Criar um aplicativo conectado no Salesforce

1. Na página **Provedor de Identidade**, selecione **Os provedores de serviço agora são criados por meio de aplicativos conectados. Clique aqui.**
2. Em **Informações Básicas**, forneça os valores necessários para seu aplicativo conectado.
3. Em **Configurações do Aplicativo Web**, marque a caixa **Habilitar SAML**.
4. No campo **ID da Entidade**, digite a URL a seguir. Substitua o valor de `your-tenant` pelo nome do seu locatário do Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      Ao usar um [domínio personalizado](custom-domain.md), use o seguinte formato:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. No campo **URL ACS**, digite a URL a seguir. Substitua o valor de `your-tenant` pelo nome do seu locatário do Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      Ao usar um [domínio personalizado](custom-domain.md), use o seguinte formato:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Role até a parte inferior da lista e, em seguida, clique em **Salvar**.

### <a name="get-the-metadata-url"></a>Obter a URL de metadados

1. Na página de visão geral de seu aplicativo conectado, clique em **Gerenciar**.
2. Copie o valor de **Ponto de Extremidade de Descoberta de Metadados** e salve-o. Você o usará posteriormente neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar os usuários do Salesforce para federação

1. Na página **Gerenciar** do aplicativo conectado, clique em **Gerenciar Perfis**.
2. Selecione os perfis (ou grupos de usuários) que você deseja federar com o Azure AD B2C. Como administrador do sistema, marque a caixa de seleção **Administrador do Sistema** para que você possa estabelecer a federação usando sua conta do Salesforce.

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado que criou no locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Insira um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Procure e selecione o certificado B2CSigningCert.pfx que você criou.
9. Insira a **Senha** do certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando uma conta da Salesforce, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta da Salesforce como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política. Para obter mais informações, consulte [definir um provedor de identidade SAML](identity-provider-generic-saml.md).

1. Abra *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualize o valor de **PartnerEntity** com a URL de metadados da Salesforce que você já copiou.
1. Atualize o valor de ambas as instâncias de **StorageReferenceId** para o nome da chave do seu certificado de autenticação. Por exemplo, B2C_1A_SAMLSigningCert.
1. Localize a seção `<ClaimsProviders>` e adicione o snippet XML a seguir. Se sua política já contiver o perfil técnico `SM-Saml-idp`, pule para a próxima etapa. Para saber mais, confira [gerenciamento de sessão de logon único](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Salve o arquivo.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **Salesforce** para entrar com a conta do Salesforce.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end