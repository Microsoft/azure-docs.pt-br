---
title: Configurar a inscrição e a entrada com o provedor de identidade SAML
titleSuffix: Azure Active Directory B2C
description: Configure a inscrição e a entrada com qualquer provedor de identidade SAML (IdP) no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 357ea903ed4bbc87717dfefc1c542722f5bd40c0
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448397"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com o provedor de identidade SAML usando Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) dá suporte à Federação com provedores de identidade SAML 2,0. Este artigo mostra como habilitar a entrada com uma conta de usuário do provedor de identidade SAML, permitindo que os usuários entrem com suas identidades sociais ou empresariais existentes, como [ADFS](identity-provider-adfs2016-custom.md) e [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Visão geral do cenário

Você pode configurar Azure AD B2C para permitir que os usuários entrem em seu aplicativo com credenciais de IdP (provedores de identidade SAML) corporativos ou sociais externos. Quando Azure AD B2C federa um provedor de identidade SAML, ele atua como um **provedor de serviços** que inicia uma solicitação SAML para o **provedor de identidade** SAML e aguarda uma resposta SAML. No diagrama a seguir:

1. O aplicativo inicia uma solicitação de autorização para Azure AD B2C. O aplicativo pode ser um aplicativo [OAuth 2,0](protocols-overview.md) ou [OpenID Connect](openid-connect.md) ou um [provedor de serviços SAML](saml-service-provider.md). 
1. Na página de entrada Azure AD B2C, o usuário escolhe entrar com uma conta de provedor de identidade SAML (por exemplo, *contoso*). Azure AD B2C inicia uma solicitação de autorização SAML e leva o usuário para o provedor de identidade SAML para concluir a entrada.
1. O provedor de identidade SAML retorna uma resposta SAML.
1. Azure AD B2C valida o token SAML, extrai declarações, emite seu próprio token e leva o usuário de volta para o aplicativo.  

![Entrar com o fluxo do provedor de identidade SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Componentes da solução

Os seguintes componentes necessários são para este cenário:

* Um **provedor de identidade** SAML com a capacidade de receber, decodificar e responder a solicitações SAML de Azure ad B2C.
* Um **ponto de extremidade de metadados** SAML disponível publicamente para seu provedor de identidade.
* Um [locatário Azure ad B2C](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Criar uma chave de política

Para estabelecer a confiança entre Azure AD B2C e seu provedor de identidade SAML, você precisa fornecer um certificado X509 válido com a chave privada. Azure AD B2C assina as solicitações SAML usando a chave privada do certificado. O provedor de identidade valida a solicitação usando a chave pública do certificado. A chave pública pode ser acessada por meio de metadados de perfil técnico. Como alternativa, você pode fazer upload manualmente do arquivo .cer para seu provedor de identidade SAML.

Um certificado autoassinado é aceitável para a maioria dos cenários. Para ambientes de produção, é recomendável usar um certificado X509 emitido por uma autoridade de certificação. Além disso, conforme descrito posteriormente neste documento, para um ambiente de não produção é possível desabilitar a assinatura do SAML em ambos os lados.

### <a name="obtain-a-certificate"></a>Obter um certificado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Carregar o certificado

Você precisa armazenar o certificado em seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Upload`.
1. Insira um **Nome** para a chave de política. Por exemplo, `SAMLSigningCert`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Procure e selecione o arquivo .pfx do certificado com a chave privada.
1. Clique em **Criar**.

## <a name="configure-the-saml-technical-profile"></a>Configurar o perfil técnico SAML

Defina o provedor de identidade SAML adicionando-o ao elemento **ClaimsProviders** no arquivo de extensão da política. Os provedores de declarações contêm um perfil técnico SAML que determina os pontos de extremidade e os protocolos necessários para se comunicar com o provedor de identidade SAML. Para adicionar um provedor de declarações com um perfil técnico SAML:

1. Abra *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Atualize os seguintes elementos XML com o valor relevante:

|Elemento XML  |Valor  |
|---------|---------|
|ClaimsProvider\Domain  | O nome de domínio que é usado para [entrada direta](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Insira o nome de domínio que você deseja usar na entrada direta. Por exemplo, *contoso.com*. |
|TechnicalProfile\DisplayName|Esse valor será exibido no botão de entrada em sua tela de entrada. Por exemplo, *contoso*. |
|Metadata\PartnerEntity|URL dos metadados do provedor de identidade SAML. Ou você pode copiar os metadados do provedor de identidade e adicioná-los dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Mapear as declarações

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade SAML. Mapeie o nome da declaração definida em sua política para o nome de asserção definido no provedor de identidade. Verifique seu provedor de identidade para obter a lista de declarações (asserções). Para obter mais informações, consulte [Claims-Mapping](identity-provider-generic-saml-options.md#claims-mapping).

No exemplo acima, *contoso-SAML2* inclui as declarações retornadas por um provedor de identidade SAML:

* A Declaração **issuerUserId** é mapeada para a Declaração **assertionSubjectName** .
* A declaração **first_name** é mapeada para a declaração **givenName**.
* A declaração **last_name** é mapeada para a declaração **surname**.
* A Declaração **DisplayName** é mapeada para a `http://schemas.microsoft.com/identity/claims/displayname` declaração.
* A declaração **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

* A declaração **identityProvider** que contém o nome do provedor de identidade.
* A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Adicionar o perfil técnico da sessão SAML

Se você ainda não tiver o `SM-Saml-idp` perfil técnico de sessão do SAML, adicione um à sua política de extensão. Localize a seção `<ClaimsProviders>` e adicione o snippet XML a seguir. Se sua política já contiver o perfil técnico `SM-Saml-idp`, pule para a próxima etapa. Para saber mais, confira [gerenciamento de sessão de logon único](custom-policy-reference-sso.md).

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Configurar seu provedor de identidade SAML

Depois que a política estiver configurada, você precisará configurar seu provedor de identidade SAML com o Azure AD B2C metadados SAML. Os metadados SAML são informações usadas no protocolo SAML para expor a configuração da política, o provedor de **Serviços**. Ele define o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais...

Cada provedor de identidade SAML tem etapas diferentes para definir um provedor de serviços. Alguns provedores de identidade SAML solicitam os metadados de Azure AD B2C, enquanto outros exigem que você passe o arquivo de metadados manualmente e forneça as informações. Consulte a documentação do provedor de identidade para obter diretrizes.

O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico Azure AD B2C:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Substitua os seguintes valores:

- **seu locatário** com o nome do locatário, como Your-Tenant.onmicrosoft.com.
- **your-policy** pelo nome da política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do provedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue até a URL. Certifique-se de digitar a URL correta e ter acesso ao arquivo de metadados XML.

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **políticas**, selecione **estrutura de experiência de identidade**
1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **contoso** para entrar com a conta contoso.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

## <a name="next-steps"></a>Próximas etapas

- [Configurar opções do provedor de identidade SAML com Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
