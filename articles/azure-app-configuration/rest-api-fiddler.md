---
title: API REST do Azure Active Directory-teste usando o Fiddler
description: Usar o Fiddler para testar a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932601"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testar a API REST de configuração de Azure App usando o Fiddler

Para testar a API REST usando o [Fiddler](https://www.telerik.com/fiddler), você precisará incluir os cabeçalhos HTTP necessários para [autenticação](./rest-api-authentication-hmac.md) em suas solicitações. Veja como configurar o Fiddler para testar a API REST, gerando automaticamente os cabeçalhos de autenticação:

1. Verifique se o TLS 1,2 é um protocolo permitido:
    1. Vá para **ferramentas**  >  **Opções**  >  **https**).
    1. Verifique se a opção **descriptografar tráfego HTTPS** está marcada.
    1. Na lista de protocolos, adicione **TLS 1.2** , se não estiver presente.
1. Abra o **Editor de scripts Fiddler** ou pressione **Ctrl-R** no Fiddler
1. Adicione o seguinte código dentro da `Handlers` classe antes da `OnBeforeRequest` função

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Adicione o código a seguir ao final da `OnBeforeRequest` função. Atualize a chave de acesso conforme indicado pelo comentário TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Usar o [Composer do Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) para gerar e enviar uma solicitação
