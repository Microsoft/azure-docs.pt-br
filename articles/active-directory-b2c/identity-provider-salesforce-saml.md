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
ms.date: 12/17/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e718f4a70b4d8311c57eb3e83ac7088c129b6207
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663561"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Configurar a entrada com um provedor de SAML do Salesforce usando o protocolo SAML no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma organização da Salesforce usando [políticas personalizadas](custom-policy-overview.md) no Azure AD B2C (Azure Active Directory B2C). Você habilita a credencial adicionando um [perfil técnico de provedor de identidade do SAML](saml-identity-provider-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).
- Se você ainda não fez isso, inscreva-se para obter uma [conta gratuita da Developer Edition](https://developer.salesforce.com/signup). Este artigo usa o [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configurou Meu Domínio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para sua organização do Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

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

6. No campo **URL ACS**, digite a URL a seguir. Substitua o valor de `your-tenant` pelo nome do seu locatário do Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Role até a parte inferior da lista e, em seguida, clique em **Salvar**.

### <a name="get-the-metadata-url"></a>Obter a URL de metadados

1. Na página de visão geral de seu aplicativo conectado, clique em **Gerenciar**.
2. Copie o valor de **Ponto de Extremidade de Descoberta de Metadados** e salve-o. Você o usará posteriormente neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar os usuários do Salesforce para federação

1. Na página **Gerenciar** do aplicativo conectado, clique em **Gerenciar Perfis**.
2. Selecione os perfis (ou grupos de usuários) que você deseja federar com o Azure AD B2C. Como administrador do sistema, marque a caixa de seleção **Administrador do Sistema** para que você possa estabelecer a federação usando sua conta do Salesforce.

## <a name="generate-a-signing-certificate"></a>Gerar um certificado de autenticação

Solicitações enviadas para o Salesforce precisam ser assinadas pelo Azure AD B2C. Para gerar um certificado de autenticação, abra o Azure PowerShell e, em seguida, execute os seguintes comandos.

> [!NOTE]
> Não deixe de atualizar o nome do locatário e a senha nas primeiras duas linhas.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

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

Você pode definir uma conta da Salesforce como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política. Para obter mais informações, confira [definir um perfil técnico do provedor de identidade SAML](saml-identity-provider-technical-profile.md).

1. Abra *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Até agora, você configurou a política para que o Azure AD B2C saiba como se comunicar com a conta da Salesforce. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade já foi definido, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, você criará uma duplicata de um percurso do usuário de modelo existente e, depois, o modificará para que ele também tenha o provedor de identidade Salesforce.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta do LinkedIn, um novo botão será exibido quando o usuário chegar à página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você acabou de criar.
2. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `SalesforceExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para que o Azure AD B2C se comunique com a conta da Salesforce para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo, `salesforce` ou `LinkedIn-OAUTH`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você acabou de criar:

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignInSalesforce.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInSalesforce`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInSalesforce).
5. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

::: zone-end