---
title: Definir um perfil técnico SAML em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico SAML em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f16de49518e334f2f5e679ce24e24a262a1e231
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674936"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do provedor de identidade SAML em uma política personalizada Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade SAML 2,0. Este artigo descreve as especificações de um perfil técnico para interagir com um provedor de declarações compatível com esse protocolo padronizado. Com um perfil de técnico SAML, pode-se federar com um provedor de identidade baseado em SAML,como o [ADFS](./identity-provider-adfs.md) e o [Salesforce](identity-provider-salesforce-saml.md). Essa federação permite que usuários entrem com suas redes sociais existentes ou identidades corporativas.

## <a name="metadata-exchange"></a>Troca de metadados

Metadados são informações usadas no protocolo SAML para expor a configuração de uma entidade SAML, como um provedor de serviços ou o provedor de identidade. Metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. O provedor de identidade usa os metadados para saber como se comunicar com o Azure AD B2C. Os metadados são configurados em formato XML e podem ser assinados com uma assinatura digital para que a outra parte possa validar a integridade dos metadados. Quando o Azure AD B2C federa com um provedor de identidade SAML, ele atua como provedor de serviços iniciando uma solicitação SAML e aguardando uma resposta SAML. E, em alguns casos, aceita autenticação SAML não solicitada, que também é conhecida como provedor de identidade iniciado.

Os metadados podem ser configurados em ambas as partes como "Metadados estáticos" ou "Metadados dinâmicos". No modo estático, você copia os metadados completos de uma parte e define-os em outra parte. No modo dinâmico, você define a URL para os metadados enquanto a outra parte lê a configuração dinamicamente. Os princípios são os mesmos, você define os metadados do perfil técnico do Azure AD B2C no seu provedor de identidade e define os metadados do provedor de identidade no Azure AD B2C.

Cada provedor de identidade SAML tem diferentes etapas para expor e definir o provedor de serviços, neste caso, o Azure AD B2C, e definir os metadados do Azure AD B2C no provedor de identidade. Confira a documentação do provedor de identidade para obter orientação sobre como fazer isso.

O exemplo a seguir mostra um endereço de URL para os metadados do SAML de um perfil técnico do Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **nome do locatário seu** com o nome do locatário, como fabrikam.b2clogin.com.
- **your-policy** pelo nome da política. Use a política em que você configura o perfil técnico do provedor SAML ou uma política que herda dessa política.
- **seu perfil técnico** com seu nome de perfil técnico do provedor de identidade SAML.

## <a name="digital-signing-certificates-exchange"></a>Troca de certificados de autenticação digital

Para criar uma relação de confiança entre o Azure AD B2C e o seu provedor de identidade SAML, você precisa fornecer um certificado X509 válido com a chave privada. Faça upload do certificado com a chave privada (arquivo .pfx) para o repositório de chaves de política do Azure AD B2C. O Azure AD B2C assina digitalmente a solicitação de entrada SAML usando o certificado que você fornecer.

O certificado é usado dos seguintes modos:

- O Azure AD B2C gera e assina uma solicitação SAML, usando a chave privada do Azure AD B2C do certificado. A solicitação SAML é enviada ao provedor de identidade, que valida a solicitação usando a chave pública do Azure AD B2C do certificado. O certificado público do Azure AD B2C é acessível por meio de metadados do perfil técnico. Como alternativa, você pode fazer upload manualmente do arquivo .cer para seu provedor de identidade SAML.
- O provedor de identidade assina os dados enviados para o Azure AD B2C usando a chave privada do provedor de identidade do certificado. O Azure AD B2C valida os dados usando o certificado público do provedor de identidade. Cada provedor de identidade tem diferentes etapas para a configuração. Confira a documentação dos seu provedor de identidade para obter orientação sobre como fazer isso. No Azure AD B2C, sua política precisa acessar a chave pública do certificado usando metadados do provedor de identidade.

Um certificado autoassinado é aceitável para a maioria dos cenários. Para ambientes de produção, é recomendável usar um certificado X509 emitido por uma autoridade de certificação. Além disso, conforme descrito posteriormente neste documento, para um ambiente de não produção é possível desabilitar a assinatura do SAML em ambos os lados.

O diagrama a seguir mostra a troca de metadados e do certificado:

![troca de metadados e do certificado](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Criptografia digital

Para criptografar a declaração de resposta SAML, o provedor de identidade sempre usa uma chave pública de um certificado de criptografia em um perfil técnico do Azure AD B2C. Quando o Azure AD B2C precisa descriptografar os dados, ele usa a parte privada do certificado de criptografia.

Para criptografar a declaração de resposta SAML:

1. Faça upload do certificado X509 válido com a chave privada (arquivo .pfx) para o repositório de chaves de política do Azure AD B2C.
2. Adicione um elemento **CryptographicKey** com um identificador de `SamlAssertionDecryption` à coleção **CryptographicKeys** do perfil técnico. Defina **StorageReferenceId** para o nome da chave de política que você criou na etapa 1.
3. Defina os metadados do perfil técnico **WantsEncryptedAssertions** para `true`.
4. Atualize o provedor de identidade com os novos metadados de perfil técnico do Azure AD B2C. Você deve ver **KeyDescriptor** com a propriedade **use** definida como `encryption` contendo a chave pública do certificado.

O exemplo a seguir mostra a seção descritor de chave dos metadados SAML usados para criptografia:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento Protocol precisa ser definido como `SAML2`.

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** é usado para enviar um **NameID** dentro do **assunto** da solicitação do SAML Authn. Para fazer isso, adicione uma declaração de entrada com um **PartnerClaimType** definido `subject` como conforme mostrado abaixo.

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade SAML na seção `AttributeStatement`. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que você defina o atributo `DefaultValue`.

### <a name="subject-name-output-claim"></a>Declaração de saída do nome da entidade

Para ler o **nome** da declaração SAML na **entidade** como uma declaração normalizada, defina o **PartnerClaimType** de declaração como o valor do `SPNameQualifier` atributo. Se o `SPNameQualifier` atributo não for apresentado, defina o **PartnerClaimType** de declaração como o valor do `NameQualifier` atributo. 


Asserção SAML: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Declaração de saída:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Se ambos `SPNameQualifier` os `NameQualifier` atributos ou não forem apresentados na Asserção SAML, defina a Declaração **PartnerClaimType** como `assertionSubjectName` . Verifique se o **NameId** é o primeiro valor na declaração XML. Quando definir mais de uma asserção, o Azure AD B2C escolherá o valor do assunto da última declaração.

O exemplo a seguir mostra as declarações retornadas por um provedor de identidade SAML:

- A Declaração **issuerUserId** é mapeada para a Declaração **assertionSubjectName** .
- A declaração **first_name** é mapeada para a declaração **givenName**.
- A declaração **last_name** é mapeada para a declaração **surname**.
- A Declaração **DisplayName** é mapeada para a declaração de **nome** .
- A declaração **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| PartnerEntity | Sim | URL dos metadados do provedor de identidade SAML. Copie os metadados do provedor de identidade e adicione-o dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Não | Indica se o perfil técnico requer que todas as solicitações de autenticação de saída sejam assinadas. Valores possíveis: `true` ou `false`. O valor padrão é `true`. Quando o valor é definido como `true`, a chave de criptografia **SamlMessageSigning** deve ser especificada e todas as solicitações de autenticação de saída devem estar assinadas. Se o valor for definido como `false`, os parâmetros **SigAlg** e **Signature** (cadeia de caracteres de consulta ou parâmetro de postagem) serão omitidos da solicitação. Esses metadados também controlam o atributo **AuthnRequestsSigned** dos metadados, que é a saída nos metadados do perfil técnico do Azure AD B2C que é compartilhado com o provedor de identidade. Azure AD B2C não assinará a solicitação se o valor de **WantsSignedRequests** nos metadados do perfil técnico estiver definido como `false` e o **WantAuthnRequestsSigned** de metadados do provedor de identidade for definido como `false` ou não especificado. |
| XmlSignatureAlgorithm | Não | O método que o Azure AD B2C usa para assinar a solicitação SAML. Esse metadado controla o valor do parâmetro **SigAlg** (cadeia de caracteres de consulta ou parâmetro de postagem) na solicitação SAML. Valores possíveis: `Sha256` , `Sha384` , `Sha512` ou `Sha1` (padrão). Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo com suporte do seu certificado. |
| WantsSignedAssertions | Não | Indica se o perfil técnico exige que todas as declarações de entrada estejam assinadas. Valores possíveis: `true` ou `false`. O valor padrão é `true`. Se o valor for definido como `true`, todas as declarações da seção `saml:Assertion` enviadas pelo provedor de identidade para o Azure AD B2C deverão estar assinadas. Se o valor for definido como `false`, o provedor de identidade não deverá assinar as declarações, mas, mesmo se isso acontecer, o Azure AD B2C não validará a assinatura. Esses metadados também controlam o **WantsAssertionsSigned** do sinalizador de metadados, que é a saída nos metadados do perfil técnico do Azure AD B2C que é compartilhado com o provedor de identidade. Se você desabilitar a validação de declarações, também deverá desabilitar a validação de assinatura de resposta (para obter mais informações, confira **ResponsesSigned**). |
| ResponsesSigned | Não | Valores possíveis: `true` ou `false`. O valor padrão é `true`. Se o valor for definido como `false`, o provedor de identidade não deverá assinar a resposta SAML, mas, mesmo se isso acontecer, o Azure AD B2C não validará a assinatura. Se o valor for definido como `true`, a resposta SAML enviada pelo provedor de identidade para o Azure AD B2C será assinada e deverá ser validada. Se você desabilitar a validação da resposta SAML, também deverá desabilitar a validação de assinatura da declaração (para obter mais informações, confira **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Não | Indica se o perfil técnico exige que todas as declarações de entrada estejam criptografadas. Valores possíveis: `true` ou `false`. O valor padrão é `false`. Se o valor for definido como `true`, as declarações enviadas pelo provedor de identidade para o Azure AD B2C deverão ser assinadas e a chave de criptografia **SamlAssertionDecryption** deverá ser especificada. Se o valor for definido como `true`, os metadados do perfil técnico do Azure AD B2C incluirão a seção de **criptografia**. O provedor de identidade lê os metadados e criptografa a declaração de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico do Azure AD B2C. Se você habilitar a criptografia de declarações, também precisará desabilitar a validação de assinatura de resposta (para obter mais informações, confira **ResponsesSigned**). |
| NameIdPolicyFormat | Não | Especifica as restrições d o identificador de nome a ser usado para representar o assunto solicitado. Se omitido, qualquer tipo de identificador com suporte pelo provedor de identidade para o assunto solicitado pode ser usado. Por exemplo, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** pode ser usado com **NameIdPolicyAllowCreate**. Examine a documentação do provedor de identidade para obter orientação sobre qual nome há suporte para políticas de ID. |
| NameIdPolicyAllowCreate | Não | Ao usar **NameIdPolicyFormat**, também se pode especificar a `AllowCreate` propriedade do **NameIDPolicy**. O valor de metadados é `true` ou `false` para indicar se o provedor de identidade tem permissão para criar uma nova conta durante o fluxo de entrada. Confira a documentação do provedor de identidade para obter orientação sobre como fazer isso. |
| AuthenticationRequestExtensions | Não | Elementos de extensão de mensagem do protocolo opcional são acordados entre o Azure AD BC e o provedor de identidade. A extensão é apresentada no formato XML. Adicione os dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>`. Verifique a documentação do provedor de identidade para ver se o elemento de extensões é suportado. |
| IncludeAuthnContextClassReferences | Não | Especifique uma ou mais referências de URI que identifica as classes de contexto de autenticação. Por exemplo, para permitir que um usuário entre com o nome de usuário e senha apenas, defina o valor `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Para permitir a entrada com o nome de usuário e senha ao longo de uma sessão protegida (SSL/TLS), especifique `PasswordProtectedTransport`. Examine a documentação do provedor de identidade para obter orientação sobre os URIs **AuthnContextClassRef** suportados. Especifique vários URIs como uma lista delimitada por vírgula. |
| IncludeKeyInfo | Não | Indica se a solicitação de autenticação SAML contém a chave pública do certificado quando a associação é definida como `HTTP-POST`. Valores possíveis: `true` ou `false`. |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina como `true` . |
|SingleLogoutEnabled| Não| Indica se, durante a entrada, o perfil técnico tenta sair de provedores de identidade federada. Para obter mais informações, consulte [Azure ad B2C sessão](session-behavior.md#sign-out)sair.  Valores possíveis: `true` (padrão) ou `false` .|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar as mensagens de SAML. O Azure AD B2C usa essa chave para assinar as solicitações e enviá-las para o provedor de identidade. |
| SamlAssertionDecryption |Não | O certificado X509 (conjunto de chaves RSA). Um provedor de identidade SAML usa a parte pública do certificado para criptografar a asserção da resposta SAML. Azure AD B2C usa a parte privada do certificado para descriptografar a asserção. |
| MetadataSigning |Não | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar os metadados de SAML. O Azure AD B2C usa essa chave para assinar os metadados.  |

## <a name="saml-entityid-customization"></a>Personalização de EntityId SAML

Se você tiver vários aplicativos SAML que dependem de diferentes valores de EntityId, poderá substituir o `issueruri` valor em seu arquivo de terceira parte confiável. Para fazer isso, copie o perfil técnico com a ID "Saml2AssertionIssuer" do arquivo base e substitua o `issueruri` valor.

> [!TIP]
> Copie a `<ClaimsProviders>` seção da base e preserve esses elementos dentro do provedor de declarações: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` e `<DisplayName>Token Issuer</DisplayName>` .
 
Exemplo:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de como trabalhar com provedores de identidade SAML no Azure AD B2C:

- [Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas](identity-provider-adfs.md)
- [Entrar usando contas do Salesforce via SAML](identity-provider-salesforce-saml.md)