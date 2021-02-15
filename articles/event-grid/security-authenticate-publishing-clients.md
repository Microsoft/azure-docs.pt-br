---
title: Autenticar eventos de publicação de clientes em domínios ou tópicos personalizados da grade de eventos
description: Este artigo descreve diferentes maneiras de autenticar os eventos de publicação de clientes para tópicos personalizados da grade de eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886253"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Autenticar clientes de publicação (grade de eventos do Azure)
Este artigo fornece informações sobre como autenticar os clientes que publicam eventos em domínios ou tópicos da grade de eventos do Azure usando a **chave de acesso** ou o token **SAS (assinatura de acesso compartilhado)** . É recomendável usar o token SAS, mas a autenticação de chave fornece programação simples e é compatível com muitos Publicadores de webhook existentes.  

## <a name="authenticate-using-an-access-key"></a>Autenticar usando uma chave de acesso
A autenticação de chave de acesso é a forma mais simples de autenticação. Você pode passar a chave de acesso como um cabeçalho HTTP ou um parâmetro de consulta de URL. 

### <a name="access-key-in-a-http-header"></a>Chave de acesso em um cabeçalho HTTP
Passe a chave de acesso como um valor para o cabeçalho HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Chave de acesso como um parâmetro de consulta
Você também pode especificar `aeg-sas-key` como um parâmetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Para obter instruções sobre como obter chaves de acesso para um tópico ou domínio, consulte [obter chaves de acesso](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Autenticar usando um token SAS
Os tokens SAS para um recurso da grade de eventos incluem o recurso, o tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho de recurso válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`. Para ver todas as versões de API compatíveis, confira [Tipos de recurso do Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Primeiro, gere programaticamente um token SAS e, em seguida, use o `aeg-sas-token` cabeçalho ou `Authorization SharedAccessSignature` cabeçalho para autenticar com a grade de eventos. 

### <a name="generate-sas-token-programmatically"></a>Gerar token SAS programaticamente
O exemplo a seguir cria um token SAS para uso com a Grade de Eventos:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Usando o cabeçalho AEG-SAS-token
Aqui está um exemplo de como passar o token SAS como um valor para o `aeg-sas-token` cabeçalho. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Usando o cabeçalho de autorização
Aqui está um exemplo de como passar o token SAS como um valor para o `Authorization` cabeçalho. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Próximas etapas
Consulte [autenticação de entrega de eventos](security-authentication.md) para saber mais sobre a autenticação com manipuladores de eventos para entregar eventos. 
