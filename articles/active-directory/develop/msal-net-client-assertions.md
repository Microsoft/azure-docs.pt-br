---
title: Declarações de cliente na biblioteca de autenticação da Microsoft para .NET | Azure
description: Saiba mais sobre as declarações de cliente assinadas com suporte para aplicativos cliente confidenciais na biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442499"
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

### <a name="signed-assertions"></a>Asserções assinadas

Uma declaração de cliente assinada assume a forma de um JWT assinado com a carga que contém as declarações de autenticação necessárias exigidas pelo Azure AD, codificada em base64. Para usá-lo:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

As declarações esperadas pelo Azure AD são:

Tipo de declaração | Valor | Descrição
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | A declaração "AUD" (público) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) consulte [RFC 7519, section 4.1.3]
exp | Qui Jun 27 2019 15:04:17 GMT + 0200 (horário de Verão de romance) | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento. Consulte [RFC 7519, seção 4.1.4]
iss | ClientID | A declaração "ISS" (emissor) identifica a entidade de segurança que emitiu o JWT. O processamento dessa declaração é específico do aplicativo. O valor "ISS" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas contendo um valor StringOrURI. [RFC 7519, seção 4.1.1]
jti | (um GUID) | A declaração "JTI" (ID JWT) fornece um identificador exclusivo para o JWT. O valor do identificador deve ser atribuído de maneira que garanta que haja uma probabilidade insignificante de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; Se o aplicativo usar vários emissores, as colisões deverão ser evitadas entre os valores produzidos por diferentes emissores também. A declaração "JTI" pode ser usada para impedir que o JWT seja reproduzido. O valor "JTI" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas. [RFC 7519, seção 4.1.7]
nbf | Qui Jun 27 2019 14:54:17 GMT + 0200 (horário de Verão de romance) | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento. [RFC 7519, seção 4.1.5]
sub | ClientID | A declaração "sub" (Subject) identifica o assunto do JWT. As declarações em um JWT normalmente são instruções sobre o assunto. O valor da entidade deve ter o escopo definido para ser local exclusivo no contexto do emissor ou ser globalmente exclusivo. Veja [RFC 7519, section 4.1.2]

Aqui está um exemplo de como criar essas declarações:

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

```CSharp
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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`Por padrão, produzirá uma asserção assinada contendo as declarações esperadas pelo Azure AD mais declarações de cliente adicionais que você deseja enviar. Aqui está um trecho de código sobre como fazer isso.

```CSharp
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
