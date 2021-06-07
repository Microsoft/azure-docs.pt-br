---
title: Credenciais de certificado da plataforma de identidade da Microsoft
titleSuffix: Microsoft identity platform
description: Este artigo aborda o registro e o uso de credenciais de certificado para autenticação do aplicativo.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582019"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenciais de certificado de autenticação do aplicativo da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft permite que um aplicativo use suas próprias credenciais para autenticação em qualquer lugar que um segredo do cliente possa ser usado, por exemplo, no fluxo de  [concessão de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) do OAuth 2,0 e no fluxo [em nome de](v2-oauth2-on-behalf-of-flow.md) (obo).

Uma forma de credencial que um aplicativo pode usar para autenticação é uma asserção JWT ( [token Web JSON](./security-tokens.md#json-web-tokens-and-claims) ) assinada com um certificado que o aplicativo possui.

## <a name="assertion-format"></a>Formato de asserção

Para computar a asserção, você pode usar uma das várias bibliotecas JWT no idioma de sua escolha- [MSAL dá suporte a `.WithCertificate()` isso usando ](msal-net-client-assertions.md). As informações são transportadas pelo token em seu [cabeçalho](#header), [declarações](#claims-payload)e [assinatura](#signature).

### <a name="header"></a>Cabeçalho

| Parâmetro |  Comentário |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | O hash de certificado X. 509 (também conhecido como a representação hexadecimal do certificado SHA-1 *impressão digital*) codificado como um valor de cadeia de caracteres Base64url. Por exemplo, dado um hash de certificado X. 509 de `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hex), a `x5t` declaração seria `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Declarações (carga)

Tipo de declaração | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A declaração "AUD" (público) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) consulte [RFC 7519, seção 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Nesse caso, esse destinatário é o servidor de logon (login.microsoftonline.com).
exp | 1601519414 | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento. Consulte [RFC 7519, seção 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Isso permite que a asserção seja usada até lá, portanto, mantenha-a curta a 5-10 minutos após `nbf` no máximo.  O Azure AD não coloca restrições no `exp` tempo atualmente. 
iss | ClientID | A declaração "ISS" (emissor) identifica a entidade de segurança que emitiu o JWT, neste caso, o aplicativo cliente.  Use a ID do aplicativo GUID.
jti | (um GUID) | A declaração "JTI" (ID JWT) fornece um identificador exclusivo para o JWT. O valor do identificador deve ser atribuído de maneira que garanta que haja uma probabilidade insignificante de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; Se o aplicativo usar vários emissores, as colisões deverão ser evitadas entre os valores produzidos por diferentes emissores também. O valor "JTI" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas. [RFC 7519, seção 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento. [RFC 7519, seção 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  O uso da hora atual é apropriado. 
sub | ClientID | A declaração "sub" (Subject) identifica o assunto do JWT, neste caso, também seu aplicativo. Use o mesmo valor de `iss` . 

### <a name="signature"></a>Assinatura

A assinatura é computada aplicando o certificado conforme descrito na [especificação RFC7519 do token Web JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma declaração JWT decodificada

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma declaração JWT codificada

A cadeia de caracteres a seguir é um exemplo de asserção codificado. Se olhar com cuidado, você notará três seções separadas por pontos ( `.` ):

* A primeira seção codifica o *cabeçalho*
* A segunda seção codifica as *declarações* (carga)
* A última seção é a *assinatura* computada com os certificados do conteúdo das duas primeiras seções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrar o certificado na plataforma de identidade da Microsoft

Você pode associar a credencial do certificado ao aplicativo cliente na plataforma de identidade da Microsoft por meio do portal do Azure usando qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Fazendo upload do arquivo de certificado

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **Certificados e segredos**.
2. Clique em **Carregar certificado** e selecione o arquivo de certificado a ser carregado.
3. Clique em **Adicionar**.
  Após o carregamento do certificado, os valores de impressão digital, data de início e expiração são exibidos.

### <a name="updating-the-application-manifest"></a>Atualizando o manifesto do aplicativo

Depois de adquirir um certificado, calcule esses valores:

- `$base64Thumbprint` -Valor codificado na base64 do hash do certificado
- `$base64Value` -Valor codificado na base64 dos dados brutos do certificado

Forneça um GUID para identificar a chave no manifesto do aplicativo ( `$keyId` ).

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **Manifesto** para abrir o manifesto do aplicativo.
2. Substitua a propriedade *keyCredentials* pelas novas informações de certificado usando o esquema a seguir.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Salve as edições no manifesto do aplicativo e, em seguida, carregue-o na plataforma de identidade da Microsoft.

   A propriedade `keyCredentials` tem vários valores, portanto, você pode fazer upload de vários certificados para um gerenciamento de chaves mais sofisticado.
   
## <a name="using-a-client-assertion"></a>Usando uma asserção de cliente

As asserções do cliente podem ser usadas em qualquer lugar que um segredo do cliente seja usado.  Por exemplo, no fluxo de [código de autorização](v2-oauth2-auth-code-flow.md), você pode passar um `client_secret` para provar que a solicitação é proveniente de seu aplicativo. Você pode substituir isso por `client_assertion` `client_assertion_type` parâmetros e. 

| Parâmetro | Valor | Descrição|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Esse é um valor fixo, indicando que você está usando uma credencial de certificado. |
|`client_assertion`| JWT |Este é o JWT criado acima. |

## <a name="next-steps"></a>Próximas etapas

A [biblioteca MSAL.net manipula esse cenário](msal-net-client-assertions.md) em uma única linha de código.

O [aplicativo de console do daemon do .NET Core usando](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) o exemplo de código da plataforma de identidade da Microsoft no GitHub mostra como um aplicativo usa suas próprias credenciais para autenticação. Ele também mostra como você pode [criar um certificado autoassinado](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando o `New-SelfSignedCertificate` cmdlet do PowerShell. Você também pode usar os [scripts de criação de aplicativo](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) no repositório de exemplo para criar certificados, calcular a impressão digital e assim por diante.
