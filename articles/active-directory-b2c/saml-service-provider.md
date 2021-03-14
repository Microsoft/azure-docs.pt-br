---
title: Configurar Azure Active Directory B2C como um IdP do SAML para seus aplicativos
title-suffix: Azure Active Directory B2C
description: Como configurar Azure Active Directory B2C para fornecer asserções de protocolo SAML para seus aplicativos (provedores de serviço). O Azure AD B2C funcionará como o IdP (provedor de identidade única) para seu aplicativo SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107259"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrar um aplicativo SAML no Azure AD B2C

Neste artigo, saiba como conectar seus aplicativos de Security Assertion Markup Language (SAML) (provedores de serviços) a Azure Active Directory B2C (Azure AD B2C) para autenticação.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Visão geral

As organizações que usam Azure AD B2C como sua solução de gerenciamento de acesso e identidade do cliente podem exigir integração com aplicativos que se autenticam usando o protocolo SAML. O diagrama a seguir mostra como Azure AD B2C serve como um IDP ( *provedor de identidade* ) para obter SSO (logon único) com aplicativos baseados em SAML.

![Diagrama com B2C como provedor de identidade à esquerda e B2C como provedor de serviços à direita.](media/saml-service-provider/saml-service-provider-integration.png)

1. O aplicativo cria uma solicitação de autenticação SAML que é enviada para o ponto de extremidade de logon do Azure AD B2C.
2. O usuário pode usar uma conta local Azure AD B2C ou qualquer outro provedor de identidade federada (se configurado) para autenticar.
3. Se o usuário entrar usando um provedor de identidade federada, uma resposta de token será enviada para Azure AD B2C.
4. Azure AD B2C gera uma Asserção SAML e a envia para o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas em [Introdução às políticas personalizadas no Azure AD B2C](custom-policy-get-started.md). Você precisa da política personalizada *SocialAndLocalAccounts* do pacote inicial de política personalizada abordado no artigo.
* Noções básicas sobre o protocolo SAML e familiaridade com a implementação SAML do aplicativo.
* Um aplicativo Web configurado como um aplicativo SAML. Para este tutorial, você pode usar um [aplicativo de teste SAML][samltest] que fornecemos.

## <a name="components"></a>Componentes

Há três componentes principais necessários para esse cenário:

* Um **aplicativo** SAML com a capacidade de enviar solicitações do SAML Authn e receber, decodificar e verificar respostas saml de Azure ad B2C. O aplicativo SAML também é conhecido como o aplicativo de terceira parte confiável ou o provedor de serviços.
* O **ponto de extremidade de metadados** SAML disponível publicamente do aplicativo SAML ou documento XML.
* Um [locatário Azure ad B2C](tutorial-create-tenant.md)

Se você ainda não tiver um aplicativo SAML e um ponto de extremidade de metadados associado, poderá usar este aplicativo SAML de exemplo que disponibilizamos para teste:

[Aplicativo de teste SAML][samltest]

## <a name="set-up-certificates"></a>Configurar certificados

Para criar uma relação de confiança entre seu aplicativo e Azure AD B2C, ambos os serviços devem ser capazes de criar e validar as assinaturas do outro. Você configura um configurar certificados X509 no Azure AD B2C e seu aplicativo.

**Certificados de aplicativo**

| Uso | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Assinatura de solicitação SAML  | Não | Um certificado com uma chave privada armazenada em seu aplicativo Web, usado pelo seu aplicativo para assinar solicitações SAML enviadas a Azure AD B2C. O aplicativo Web deve expor a chave pública por meio de seu ponto de extremidade de metadados do SAML. Azure AD B2C valida a assinatura de solicitação SAML usando a chave pública dos metadados do aplicativo.|
| Criptografia de Asserção SAML  | Não | Um certificado com uma chave privada armazenada em seu aplicativo Web. O aplicativo Web deve expor a chave pública por meio de seu ponto de extremidade de metadados do SAML. Azure AD B2C pode criptografar asserções para seu aplicativo usando a chave pública. O aplicativo usa a chave privada para descriptografar a asserção.|

**Certificados do Azure AD B2C**

| Uso | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Assinatura de resposta SAML | Sim | Um certificado com uma chave privada armazenada em Azure AD B2C. Esse certificado é usado pelo Azure AD B2C para assinar a resposta SAML enviada ao seu aplicativo. Seu aplicativo lê a chave pública de metadados Azure AD B2C para validar a assinatura da resposta SAML. |

Em um ambiente de produção, é recomendável usar certificados emitidos por uma autoridade de certificação pública. No entanto, você também pode concluir esse procedimento com certificados autoassinados.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Preparar um certificado autoassinado para assinatura de resposta SAML

Você deve criar um certificado de assinatura de resposta SAML para que seu aplicativo possa confiar na asserção de Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Habilitar sua política para se conectar a um aplicativo SAML

Para se conectar ao aplicativo SAML, Azure AD B2C deve ser capaz de criar respostas SAML.

Abra `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** no pacote inicial de política personalizada.

Localize a `<ClaimsProviders>` seção e adicione o trecho XML a seguir para implementar seu gerador de resposta SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Configurar o IssuerUri da resposta SAML

Você pode alterar o valor do `IssuerUri` item de metadados no perfil técnico do emissor do token SAML. Essa alteração será refletida no `issuerUri` atributo retornado na resposta SAML de Azure ad B2C. Seu aplicativo deve ser configurado para aceitar o mesmo `issuerUri` durante a validação de resposta SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Assinar o Azure AD B2C metadados SAML do IdP (opcional)

Você pode instruir Azure AD B2C a assinar seu documento de metadados do IdP do SAML, se exigido pelo aplicativo. Para fazer isso, gere e carregue uma chave de política de assinatura de metadados do IdP do SAML, conforme mostrado em [preparar um certificado autoassinado para assinatura de resposta SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Em seguida, configure o `MetadataSigning` item de metadados no perfil técnico do emissor do token SAML. O `StorageReferenceId` deve referenciar o nome da chave de política.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Assinar o Azure AD B2C elemento de resposta SAML do IdP (opcional)

Você pode especificar um certificado a ser usado para assinar as mensagens SAML. A mensagem é o `<samlp:Response>` elemento dentro da resposta SAML enviada ao aplicativo.

Para especificar um certificado, gere e carregue uma chave de política, conforme mostrado em [preparar um certificado autoassinado para assinatura de resposta SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Em seguida, configure o `SamlMessageSigning` item de metadados no perfil técnico do emissor do token SAML. O `StorageReferenceId` deve referenciar o nome da chave de política.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configurar sua política para emitir uma resposta SAML

Agora que sua política pode criar respostas SAML, você deve configurar a política para emitir uma resposta SAML em vez da resposta JWT padrão para seu aplicativo.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Criar uma política de inscrição ou entrada configurada para SAML

1. Crie uma cópia do arquivo *SignUpOrSignin.xml* no diretório de trabalho do pacote inicial e salve-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml*. Esse é o arquivo de política de terceira parte confiável e está configurado para emitir uma resposta JWT por padrão.

1. Abra o arquivo *SignUpOrSigninSAML.xml* no editor de sua preferência.

1. Altere `PolicyId` e `PublicPolicyUri` da política para _B2C_1A_signup_signin_saml_ e `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` conforme visto abaixo.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. No final da jornada do usuário, Azure AD B2C contém uma `SendClaims` etapa. Esta etapa faz referência ao perfil técnico emissor do token. Para emitir uma resposta SAML em vez da resposta JWT padrão, modifique a `SendClaims` etapa para fazer referência ao novo perfil técnico do emissor do token SAML, `Saml2AssertionIssuer` .

Adicione o seguinte trecho XML logo antes do `<RelyingParty>` elemento. Esse XML substitui o número 7 da etapa de orquestração no percurso do usuário _SignUpOrSignIn_ . Se você iniciou a partir de uma pasta diferente no pacote inicial ou se personalizou o percurso do usuário adicionando ou removendo etapas de orquestração, verifique se o número no `order` elemento corresponde ao número especificado no percurso do usuário para a etapa do emissor do token. Por exemplo, nas outras pastas do pacote de início, o número da etapa correspondente é 4 para `LocalAccounts` , 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

O elemento de terceira parte confiável determina o protocolo que seu aplicativo usa. O padrão é `OpenId`. O `Protocol` elemento deve ser alterado para `SAML` . As declarações de saída criarão o mapeamento de declarações para a Asserção SAML.

Substitua todo o elemento `<TechnicalProfile>` no elemento `<RelyingParty>` pelo XML de perfil técnico a seguir. Atualize `tenant-name` com o nome do seu locatário do Azure AD B2C.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

O arquivo de política de terceira parte confiável final deve ser semelhante ao seguinte código XML:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Você pode seguir esse mesmo processo para implementar outros tipos de fluxos de usuário (por exemplo, entrada, redefinição de senha ou fluxos de edição de perfil).

### <a name="upload-your-policy"></a>Carregar sua política

Salve as alterações e carregue os novos arquivos de política **TrustFrameworkExtensions.xml** e **SignUpOrSigninSAML.xml** para o portal do Azure.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testar o Azure AD B2C metadados SAML do IdP

Depois que os arquivos de política são carregados, Azure AD B2C usa as informações de configuração para gerar o documento de metadados SAML do provedor de identidade a ser usado pelo aplicativo. O documento de metadados do SAML contém os locais de serviços, como os métodos de entrada e logout, certificados e assim por diante.

Os metadados da política de Azure AD B2C estão disponíveis na seguinte URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Substitua `<tenant-name>` pelo nome do seu locatário do Azure ad B2C e `<policy-name>` pelo nome (ID) da política, por exemplo:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrar seu aplicativo SAML no Azure AD B2C

Para Azure AD B2C confiar em seu aplicativo, você cria um registro de aplicativo Azure AD B2C, que contém informações de configuração, como o ponto de extremidade de metadados do aplicativo.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *SAMLApp1*.
1. Em **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**.
1. Em **URI de Redirecionamento**, selecione **Web** e, em seguida, digite `https://localhost`. Você modificará esse valor posteriormente no manifesto do registro do aplicativo.
1. Selecione **Registrar**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Configurar seu aplicativo no Azure AD B2C

Para aplicativos SAML, você precisará configurar várias propriedades no manifesto do registro do aplicativo.

1. No [portal do Azure](https://portal.azure.com), navegue até o registro de aplicativo criado na seção anterior.
1. Em **gerenciar**, selecione **manifesto** para abrir o editor de manifesto e, em seguida, modifique as propriedades descritas nas seções a seguir.

#### <a name="add-the-identifier"></a>Adicionar o identificador

Quando o aplicativo SAML faz uma solicitação para Azure AD B2C, a solicitação de autenticação SAML inclui um `Issuer` atributo, que normalmente é o mesmo valor que os metadados do aplicativo `entityID` . Azure AD B2C usa esse valor para pesquisar o registro do aplicativo no diretório e ler a configuração. Para que essa pesquisa seja realizada com sucesso, o `identifierUri` no registro do aplicativo deve ser preenchido com um valor que corresponda ao `Issuer` atributo.

No manifesto de registro, localize o `identifierURIs` parâmetro e adicione o valor apropriado. Esse valor será o mesmo valor configurado nas solicitações de authid do SAML para EntityId no aplicativo e o `entityID` valor nos metadados do aplicativo.

O exemplo a seguir mostra o `entityID` nos metadados SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

A `identifierUris` propriedade só aceitará URLs no domínio `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Compartilhar os metadados do aplicativo com Azure AD B2C

Depois que o registro do aplicativo for carregado por seu `identifierUri` , Azure ad B2C usará os metadados do aplicativo para validar a solicitação do SAML Authn e determinar como responder.

É recomendável que seu aplicativo exponha um ponto de extremidade de metadados publicamente acessível.

Se houver propriedades *especificadas na URL* de metadados SAML e no manifesto do registro do aplicativo, elas serão *mescladas*. As propriedades especificadas na URL de metadados são processadas primeiro e têm precedência.

Usando o aplicativo de teste do SAML como um exemplo, você usaria o seguinte valor para `samlMetadataUrl` no manifesto do aplicativo:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Substituir ou definir a URL do consumidor de asserção (opcional)

Você pode configurar a URL de resposta para a qual Azure AD B2C envia respostas SAML. As URLs de resposta podem ser configuradas no manifesto do aplicativo. Essa configuração é útil quando seu aplicativo não expõe um ponto de extremidade de metadados publicamente acessível.

A URL de resposta para um aplicativo SAML é o ponto de extremidade no qual o aplicativo espera receber respostas SAML. O aplicativo geralmente fornece essa URL no documento de metadados sob o `AssertionConsumerServiceUrl` atributo, como mostrado abaixo:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Se você quiser substituir os metadados fornecidos no `AssertionConsumerServiceUrl` atributo ou a URL não estiver presente no documento de metadados, você poderá configurar a URL no manifesto sob a `replyUrlsWithType` propriedade. O `BindingType` será definido como `HTTP POST` .

Usando o aplicativo de teste do SAML como um exemplo, você definiria a `url` propriedade de `replyUrlsWithType` como o valor mostrado no trecho de JSON a seguir.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Substituir ou definir a URL de logout (opcional)

Você pode configurar a URL de logout para a qual Azure AD B2C enviará o usuário após uma solicitação de logoff. As URLs de resposta podem ser configuradas no manifesto do aplicativo.

Se você quiser substituir os metadados fornecidos no `SingleLogoutService` atributo ou a URL não estiver presente no documento de metadados, você poderá configurá-lo no manifesto sob a `Logout` propriedade. O `BindingType` será definido como `Http-Redirect` .

O aplicativo geralmente fornece essa URL no documento de metadados sob o `AssertionConsumerServiceUrl` atributo, como mostrado abaixo:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Usando o aplicativo de teste do SAML como um exemplo, você deve deixar `logoutUrl` definido como `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Se você optar por configurar a URL de resposta e a URL de logout no manifesto do aplicativo sem preencher o ponto de extremidade de metadados do aplicativo por meio da `samlMetadataUrl` propriedade, Azure ad B2C não validará a assinatura de solicitação SAML, nem criptografará a resposta SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Configurar Azure AD B2C como um IdP do SAML em seu aplicativo SAML

A última etapa é habilitar Azure AD B2C como um IdP SAML em seu aplicativo SAML. Cada aplicativo é diferente e as etapas variam. Consulte a documentação do seu aplicativo para obter detalhes.

Os metadados podem ser configurados em seu aplicativo como *metadados estáticos* ou *dinâmicos*. No modo estático, copie todos ou parte dos metadados dos metadados da política de Azure AD B2C. No modo dinâmico, forneça a URL para os metadados e para permitir que seu aplicativo Leia os metadados dinamicamente.

Alguns ou todos os seguintes itens são normalmente necessários:

* **Metadados**: Use o formato `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Emissor**: o valor da solicitação SAML `issuer` deve corresponder a um dos URIs configurados no `identifierUris` elemento do manifesto de registro do aplicativo. Se o nome da solicitação SAML `issuer` não existir no `identifierUris` elemento, [adicione-o ao manifesto de registro do aplicativo](#add-the-identifier). Por exemplo, `https://contoso.onmicrosoft.com/app-name`. 
* **URL de logon/ponto de extremidade SAML/URL SAML**: Verifique o valor no arquivo de metadados de política SAML Azure ad B2C para o `<SingleSignOnService>` elemento XML.
* **Certificado**: esse certificado é *B2C_1A_SamlIdpCert*, mas sem a chave privada. Para obter a chave pública do certificado:

    1. acesse a URL de metadados especificada acima.
    1. Copie o valor no elemento `<X509Certificate>`.
    1. Cole-o em um arquivo de texto.
    1. Salve o arquivo de texto como um arquivo *.cer*.

### <a name="test-with-the-saml-test-app"></a>Testar com o aplicativo de teste do SAML

Você pode usar nosso [aplicativo de teste do SAML][samltest] para testar sua configuração:

* Atualize o nome do locatário.
* Atualize o nome da política, por exemplo *B2C_1A_signup_signin_saml*.
* Especifique esse URI do emissor. Use um dos URIs encontrados no `identifierUris` elemento no manifesto de registro do aplicativo, por exemplo `https://contoso.onmicrosoft.com/app-name` .

Selecione **Logon** e você deverá receber uma tela de entrada do usuário. Após a entrada, uma resposta SAML é emitida de volta para o aplicativo de exemplo.

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML com e sem suporte

Os cenários de aplicativo SAML a seguir têm suporte por meio de seu próprio ponto de extremidade de metadados:

* Várias URLs de logout ou pós-Associação para URL de logout no objeto de aplicativo/entidade de serviço.
* Especifique uma chave de assinatura para verificar as solicitações de RP (terceira parte confiável) no objeto de aplicativo/entidade de serviço.
* Especifique uma chave de criptografia de token no objeto de aplicativo/entidade de serviço.
* Logon iniciado pelo provedor de identidade, no qual o provedor de identidade está Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

- Obtenha o aplicativo Web de teste SAML do [repositório da comunidade GitHub do Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).
- Consulte as [opções para registrar um aplicativo SAML no Azure ad B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
