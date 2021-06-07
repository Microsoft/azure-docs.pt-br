---
title: Definir entrada com opções do provedor de identidade SAML
titleSuffix: Azure Active Directory B2C
description: Configure as opções do IdP (provedor de identidade SAML) de entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 32f9df410dabf1902e9a7d9aadbf47288bfa90f5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798231"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Configurar opções do provedor de identidade SAML com Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) dá suporte à Federação com provedores de identidade SAML 2,0. Este artigo descreve as opções de configuração que estão disponíveis ao habilitar a entrada com um provedor de identidade SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mapeamento de declarações

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade SAML. Você precisa mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Verifique seu provedor de identidade para obter a lista de declarações (asserções). Você também pode verificar o conteúdo da resposta SAML que seu provedor de identidade retorna. Para obter mais informações, consulte [depurar as mensagens SAML](#debug-saml-protocol). Para adicionar uma declaração, primeiro [defina uma declaração](claimsschema.md)e, em seguida, adicione a declaração à coleção de declarações de saída.

Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que defina o atributo `DefaultValue`. O valor padrão pode ser estático ou dinâmico, usando [declarações de contexto](#enable-use-of-context-claims).

O elemento de declaração de saída contém os seguintes atributos:

- **ClaimTypeReferenceId** é a referência a um tipo de declaração. 
- **PartnerClaimType** é o nome da propriedade que aparece Asserção SAML. 
- **DefaultValue** é um valor padrão predefinido. Se a declaração estiver vazia, o valor padrão será usado. Você também pode usar um [resolvedor de declaração](claim-resolver-overview.md) com um valor contextual, como a ID de correlação ou o endereço IP do usuário.

### <a name="subject-name"></a>Nome da entidade

Para ler o **nome** da declaração SAML na **entidade** como uma declaração normalizada, defina a Declaração **PartnerClaimType** como o valor do `SPNameQualifier` atributo. Se o `SPNameQualifier` atributo não for apresentado, defina o **PartnerClaimType** de declaração como o valor do `NameQualifier` atributo.

Asserção SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Declaração de saída:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Se ambos `SPNameQualifier` os `NameQualifier` atributos ou não forem apresentados na Asserção SAML, defina a Declaração **PartnerClaimType** como `assertionSubjectName` . Verifique se o **NameId** é o primeiro valor na declaração XML. Quando definir mais de uma asserção, o Azure AD B2C escolherá o valor do assunto da última declaração.


## <a name="configure-saml-protocol-bindings"></a>Configurar associações de protocolo SAML

As solicitações SAML são enviadas para o provedor de identidade conforme especificado no elemento de metadados do provedor de identidade `SingleSignOnService` . A maioria das solicitações de autorização dos provedores de identidade são transportadas diretamente na cadeia de caracteres de consulta de URL de uma solicitação HTTP GET (pois as mensagens são relativamente curtas). Consulte a documentação do provedor de identidade para saber como configurar as associações para as duas solicitações SAML.

Veja a seguir um exemplo de um serviço de logon único de metadados do Azure AD com duas associações. O `HTTP-Redirect` tem precedência sobre o `HTTP-POST` porque ele aparece primeiro nos metadados do provedor de identidade SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Serviço de consumidor de asserção

O serviço de consumidor de asserção (ou ACS) é onde as respostas SAML do provedor de identidade podem ser enviadas e recebidas por Azure AD B2C. As respostas SAML são transmitidas para Azure AD B2C via associação HTTP POST. O local do ACS aponta para a política base de sua terceira parte confiável. Por exemplo, se a política de confiança for *B2C_1A_signup_signin*, o ACS será a política de base do *B2C_1A_signup_signin*, como *B2C_1A_TrustFrameworkBase*.

Veja a seguir um exemplo de um elemento de serviço de consumidor de declaração de metadados de política de Azure AD B2C. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Configurar a assinatura de solicitação SAML

Azure AD B2C assina todas as solicitações de autenticação de saída usando a chave de criptografia **SamlMessageSigning** . Para desabilitar a assinatura de solicitação SAML, defina **WantsSignedRequests** como `false` . Se os metadados forem definidos como `false` , os parâmetros de **SigAlg** e de **assinatura** (cadeia de caracteres de consulta ou parâmetro de postagem) serão omitidos da solicitação.

Esses metadados também controlam o atributo **AuthnRequestsSigned** , que está incluído com os metadados do perfil técnico de Azure ad B2C que é compartilhado com o provedor de identidade. Azure AD B2C não assinará a solicitação se o valor de **WantsSignedRequests** nos metadados do perfil técnico estiver definido como `false` e o **WantAuthnRequestsSigned** de metadados do provedor de identidade for definido como `false` ou não especificado.

O exemplo a seguir remove a assinatura da solicitação SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algoritmo de assinatura

O Azure AD B2C usa `Sha1` para assinar a solicitação SAML. Use os metadados **XmlSignatureAlgorithm** para configurar o algoritmo a ser usado. Os valores possíveis são `Sha256` ,, `Sha384` `Sha512` ou `Sha1` (padrão). Esse metadado controla o valor do parâmetro **SigAlg** (cadeia de caracteres de consulta ou parâmetro de postagem) na solicitação SAML. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo com suporte do seu certificado.

### <a name="include-key-info"></a>Incluir informações da chave

Quando o provedor de identidade indica que Azure AD B2C associação é definida como `HTTP-POST` , Azure ad B2C inclui a assinatura e o algoritmo no corpo da solicitação SAML. Você também pode configurar o Azure AD para incluir a chave pública do certificado quando a associação for definida como `HTTP-POST` . Use os metadados **IncludeKeyInfo** para `true` , ou `false` . No exemplo a seguir, o Azure AD não incluirá a chave pública do certificado.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Configurar ID do nome da solicitação SAML

O elemento de solicitação de autorização SAML `<NameID>` indica o formato do identificador de nome SAML. Esta seção descreve a configuração padrão e como personalizar o elemento Name ID.

## <a name="preferred-username"></a>Nome de usuário preferencial

Durante um percurso do usuário de entrada, um aplicativo de terceira parte confiável pode ter como alvo um usuário específico. Azure AD B2C permite que você envie um nome de usuário preferencial para o provedor de identidade SAML. O elemento **InputClaims** é usado para enviar um **NameID** dentro do **assunto** da solicitação de autorização SAML.

Para incluir a ID de nome da entidade dentro da solicitação de autorização, adicione o seguinte `<InputClaims>` elemento imediatamente após o `<CryptographicKeys>` . O **PartnerClaimType** deve ser definido como `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

Neste exemplo, Azure AD B2C envia o valor da Declaração **signInName** com **NameID** dentro do **assunto** da solicitação de autorização SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Você pode usar [declarações de contexto](claim-resolver-overview.md), como `{OIDC:LoginHint}` para preencher o valor da declaração.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Formato de política de ID de nome

Por padrão, a solicitação de autorização SAML especifica a `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` política. Isso indica que qualquer tipo de identificador suportado pelo provedor de identidade para o assunto solicitado pode ser usado.

Para alterar esse comportamento, consulte a documentação do provedor de identidade para obter orientação sobre quais políticas de ID de nome têm suporte. Em seguida, adicione os `NameIdPolicyFormat` metadados no formato de política correspondente. Por exemplo:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

A solicitação de autorização SAML a seguir contém a política de ID de nome.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Permitir a criação de novas contas

Se você especificar o [formato de política de ID de nome](#name-id-policy-format), também poderá especificar a `AllowCreate` propriedade de **NameIDPolicy** para indicar se o provedor de identidade tem permissão para criar uma nova conta durante o fluxo de entrada. Consulte a documentação do provedor de identidade para obter diretrizes.

Azure AD B2C omite a `AllowCreate` Propriedade por padrão. Você pode alterar esse comportamento usando os `NameIdPolicyAllowCreate` metadados. O valor desses metadados é `true` ou `false` .

O exemplo a seguir demonstra como definir `AllowCreate` a propriedade de `NameIDPolicy` como `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


O exemplo a seguir demonstra uma solicitação de autorização com **AllowCreate** do elemento **NameIDPolicy** na solicitação de autorização.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Incluir referências de classe de contexto de autenticação

Uma solicitação de autorização SAML pode conter um elemento **AuthnContext** , que especifica o contexto de uma solicitação de autorização. O elemento pode conter uma referência de classe de contexto de autenticação, que informa ao provedor de identidade SAML qual mecanismo de autenticação deve apresentar ao usuário.

Para configurar as referências de classe de contexto de autenticação, adicione **IncludeAuthnContextClassReferences** Metadata. No valor, especifique uma ou mais referências de URI identificando as classes de contexto de autenticação. Especifique vários URIs como uma lista delimitada por vírgula. Consulte a documentação do provedor de identidade para obter orientação sobre os URIs do **AuthnContextClassRef** com suporte.

O exemplo a seguir permite que os usuários entrem com nome de usuário e senha e entrem com nome de usuário e senha em uma sessão protegida (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

A solicitação de autorização SAML a seguir contém as referências de classe de contexto de autenticação.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Incluir dados personalizados na solicitação de autorização

Opcionalmente, você pode incluir elementos de extensão de mensagem de protocolo que são acordados pelo Azure AD BC e seu provedor de identidade. A extensão é apresentada no formato XML. Você inclui elementos de extensão adicionando dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` . Verifique a documentação do provedor de identidade para ver se o elemento de extensões é suportado.

O exemplo a seguir ilustra o uso de dados de extensão:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Ao usar a extensão de mensagem do protocolo SAML, a resposta SAML será parecida com o exemplo a seguir:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Exigir respostas SAML assinadas

Azure AD B2C requer que todas as asserções de entrada sejam assinadas. Você pode remover esse requisito definindo o **WantsSignedAssertions** como `false` . O provedor de identidade não deve assinar as declarações nesse caso, mas mesmo que ela tenha, Azure AD B2C não validará a assinatura.

Os metadados **WantsSignedAssertions** controlam o sinalizador de metadados SAML **WantAssertionsSigned**, que está incluído nos metadados do perfil técnico de Azure ad B2C que é compartilhado com o provedor de identidade.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Se você desabilitar a validação de asserções, talvez também queira desabilitar a validação da assinatura da mensagem de resposta. Defina os metadados de **ResponsesSigned** como `false` . O provedor de identidade não deve assinar a mensagem de resposta SAML nesse caso, mas mesmo que ela tenha, Azure AD B2C não validará a assinatura.

O exemplo a seguir remove a mensagem e a assinatura de asserção:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Exigir respostas SAML criptografadas

Para exigir que todas as asserções de entrada sejam criptografadas, defina os metadados de **WantsEncryptedAssertions** . Quando a criptografia é necessária, o provedor de identidade usa uma chave pública de um certificado de criptografia em um perfil técnico de Azure AD B2C. Azure AD B2C descriptografa a declaração de resposta usando a parte privada do certificado de criptografia.

Se você habilitar a criptografia de asserções, talvez também precise desabilitar a validação da assinatura de resposta (para obter mais informações, consulte [exigir respostas de SAML assinadas](#require-signed-saml-responses).

Quando os metadados de **WantsEncryptedAssertions** são definidos como `true` , os metadados do perfil técnico de Azure ad B2C inclui a seção **criptografia** . O provedor de identidade lê os metadados e criptografa a declaração de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico do Azure AD B2C.

O exemplo a seguir mostra a seção descritor de chave dos metadados SAML usados para criptografia:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Para criptografar a declaração de resposta SAML:

1. [Crie uma chave de política](identity-provider-generic-saml.md#create-a-policy-key) com um identificador exclusivo. Por exemplo, `B2C_1A_SAMLEncryptionCert`.
2. Em sua coleção de **CryptographicKeys** de perfil técnico do SAML. Defina **StorageReferenceId** como o nome da chave de política que você criou na primeira etapa. A `SamlAssertionDecryption` ID indica o uso da chave de criptografia para criptografar e descriptografar a asserção da resposta SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Defina os metadados do perfil técnico **WantsEncryptedAssertions** para `true`.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Atualize seu provedor de identidade com os novos metadados de perfil técnico de Azure AD B2C. Você deve ver **KeyDescriptor** com a propriedade **use** definida como `encryption` contendo a chave pública do certificado.

## <a name="enable-use-of-context-claims"></a>Habilitar o uso de declarações de contexto

Na coleção de declarações de entrada e saída, você pode incluir declarações que não são retornadas pelo provedor de identidade, desde que você defina o `DefaultValue` atributo. Você também pode usar [declarações de contexto](claim-resolver-overview.md) a serem incluídas no perfil técnico. Para usar uma declaração de contexto:

1. Adicione um tipo de declaração ao elemento [ClaimsSchema](claimsschema.md) dentro de [BuildingBlocks](buildingblocks.md).
2. Adicione uma declaração de saída à coleção de entrada ou de saída. No exemplo a seguir, a primeira declaração define o valor do provedor de identidade. A segunda declaração usa as [declarações de contexto](claim-resolver-overview.md)de endereço IP do usuário.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Desabilitar logoff único

Após uma solicitação de logout do aplicativo, Azure AD B2C tenta sair do seu provedor de identidade SAML. Para obter mais informações, consulte [Azure ad B2C sessão](session-behavior.md#sign-out)sair.  Para desabilitar o comportamento de logoff único, defina os metadados de **SingleLogoutEnabled** como `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Depurar protocolo SAML

Para ajudar a configurar e depurar a Federação com um provedor de identidade SAML, você pode usar uma extensão de navegador para o protocolo SAML, como a [extensão devtools SAML](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para Chrome, o [rastreador SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para Firefox [ou as ferramentas de desenvolvedor do Edge ou do IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Usando essas ferramentas, você pode verificar a integração entre Azure AD B2C e seu provedor de identidade SAML. Por exemplo:

* Verifique se a solicitação SAML contém uma assinatura e determine qual algoritmo é usado para entrar na solicitação de autorização.
* Obtenha as declarações (asserções) na `AttributeStatement` seção.
* Verifique se o provedor de identidade retorna uma mensagem de erro.
* Verifique se a seção de asserção está criptografada.

## <a name="next-steps"></a>Próximas etapas

- Saiba como diagnosticar problemas com suas políticas personalizadas usando [Application insights](troubleshoot-with-application-insights.md). 

::: zone-end
