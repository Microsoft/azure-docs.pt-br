---
title: Configurar opções do provedor de serviços SAML
title-suffix: Azure Active Directory B2C
description: Como configurar Azure Active Directory B2C opções do provedor de serviços SAML
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: adfe5318949ffa624ebe3548944b558bd0dda9e1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198465"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opções para registrar um aplicativo SAML no Azure AD B2C

Este artigo descreve as opções de configuração que estão disponíveis ao conectar Azure Active Directory (Azure AD B2C) com o aplicativo SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Asserções SAML criptografadas

Quando seu aplicativo espera que as asserções SAML estejam em um formato criptografado, você precisa certificar-se de que a criptografia esteja habilitada na política de Azure AD B2C.

Azure AD B2C usa o certificado de chave pública do provedor de serviços para criptografar a Asserção SAML. A chave pública deve existir no ponto de extremidade de metadados do aplicativo SAML com o descritor de chave ' use ' definido como ' Encryption ', conforme mostrado no exemplo a seguir:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Para permitir que Azure AD B2C enviem asserções criptografadas, defina o item de metadados **WantsEncryptedAssertion** como `true` no [perfil técnico de terceira parte confiável](relyingparty.md#technicalprofile). Você também pode configurar o algoritmo usado para criptografar a Asserção SAML.

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

### <a name="encryption-method"></a>Método de criptografia

Para configurar o método de criptografia usado para criptografar os dados de Asserção SAML, defina a `DataEncryptionMethod` chave de metadados na terceira parte confiável. Os valores possíveis são `Aes256` (padrão),, `Aes192` `Sha512` ou `Aes128` . Os metadados controlam o valor do `<EncryptedData>` elemento na resposta SAML.

Para configurar o método de criptografia usado para criptografar a cópia da chave, que foi usada para criptografar os dados de Asserção SAML, defina a `KeyEncryptionMethod` chave de metadados na terceira parte confiável. Os valores possíveis são `Rsa15` (padrão)-algoritmo de criptografia do RSA Public Key Cryptography padrão (PKCS) versão 1,5 e `RsaOaep` -algoritmo CRIPTOGRÁFICO de OAEP (preenchimento de criptografia assimétrica ideal) da RSA.  Os metadados controlam o valor do  `<EncryptedKey>` elemento na resposta SAML.

O exemplo a seguir mostra a `EncryptedAssertion` seção de uma ASSERÇÃO SAML. O método de dados criptografados é `Aes128` , e o método de chave criptografada é `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Você pode alterar o formato das asserções criptografadas. Para configurar o formato de criptografia, defina a `UseDetachedKeys` chave de metadados na terceira parte confiável. Valores possíveis: `true` ou `false` (padrão). Quando o valor é definido como `true` , as chaves desanexadas adicionam a declaração criptografada como um filho do `EncrytedAssertion` em vez de `EncryptedData` .

Configure o método e o formato de criptografia, use as chaves de metadados dentro do [perfil técnico](relyingparty.md#technicalprofile)de terceira parte confiável:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Fluxo iniciado pelo provedor de identidade

Quando seu aplicativo espera receber uma Asserção SAML sem primeiro enviar uma solicitação de autenticação SAML para o provedor de identidade, você deve configurar Azure AD B2C para o fluxo iniciado pelo provedor de identidade.

No fluxo iniciado pelo provedor de identidade, o processo de entrada é iniciado pelo provedor de identidade (Azure AD B2C), que envia uma resposta SAML não solicitada para o provedor de serviços (seu aplicativo de terceira parte confiável).

Atualmente, não há suporte para cenários em que o provedor de identidade inicial é um provedor de identidade externo federado com Azure AD B2C, por exemplo, [AD-FS](identity-provider-adfs.md)ou [Salesforce](identity-provider-salesforce-saml.md). Só há suporte para a autenticação de conta local Azure AD B2C.

Para habilitar o fluxo iniciado pelo provedor de identidade, defina o item de metadados **IdpInitiatedProfileEnabled** como `true` no [perfil técnico de terceira parte confiável](relyingparty.md#technicalprofile).

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
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Substitua os seguintes valores:

* **nome do locatário** com o nome do locatário
* **nome da política** com o nome da política de terceira parte confiável SAML
* **app-Identifier-URI** com o `identifierUris` no arquivo de metadados, como `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política de exemplo completa que você pode usar para testar com o aplicativo de teste do SAML.

1. Baixe a [política de exemplo de logon iniciada pelo SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Atualize `TenantId` para corresponder ao nome do locatário, por exemplo, *contoso.b2clogin.com*.
1. Mantenha o nome da política *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algoritmo de assinatura de resposta SAML

Você pode configurar o algoritmo de assinatura usado para assinar a Asserção SAML. Os valores possíveis são `Sha256`, `Sha384`, `Sha512` ou `Sha1`. Verifique se o perfil técnico e o aplicativo usam o mesmo algoritmo de assinatura. Use apenas o algoritmo com suporte do seu certificado.

Configure o algoritmo de assinatura usando a `XmlSignatureAlgorithm` chave de metadados dentro do elemento de metadados de terceira parte confiável.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Tempo de vida de resposta SAML

Você pode configurar o período de tempo que a resposta SAML permanece válida. Defina o tempo de vida usando o `TokenLifeTimeInSeconds` item de metadados dentro do perfil técnico do emissor do token SAML. Esse valor é o número de segundos que podem decorrer do `NotBefore` carimbo de data/hora calculado no momento da emissão do token. O tempo de vida padrão é de 300 segundos (5 minutos).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>Resposta SAML válida de distorção

Você pode configurar a distorção de tempo aplicada ao carimbo de data/hora de resposta SAML `NotBefore` . Essa configuração garante que, se os tempos entre duas plataformas não estiverem em sincronia, a Asserção SAML ainda será considerada válida quando dentro dessa diferença de tempo.

Defina a distorção de tempo usando o `TokenNotBeforeSkewInSeconds` item de metadados dentro do perfil técnico do emissor do token SAML. O valor de distorção é fornecido em segundos, com um valor padrão de 0. O valor máximo é 3600 (uma hora).

Por exemplo, quando o `TokenNotBeforeSkewInSeconds` é definido como `120` segundos:

- O token é emitido às 13:05:10 UTC
- O token é válido a partir de 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Remover milissegundos da data e hora

Você pode especificar se os milissegundos serão removidos dos valores de DateTime na resposta SAML (incluindo IssueInstant, nobefore, NotOnOrAfter e AuthnInstant). Para remover os milissegundos, defina a `RemoveMillisecondsFromDateTime
` chave de metadados na terceira parte confiável. Valores possíveis: `false` (padrão) ou `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C ID do emissor

Se você tiver vários aplicativos SAML que dependem de `entityID` valores diferentes, poderá substituir o `issueruri` valor em seu arquivo de terceira parte confiável. Para substituir o URI do emissor, copie o perfil técnico com a ID "Saml2AssertionIssuer" do arquivo base e substitua o `issueruri` valor.

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

## <a name="session-management"></a>Gerenciamento da sessão

Você pode gerenciar a sessão entre Azure AD B2C e o aplicativo de terceira parte confiável SAML usando o `UseTechnicalProfileForSessionManagement` elemento e o [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="debug-the-saml-protocol"></a>Depurar o protocolo SAML

Para ajudar a configurar e depurar a integração com seu provedor de serviços, você pode usar uma extensão de navegador para o protocolo SAML, por exemplo, [extensão devtools SAML](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para Firefox [ou ferramentas de desenvolvedor do Edge ou IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Usando essas ferramentas, você pode verificar a integração entre seu aplicativo e Azure AD B2C. Por exemplo: 

* Verifique se a solicitação SAML contém uma assinatura e determine qual algoritmo é usado para entrar na solicitação de autorização.
* Verifique se Azure AD B2C retorna uma mensagem de erro.
* Verifique se a seção de asserção está criptografada.

## <a name="next-steps"></a>Próximas etapas

- Encontre mais informações sobre o [protocolo SAML no site da Oasis](https://www.oasis-open.org/).
- Obtenha o aplicativo Web de teste do SAML do [Azure ad B2C repositório de comunidade do GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
