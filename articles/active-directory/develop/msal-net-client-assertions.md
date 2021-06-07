---
title: Asserções de cliente (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as declarações de cliente assinadas com suporte para aplicativos cliente confidenciais na biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8fb4ecb8fa8d6938e9afbc77064380b7b213029a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578729"
---
# <a name="confidential-client-assertions"></a>Asserções confidenciais do cliente

Para provar sua identidade, os aplicativos cliente confidenciais trocam um segredo com o Azure AD. O segredo pode ser:
- Um segredo do cliente (senha do aplicativo).
- Um certificado, que é usado para criar uma asserção assinada contendo declarações padrão.

Esse segredo também pode ser uma declaração assinada diretamente.

O MSAL.NET tem quatro métodos para fornecer credenciais ou asserções ao aplicativo cliente confidencial:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Embora seja possível usar a `WithClientAssertion()` API para adquirir tokens para o cliente confidencial, não recomendamos usá-lo por padrão, pois ele é mais avançado e projetado para lidar com cenários muito específicos que não são comuns. Usar a `.WithCertificate()` API permitirá que o MSAL.net manipule isso para você. Essa API oferece a capacidade de personalizar sua solicitação de autenticação, se necessário, mas a declaração padrão criada por `.WithCertificate()` será suficiente para a maioria dos cenários de autenticação. Essa API também pode ser usada como uma solução alternativa em alguns cenários em que o MSAL.NET falha ao executar a operação de assinatura internamente.

### <a name="signed-assertions"></a>Asserções assinadas

Uma declaração de cliente assinada assume a forma de um JWT assinado com a carga que contém as declarações de autenticação necessárias exigidas pelo Azure AD, codificada em base64. Para usá-lo:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Você também pode usar o formulário delegado, que permite que você calcule a asserção just in time:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
```

As [declarações esperadas pelo Azure ad](active-directory-certificate-credentials.md) na declaração assinada são:

Tipo de declaração | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A declaração "AUD" (público) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) consulte [RFC 7519, seção 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Nesse caso, esse destinatário é o servidor de logon (login.microsoftonline.com).
exp | 1601519414 | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento. Consulte [RFC 7519, seção 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Isso permite que a asserção seja usada até lá, portanto, mantenha-a curta a 5-10 minutos após `nbf` no máximo.  O Azure AD não coloca restrições no `exp` tempo atualmente. 
iss | ClientID | A declaração "ISS" (emissor) identifica a entidade de segurança que emitiu o JWT, neste caso, o aplicativo cliente.  Use a ID do aplicativo GUID.
jti | (um GUID) | A declaração "JTI" (ID JWT) fornece um identificador exclusivo para o JWT. O valor do identificador deve ser atribuído de maneira que garanta que haja uma probabilidade insignificante de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; Se o aplicativo usar vários emissores, as colisões deverão ser evitadas entre os valores produzidos por diferentes emissores também. O valor "JTI" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas. [RFC 7519, seção 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento. [RFC 7519, seção 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  O uso da hora atual é apropriado. 
sub | ClientID | A declaração "sub" (Subject) identifica o assunto do JWT, neste caso, também seu aplicativo. Use o mesmo valor de `iss` . 

Aqui está um exemplo de como criar essas declarações:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Aqui está como criar uma declaração de cliente assinada:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Método alternativo

Você também tem a opção de usar [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para criar a asserção para você. O código será mais elegante, conforme mostrado no exemplo abaixo:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Depois de ter a declaração de cliente assinada, você pode usá-la com as APIs MSAL, conforme mostrado abaixo.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` Por padrão, produzirá uma asserção assinada contendo as declarações esperadas pelo Azure AD mais declarações de cliente adicionais que você deseja enviar. Aqui está um trecho de código sobre como fazer isso.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se uma das declarações no dicionário que você passa for igual a uma das declarações obrigatórias, o valor da declaração adicional será levado em conta. Ele substituirá as declarações computadas por MSAL.NET.

Se você quiser fornecer suas próprias declarações, incluindo as declarações obrigatórias esperadas pelo Azure AD, passe `false` para o `mergeWithDefaultClaims` parâmetro.
