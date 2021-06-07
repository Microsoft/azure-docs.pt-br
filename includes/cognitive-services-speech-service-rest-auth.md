---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033432"
---
## <a name="authentication"></a>Autenticação

Cada solicitação requer um cabeçalho de autorização. Esta tabela ilustra quais cabeçalhos são suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de fala em texto | Conversão de texto em fala |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Sim | Sim |
| Autorização: portador | Sim | Sim |

Ao usar o cabeçalho `Ocp-Apim-Subscription-Key`, você só precisa fornecer sua chave de assinatura. Por exemplo:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao usar o cabeçalho `Authorization: Bearer`, você precisa fazer uma solicitação ao `issueToken` endpoint. Nesta solicitação, você troca sua chave de assinatura por um token de acesso válido por 10 minutos. Nas próximas seções, você aprenderá a obter um token e usará um token.

### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso, você precisará fazer uma solicitação para o endpoint `issueToken` usando a `Ocp-Apim-Subscription-Key` e sua chave de assinatura.

O `issueToken` ponto de extremidade tem este formato:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Substituir `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Use essas amostras para criar sua solicitação de token de acesso.

#### <a name="http-sample"></a>Exemplo de HTTP

Este exemplo é uma solicitação HTTP simples para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Se sua assinatura não se encontra na região Oeste dos EUA, substitua o cabeçalho `Host` pelo nome de host da sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o token de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Este exemplo é um script simples do PowerShell para obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Certifique-se de usar o endpoint correto para a região que corresponde à sua assinatura. Este exemplo está atualmente definido para o oeste dos EUA.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>exemplo de cURL

cURL é uma ferramenta de linha de comando no Linux (e no Subsistema do Windows para Linux). Este comando cURL ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Certifique-se de usar o endpoint correto para a região que corresponde à sua assinatura. Este exemplo está atualmente definido para o oeste dos EUA.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo de C#

Esta classe C# ilustra como obter um token de acesso. Passar sua chave de assinatura do serviço de Fala quando você instanciar a classe. Se a sua assinatura não estiver na região Oeste dos EUA, altere o valor de `FetchTokenUri` para que corresponda à região da sua assinatura.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Exemplo de Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Como usar um token de acesso

O token de acesso deve ser enviado para o serviço como o cabeçalho `Authorization: Bearer <TOKEN>`. Cada token de acesso é válido por 10 minutos. Você pode obter um novo token a qualquer momento, no entanto, para minimizar o tráfego de rede e a latência, recomendamos usar o mesmo token por nove minutos.

Aqui está um exemplo de solicitação HTTP para a API REST de conversão de fala em texto para áudio curto:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
