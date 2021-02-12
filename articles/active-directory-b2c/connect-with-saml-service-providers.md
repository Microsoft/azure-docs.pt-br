---
title: Configurar o Azure AD B2C como um IdP SAML para seus aplicativos
title-suffix: Azure AD B2C
description: Como configurar o Azure AD B2C para fornecer declarações de protocolo SAML para seus aplicativos (provedores de serviço). O Azure AD B2C funcionará como o IdP (provedor de identidade única) para seu aplicativo SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96a72dbc0e45ebd50a49000ae66e3713cb28aa9a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916907"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrar um aplicativo SAML no Azure AD B2C

Neste artigo, você aprenderá a configurar o Azure AD B2C (Azure Active Directory B2C) para funcionar como um IdP (provedor de identidade) SAML (Security Assertion Markup Language) para seus aplicativos.

## <a name="scenario-overview"></a>Visão geral do cenário

As organizações que usam o Azure AD B2C como a solução de gerenciamento de acesso e identidade do cliente podem exigir interação com provedores de identidade ou aplicativos configurados para se autenticar usando o protocolo SAML.

O Azure AD B2C obtém a interoperabilidade SAML de uma das duas maneiras:

* Funcionando como um *IdP* (provedor de identidade) e obtendo SSO (logon único) com provedores de serviços baseados em SAML (seus aplicativos)
* Funcionando como um *SP* (provedor de serviços) e interagindo com provedores de identidade baseados em SAML como Salesforce e ADFS

![Diagrama com B2C como provedor de identidade à esquerda e B2C como provedor de serviços à direita](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Resumindo os dois principais cenários não exclusivos com SAML:

| Cenário | Função do Azure AD B2C | Como fazer |
| -------- | ----------------- | ------- |
| Meu aplicativo espera que uma declaração SAML conclua uma autenticação. | **O Azure AD B2C funciona como o IdP (provedor de identidade)**<br />O Azure AD B2C funciona como um IdP SAML para os aplicativos. | Este artigo. |
| Meus usuários precisam do logon único com um provedor de identidade em conformidade com SAML como ADFS, Salesforce ou Shibboleth.  | **O Azure AD B2C funciona como o SP (provedor de serviços)**<br />O Azure AD B2C funciona como um provedor de serviços ao se conectar ao provedor de identidade SAML. É um proxy de federação entre seu aplicativo e o provedor de identidade SAML.  | <ul><li>[Configurar a entrada com o ADFS como um IdP SAML usando políticas personalizadas](identity-provider-adfs.md)</li><li>[Configurar a entrada com um provedor SAML da Salesforce que usa políticas personalizadas](identity-provider-salesforce-saml.md)</li></ul> |

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas em [Introdução às políticas personalizadas no Azure AD B2C](custom-policy-get-started.md). Você precisa da política personalizada *SocialAndLocalAccounts* do pacote inicial de política personalizada abordado no artigo.
* Noções básicas sobre o protocolo SAML (Security Assertion Markup Language).
* Um aplicativo Web configurado como um SP (provedor de serviços) SAML. Para este tutorial, você pode usar um [aplicativo de teste SAML][samltest] que fornecemos.

## <a name="components-of-the-solution"></a>Componentes da solução

Há três componentes principais necessários para esse cenário:

* O **provedor de serviços** SAML com a capacidade de enviar solicitações SAML e receber, decodificar e responder a declarações SAML do Azure AD B2C. O provedor de serviços também é conhecido como o aplicativo de terceira parte confiável.
* **Ponto de extremidade de metadados** SAML publicamente disponível para seu provedor de serviços.
* [Locatário do Azure AD B2C](tutorial-create-tenant.md)

Se você ainda não tiver um provedor de serviços SAML e um ponto de extremidade de metadados associado, poderá usar este aplicativo SAML de exemplo que disponibilizamos para teste:

[Aplicativo de teste SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurar certificados

Para criar uma relação de confiança entre seu provedor de serviços e o Azure AD B2C, você precisa fornecer os certificados X509 do aplicativo Web.

* **Certificados de provedor de serviços**
  * Certificado com uma chave privada armazenada em seu Aplicativo Web. Esse certificado é usado pelo seu provedor de serviços para assinar a solicitação SAML enviada ao Azure AD B2C. O Azure AD B2C lê a chave pública dos metadados do provedor de serviços para validar a assinatura.
  * (Opcional) Certificado com uma chave privada armazenada em seu Aplicativo Web. O Azure AD B2C lê a chave pública dos metadados do provedor de serviços para criptografar a declaração SAML. Em seguida, o provedor de serviços usa a chave privada para descriptografar a declaração.
* **Certificados do Azure AD B2C**
  * Certificado com uma chave privada no Azure AD B2C. Esse certificado é usado pelo Azure AD B2C para assinar a resposta SAML enviada ao seu provedor de serviços. Seu provedor de serviços lê a chave pública de metadados do Azure AD B2C para validar a assinatura da resposta SAML.

Você pode usar um certificado emitido por uma autoridade de certificação pública ou, para este tutorial, um certificado autoassinado.

### <a name="11-create-a-self-signed-certificate"></a>1,1 criar um certificado autoassinado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="12-upload-the-certificate"></a>1.2 Carregar o certificado

Em seguida, carregue a declaração SAML e o certificado de assinatura de resposta para o Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o locatário do Azure AD B2C.
1. Em **Políticas**, selecione **Identity Experience Framework** e **Chaves de política**.
1. Selecione **Adicionar** e depois **Opções** > **Carregar**.
1. Insira um **Nome**, por exemplo *SamlIdpCert*. O prefixo *B2C_1A_* será adicionado automaticamente ao nome da chave.
1. Carregue seu certificado usando carregar o controle de arquivo.
1. Insira a senha do certificado.
1. Selecione **Criar**.
1. Verifique se a chave aparece como esperado. Por exemplo, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Preparar sua política

### <a name="21-create-the-saml-token-issuer"></a>2.1 Criar o emissor do token SAML

Agora adicione a funcionalidade para o seu locatário emitir tokens SAML usando os perfis técnicos [emissor de token SAML](saml-issuer-technical-profile.md) e [provedor de sessão SAML](custom-policy-reference-sso.md#samlssosessionprovider).

Abra `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** no pacote inicial de política personalizada.

Localize a seção `<ClaimsProviders>` e adicione o snippet XML a seguir.

Você pode alterar o valor dos metadados `IssuerUri`. Esse é o URI do emissor retornado na resposta SAML do Azure AD B2C. Seu aplicativo de terceira parte confiável deve ser configurado para aceitar um URI de emissor durante a validação de declaração SAML.

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
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

## <a name="3-add-the-saml-relying-party-policy"></a>3. Adicionar a política de terceira parte confiável SAML

Agora que seu locatário pode emitir declarações SAML, você precisa criar a política de terceira parte confiável SAML e modificar o percurso do usuário para que ela emita uma declaração SAML em vez de um JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Criar uma política de inscrição ou entrada

1. Crie uma cópia do arquivo *SignUpOrSignin.xml* no diretório de trabalho do pacote inicial e salve-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml*. Este é o arquivo de política de terceira parte confiável.

1. Abra o arquivo *SignUpOrSigninSAML.xml* no editor de sua preferência.

1. Altere `PolicyId` e `PublicPolicyUri` da política para _B2C_1A_signup_signin_saml_ e `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` conforme visto abaixo.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Adicione o snippet XML a seguir logo antes do elemento `<RelyingParty>`. Esse XML substitui a etapa de orquestração número 7 do percurso do usuário _SignUpOrSignIn_. Se você começou em uma pasta diferente no pacote inicial ou personalizou seu percurso do usuário adicionando ou removendo etapas de orquestração, verifique se o número (no elemento `order`) está alinhado com o especificado no percurso do usuário para etapa de emissor de token (por exemplo, nas outras pastas do pacote inicial, é a etapa número 4 para `LocalAccounts`, 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa`).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Substitua todo o elemento `<TechnicalProfile>` no elemento `<RelyingParty>` pelo XML de perfil técnico a seguir.

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

1. Atualize `tenant-name` com o nome do seu locatário do Azure AD B2C.

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
> Ao implementar outros tipos de fluxos de usuário (por exemplo, entrada, redefinição de senha ou edição de perfil), o processo é essencialmente o mesmo descrito nesta seção. Na etapa 4 acima, você alterará a última etapa do percurso do usuário de `JWTIssuer` para `Saml2AssertionIssuer` . E na etapa 6 acima, na seção terceira parte confiável, você alterará o **protocolo** de `OpenIdConnect` para `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Carregar e testar seus metadados de política

Salve as alterações e carregue o novo arquivo de política. Depois de carregar ambas as políticas (a extensão e os arquivos de terceira parte confiável), abra um navegador da Web e navegue até os metadados de política.

Os metadados do IDP de política do Azure AD B2C são informações usadas no protocolo SAML para expor a configuração de um provedor de identidade SAML. Metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. Os metadados de política do Azure AD B2C estão disponíveis na URL a seguir. Substitua `tenant-name` pelo nome do seu locatário do Azure ad B2C e `policy-name` pelo nome (ID) da política, por exemplo,.../B2C_1A_signup_signin_saml/samlp/Metadata:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Sua política personalizada e o locatário do Azure AD B2C agora estão prontos. Em seguida, crie um registro de aplicativo no Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Aplicativo de instalação no diretório do Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 registrar seu aplicativo no Azure AD B2C

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *SAMLApp1*.
1. Em **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**
1. Em **URI de Redirecionamento**, selecione **Web** e, em seguida, digite `https://localhost`. Você modificará esse valor posteriormente no manifesto do registro de aplicativo.
1. Selecione **Registrar**.

### <a name="42-update-the-app-manifest"></a>4.2 Atualizar o manifesto do aplicativo

Para aplicativos SAML, há várias propriedades que você precisa configurar no manifesto do registro de aplicativo.

1. No [portal do Azure](https://portal.azure.com), navegue até o registro de aplicativo criado na seção anterior.
1. Em **Gerenciar**, selecione **Manifesto** para abrir o editor de manifesto. Você modifica várias propriedades nas seções a seguir.

#### <a name="identifieruris"></a>identifierUris

O `identifierUris` é uma coleção de cadeia de caracteres que contém os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web dentro de seu locatário do Azure AD B2C. O URI deve corresponder ao nome da solicitação SAML `Issuer` . O URI definido pelo usuário normalmente é o mesmo valor que os metadados do provedor de serviços `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Essa propriedade representa a URL de metadados publicamente disponível do provedor de serviços. A URL de metadados pode apontar para um arquivo de metadados carregado para qualquer ponto de extremidade acessível anonimamente, por exemplo, armazenamento de blobs.

Os metadados são informações usadas no protocolo SAML para expor a configuração de uma entidade SAML, como um provedor de serviços. Os metadados definem o local dos serviços como entrada e saída, certificados, método de entrada e muito mais. O Azure AD B2C lê os metadados do provedor de serviços e age de acordo. Os metadados não são necessários. Você também pode especificar alguns atributos como o URI de resposta e o URI de saída diretamente no manifesto do aplicativo.

Se houver propriedades especificadas *tanto* na URL de metadados SAML quanto no manifesto do registro de aplicativo, elas serão **mescladas**. As propriedades especificadas na URL de metadados são processadas primeiro e têm precedência.

Para este tutorial, que usa o aplicativo de teste SAML, use o seguinte valor para `samlMetadataUrl`:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (opcional)

Se você não fornecer um URI de metadados, poderá especificar explicitamente a URL de resposta. Essa propriedade opcional representa o `AssertionConsumerServiceUrl` (URL `SingleSignOnService` nos metadados do provedor de serviços) e supõe-se que `BindingType` seja `HTTP POST`.

Se você optar por configurar a URL de resposta e a URL de logoff no manifesto do aplicativo sem usar os metadados do provedor de serviços, o Azure AD B2C não validará a assinatura de solicitação SAML nem criptografará a resposta SAML.

Para este tutorial, no qual você usa o aplicativo de teste SAML, defina a propriedade `url` de `replyUrlsWithType` como o valor mostrado no snippet JSON a seguir.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (opcional)

Essa propriedade opcional representa a URL `Logout` (URL `SingleLogoutService` nos metadados de terceira parte confiável) e supõe-se que `BindingType` para isso seja `Http-Redirect`.

Para este tutorial, que usa o aplicativo de teste SAML, deixe `logoutUrl` definido como `https://samltestapp2.azurewebsites.net/logout`:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Atualizar o código do aplicativo

A última etapa é habilitar o Azure AD B2C como uma IdP SAML em seu aplicativo de terceira parte confiável SAML. Cada aplicativo é diferente e as etapas para fazer isso variam. Consulte a documentação do seu aplicativo para obter detalhes.

Os metadados podem ser configurados em seu provedor de serviços como "metadados estáticos" ou "metadados dinâmicos". No modo estático, você copia todos ou parte dos metadados dos metadados da política de Azure AD B2C. No modo dinâmico, você define a URL para os metadados e permite que nosso aplicativo Leia os metadados dinamicamente.

Alguns ou todos os seguintes itens são normalmente necessários:

* **Metadados**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emissor**: o valor da solicitação SAML `issuer` deve corresponder a um dos URIs configurados no `identifierUris` elemento do manifesto de registro do aplicativo. Se o nome da solicitação SAML `issuer` não existir no `identifierUris` elemento, [adicione-o ao manifesto de registro do aplicativo](#identifieruris). Por exemplo, `https://contoso.onmicrosoft.com/app-name`. 
* **URL de logon/ponto de extremidade SAML/URL SAML**: Verifique o valor no arquivo de metadados de política SAML Azure ad B2C para o `<SingleSignOnService>` elemento XML
* **Certificado**: esse é *B2C_1A_SamlIdpCert*, mas sem a chave privada. Para obter a chave pública do certificado:

    1. acesse a URL de metadados especificada acima.
    1. Copie o valor no elemento `<X509Certificate>`.
    1. Cole-o em um arquivo de texto.
    1. Salve o arquivo de texto como um arquivo *.cer*.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Testar com o aplicativo de teste SAML (opcional)

Para concluir este tutorial usando nosso [Aplicativo de teste SAML][samltest]:

* Atualizar o nome do locatário
* Atualizar o nome da política, por exemplo *B2C_1A_signup_signin_saml*
* Especifique esse URI do emissor. Use um dos URIs encontrados no `identifierUris` elemento no manifesto de registro do aplicativo, por exemplo `https://contoso.onmicrosoft.com/app-name` .

Selecione **Logon** e você deverá receber uma tela de entrada do usuário. Após a entrada, uma declaração SAML é emitida de volta para o aplicativo de exemplo.

## <a name="enable-encrypted-assertions-optional"></a>Habilitar asserções criptografadas (opcional)

Para criptografar as asserções SAML enviadas de volta ao provedor de serviços, Azure AD B2C usará o certificado de chave pública de provedores de serviço. A chave pública deve existir nos metadados SAML descritos no ["samlMetadataUrl"](#samlmetadataurl) acima como um descritor de chaves com o uso de "Encryption".

O código XML a seguir é um exemplo do descritor de keymetadata de metadados SAML com um uso definido para criptografia:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Para permitir que Azure AD B2C enviem asserções criptografadas, defina o item de metadados **WantsEncryptedAssertion** como `true` no [perfil técnico de terceira parte confiável](relyingparty.md#technicalprofile). Você também pode configurar o algoritmo usado para criptografar a Asserção SAML. Para obter mais informações, consulte [metadados de perfil técnico de terceira parte confiável](relyingparty.md#metadata). 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Habilitar o fluxo iniciado pelo provedor de identidade (opcional)

No fluxo iniciado pelo provedor de identidade, o processo de entrada é iniciado pelo provedor de identidade (Azure AD B2C), que envia uma resposta SAML não solicitada para o provedor de serviços (seu aplicativo de terceira parte confiável). Atualmente, não há suporte para cenários em que o provedor de identidade inicial é um provedor de identidade externo, por exemplo, [AD-FS](identity-provider-adfs.md)ou [Salesforce](identity-provider-salesforce-saml.md).

Para habilitar o fluxo iniciado do provedor de identidade (Azure AD B2C), defina o item de metadados **IdpInitiatedProfileEnabled** como `true` no [perfil técnico de terceira parte confiável](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Para entrar ou inscrever um usuário por meio do fluxo iniciado pelo provedor de identidade, use a seguinte URL:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Substitua os seguintes valores:

* **nome do locatário** com o nome do locatário
* **nome da política** com o nome da política de terceira parte confiável SAML
* **app-Identifier-URI** com o `identifierUris` no arquivo de metadados, como `https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política de exemplo completa que você pode usar para testar com o Aplicativo Teste SAML.

1. Baixar a [política de exemplo de logon iniciada pelo SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Atualizar `TenantId` para corresponder ao nome do locatário, por exemplo *contoso.b2clogin.com*
1. Manter o nome da política de *B2C_1A_signup_signin_saml*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML com e sem suporte

Há suporte aos seguintes cenários de RP (terceira parte confiável) SAML por meio do seu próprio ponto de extremidade de metadados:

* Várias URLs de logoff ou a associação POST para a URL de logoff no objeto de aplicativo/entidade de serviço.
* Especifique a chave de assinatura para verificar solicitações de RP no objeto de aplicativo/entidade de serviço.
* Especifique a chave de criptografia de token no objeto de aplicativo/entidade de serviço.
* O provedor de identidade iniciou o logon, onde o provedor de identidade está Azure AD B2C.

## <a name="saml-token"></a>token de SAML

Um token SAML é um token de segurança que é emitido pelo Azure AD B2C após uma entrada bem-sucedida. Ele contém informações sobre o usuário, o provedor de serviços para o qual o token é pretendido, assinatura e tempo de validade. A tabela a seguir lista as declarações e as propriedades que você pode esperar em um token SAML emitido por Azure AD B2C.

|Elemento  |Propriedade  |Observações  |
|---------|---------|---------|
|`<Response>`| `ID` | Um identificador exclusivo gerado automaticamente da resposta. | 
|`<Response>`| `InResponseTo` | A ID da solicitação SAML à qual essa mensagem está em resposta. | 
|`<Response>` | `IssueInstant` | O tempo de problemas da resposta. O valor de hora é codificado em UTC.  Para alterar as configurações nos tempos de vida do token, defina os `TokenNotBeforeSkewInSeconds` [metadados](saml-issuer-technical-profile.md#metadata) do perfil técnico do emissor do token SAML. | 
|`<Response>` | `Destination`| Uma referência de URI que indica o endereço para o qual essa resposta foi enviada. O valor é idêntico à solicitação SAML `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Identifica o emissor do token. Este é um URI arbitrário definido pelos metadados do problema do token `IssuerUri` [](saml-issuer-technical-profile.md#metadata) SAML     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |A entidade de segurança sobre a qual o token declara informações, como a ID de objeto de usuário. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | Uma referência de URI que representa a classificação de informações de identificador com base em cadeia de caracteres. Por padrão, essa propriedade é omitida. Você pode definir o [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) da terceira parte confiável para especificar o `NameID` formato, como `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |A hora na qual o token se torna válido. O valor de hora é codificado em UTC. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. Para alterar as configurações em seus tempos de vida de token, defina os `TokenNotBeforeSkewInSeconds` [metadados](saml-issuer-technical-profile.md#metadata) do perfil técnico de emissão de token SAML. |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | A hora em que o token se torna inválido. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. O valor padrão é 5 minutos após o `NotBefore` e pode ser atualizado adicionando os `TokenLifeTimeInSeconds` [metadados](saml-issuer-technical-profile.md#metadata) do perfil técnico de emissão de token SAML.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Uma referência de URI que identifica um público-alvo. Ele identifica o destinatário pretendido do token. O valor é idêntico à solicitação SAML `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` coleção de`<Attribute>` | | A coleção de asserções (declarações), conforme configurada nas declarações de saída do [perfil técnico de terceira parte confiável](relyingparty.md#technicalprofile) . Você pode configurar o nome da asserção definindo o `PartnerClaimType` da declaração de saída. |

## <a name="next-steps"></a>Próximas etapas

- Você pode encontrar mais informações sobre o [protocolo SAML no site da OASIS](https://www.oasis-open.org/).
- Obtenha o aplicativo Web de teste SAML do [repositório da comunidade GitHub do Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
