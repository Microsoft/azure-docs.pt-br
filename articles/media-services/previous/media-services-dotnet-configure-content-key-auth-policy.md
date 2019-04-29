---
title: Configurar uma política de autorização de chave de conteúdo usando o SDK do .NET dos Serviços de Mídia | Microsoft Docs
description: Saiba como configurar uma política de autorização para uma chave de conteúdo usando o SDK do .NET dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;mingfeiy
ms.openlocfilehash: 744887a3b23518a5b970a3fda94bf990806bd2d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230263"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Criptografia dinâmica: Configurar uma política de autorização de chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Visão geral
 É possível usar os Serviços de Mídia do Azure para entregar MPEG-DASH, Smooth Streaming e HLS (HTTP Live Streaming) protegidos com a criptografia AES por meio do uso de chaves de criptografia de 128 bits ou do [DRM (Gerenciamento de Direitos Digitais) do PlayReady](https://www.microsoft.com/playready/overview/). Com os Serviços de Mídia, também é possível distribuir streams DASH criptografados com o DRM do Widevine. O PlayReady e o Widevine são criptografados de acordo com a especificação de criptografia comum (ISO/IEC 23001-7 CENC).

Os Serviços de Mídia também fornecem um serviço de entrega de chaves/licenças por meio do qual os clientes podem obter chaves AES ou licenças do PlayReady/Widevine para reproduzir o conteúdo criptografado.

Se você desejar que os Serviços de Mídia criptografem um ativo, associe uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo. Para saber mais, confira [Criar ContentKeys com .NET](media-services-dotnet-create-contentkey.md). Você também precisa configurar políticas de autorização para a chave (conforme descrito neste artigo).

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização. As opções são restrição aberta ou por token. A política restrita de token deve ser acompanhada por um token emitido por um STS (serviço de token de segurança). Os Serviços de Mídia são compatíveis com tokens no formato [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (Token Web Simples) e no formato [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (Token Web JSON).

Os Serviços de Mídia não oferecem um STS. Você pode criar um STS personalizado ou usar o Serviço de Controle de Acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com a chave especificada e declarações de emissão que você especificou na configuração de restrição do token (conforme descrito neste artigo). Se o token for válido e as declarações no token corresponderem às configuradas para a chave de conteúdo, o serviço de distribuição de chave dos Serviços de Mídia retornará a chave criptografada para o cliente.

Para obter mais informações, consulte os seguintes artigos:

- [Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrar o aplicativo OWIN com base no MVC dos Serviços de Mídia do Azure com o Azure Active Directory e restringir a entrega da chave de conteúdo restrito com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado "Parado". Para começar a transmitir seu conteúdo e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming deve estar no estado “Executando”. 
* O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).
* Carregar e codificar seus ativos usando a opção AssetCreationOptions.StorageEncrypted.
* Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, recomendamos criar uma política de autorização única e reutilizá-la com várias chaves de conteúdo.
* O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos. Você pode criar ContentKeyAuthorizationPolicy e especificar o uso de uma restrição de token, testá-la e, em seguida, atualizar a política para a restrição aberta. Esse processo leva aproximadamente 15 minutos antes de a política mudar para a versão aberta da política.
* Se você adicionar ou atualizar a política de entrega do ativo, deverá excluir um localizador existente e criar um novo localizador.
* No momento, não é possível criptografar downloads progressivos.
* O ponto de extremidade de streaming dos Serviços de Mídia define o valor do cabeçalho Access-Control-Allow-Origin do CORS na resposta de simulação como o curinga “\*”. Esse valor funciona bem com a maioria dos players, incluindo o Player de Mídia do Azure, Roku e JWPlayer e outros. No entanto, alguns players que usam dashjs não funcionam, pois, com o modo de credenciais definido para "incluir", o XMLHttpRequest em seu dashjs não permite o curinga “\*” como o valor de Access-Control-Allow-Origin. Como uma solução alternativa para essa limitação em dashjs, se você hospedar seu cliente em um único domínio, os Serviços de Mídia poderão especificar esse domínio no cabeçalho da resposta de simulação. Para obter assistência, abra um tíquete de suporte por meio do portal do Azure.

## <a name="aes-128-dynamic-encryption"></a>Criptografia dinâmica AES-128
### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave a qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e a adiciona à chave de conteúdo:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Restrição de token
Esta seção descreve como criar uma política de autorização de chave de conteúdo e associá-la com a chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave. Por exemplo, a lista de chave de verificação contém a chave com a qual o token foi assinado?

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. O XML de configuração de restrição de token deve estar de acordo com o esquema XML a seguir:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

Ao usar o SDK dos Serviços de Mídia para .NET, você pode usar a classe TokenRestrictionTemplate para gerar o token de restrição.
O exemplo a seguir cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente precisa apresentar um token que contenha a chave de assinatura (VerificationKey), um emissor de token e as declarações necessárias.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Token de teste
Para obter um token de teste com base na restrição de token que foi usada para a política de autorização da chave, faça o seguinte:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Criptografia dinâmica do PlayReady
Você pode usar os Serviços de Mídia para configurar os direitos e restrições que você deseja para que o tempo de execução do PlayReady DRM imponha quando um usuário tentar reproduzir conteúdo protegido. 

Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](media-services-playready-license-template-overview.md). No SDK dos Serviços de Mídia para .NET, as classes PlayReadyLicenseResponseTemplate e PlayReadyLicenseTemplate ajudam a definir o modelo de licença do PlayReady.

Para saber como criptografar seu conteúdo com PlayReady e Widevine, consulte [Usar criptografia comum dinâmica do PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave a qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e a adiciona à chave de conteúdo:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Restrição de token
Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. O XML de configuração de restrição de token deve estar em conformidade com o esquema XML mostrado na seção “Esquema de restrição de token”.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Para obter um token de teste com base na restrição de token que foi usada para a política de autorização da chave, consulte a seção “[Testar token](#test-token)”. 

## <a id="types"></a>Tipos usados ao definir ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou a política de autorização da chave de conteúdo, consulte [Configurar uma política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

