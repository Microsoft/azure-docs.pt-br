---
title: Introdução à Tradução de Documento
description: Como criar um serviço de tradução de documentos usando linguagens e plataformas de programação C#, go, Java, Node.js ou Python
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: 780e6defe4f7d09e2d136c080525447ffd29bbb4
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612374"
---
# <a name="get-started-with-document-translation-preview"></a>Introdução à tradução do documento (visualização)

 Neste artigo, você aprenderá a usar a tradução de documentos com métodos da API REST HTTP. A tradução de documentos é um recurso baseado em nuvem do serviço [do Azure Translator](../translator-info-overview.md) .  A API de tradução de documentos permite a tradução de documentos inteiros, preservando a estrutura do documento de origem e a formatação do texto.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
>
> 1. Geralmente, quando você cria um recurso de serviço cognitiva no portal do Azure, você tem a opção de criar uma chave de assinatura de vários serviços ou uma chave de assinatura de serviço único. No entanto, atualmente, a tradução de documentos é suportada apenas no recurso Tradutor (serviço único) e **não** está incluída no recurso serviços cognitivas (vários serviços).
> 2. A tradução de documentos está disponível no momento no **plano de serviço padrão S1**. _Veja_ [preços de serviços cognitivas – Tradutor](https://azure.microsoft.com/pricing/details/cognitive-services/translator/).
>

Para começar, você precisará de:

* Uma [**conta ativa do Azure**](https://azure.microsoft.com/free/cognitive-services/).  Se você não tiver uma, poderá [**criar uma conta gratuita**](https://azure.microsoft.com/free/).

* Um recurso de serviço do [**Tradutor**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**não** um recurso de serviços cognitivas).

* Uma [**conta de armazenamento de BLOBs do Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você criará contêineres para armazenar e organizar seus dados de BLOB em sua conta de armazenamento.

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Obter seu nome de domínio personalizado e a chave de assinatura

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não usará o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

### <a name="what-is-the-custom-domain-endpoint"></a>O que é o ponto de extremidade de domínio personalizado?

O ponto de extremidade do domínio personalizado é uma URL formatada com os subdiretórios Name, hostname e Translator do recurso:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Localizar seu nome de domínio personalizado

O parâmetro **nome-do-recurso** (também chamado de *nome de domínio personalizado*) é o valor que você inseriu no campo **nome** quando criou o recurso do tradutor.

:::image type="content" source="../media/instance-details.png" alt-text="Imagem do portal do Azure, criar recurso, detalhes instantâneos, campo nome.":::

### <a name="get-your-subscription-key"></a>Obter sua chave de assinatura

As solicitações para o serviço do tradutor exigem uma chave somente leitura para autenticar o acesso.

1. Se você tiver criado um novo recurso, depois que ele for implantado, selecione **ir para o recurso**. Se você tiver um recurso de tradução de documento existente, navegue diretamente para a página de recursos.
1. No trilho esquerdo, em *Gerenciamento de recursos*, selecione **chaves e ponto de extremidade**.
1. Copie e cole sua chave de assinatura em um local conveniente, como *o bloco de notas da Microsoft*.
1. Você o colará no código abaixo para autenticar sua solicitação para o serviço de tradução de documentos.

:::image type="content" source="../media/translator-keys.png" alt-text="Imagem do campo obter sua chave de assinatura em portal do Azure.":::

## <a name="create-your-azure-blob-storage-containers"></a>Criar seus contêineres de armazenamento de BLOBs do Azure

Você precisará  [**criar contêineres**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) em sua [**conta de armazenamento de BLOBs do Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para arquivos de origem, destino e Glossário opcionais.

* **Contêiner de origem**. Esse contêiner é onde você carrega os arquivos para tradução (obrigatório).
* **Contêiner de destino**. Esse contêiner é onde os arquivos traduzidos serão armazenados (obrigatório).  
* **Contêiner de Glossário**. Esse contêiner é onde você carrega seus arquivos de Glossário (opcional).  

### <a name="create-sas-access-tokens-for-document-translation"></a>**Criar tokens de acesso SAS para tradução de documentos**

Os `sourceUrl` `targetUrl` opcionais, e `glossaryUrl`  devem incluir um token de SAS (assinatura de acesso compartilhado), anexado como uma cadeia de caracteres de consulta. O token pode ser atribuído ao contêiner ou a BLOBs específicos. *Consulte* [**criar tokens SAS para o processo de tradução de documentos**](create-sas-tokens.md).

* O contêiner ou o blob de **origem** deve ter acesso de **lista** e **leitura** designado.
* O contêiner ou BLOB de **destino** deve ter o acesso de  **gravação** e **lista** designado.
* O contêiner ou BLOB do **Glossário** deve ter acesso de  **leitura** e **lista** designado.

> [!TIP]
>
> * Se você estiver traduzindo **vários** arquivos (BLOBs) em uma operação, **delegado o acesso SAS no nível de contêiner**.  
> * Se você estiver traduzindo um **único** arquivo (BLOB) em uma operação, **delegar acesso SAS no nível do blob**.  
>

## <a name="set-up-your-coding-platform"></a>Configurar sua plataforma de codificação

### <a name="c"></a>[C#](#tab/csharp)

* Criar um novo projeto.
* Substitua Program.cs pelo C# código mostrado abaixo.
* Defina seus valores de ponto de extremidade, chave de assinatura e URL de contêiner em Program. cs.
* Para processar dados JSON, adicione [Newtonsoft.Jsno pacote usando a CLI do .net](https://www.nuget.org/packages/Newtonsoft.Json/).
* Execute o programa a partir do diretório do projeto.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Crie um novo projeto Node.js.
* Instale a biblioteca axios com `npm i axios` .
* Copie/cole o código abaixo em seu projeto.
* Defina os valores de ponto de extremidade, chave de assinatura e URL do contêiner.
* executar o programa.

### <a name="python"></a>[Python](#tab/python)  

* Criar um novo projeto.
* Copie e cole o código de um dos exemplos em seu projeto.
* Defina os valores de ponto de extremidade, chave de assinatura e URL do contêiner.
* Execute o programa. Por exemplo: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Crie um diretório de trabalho para seu projeto. Por exemplo:

```powershell
mkdir sample-project
```

* No diretório do projeto, crie a seguinte estrutura de subdiretório:  

  src</br>
&emsp; └ principal</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

**Observação**: os arquivos de origem Java (por exemplo, _Sample. java_) residem em src/main/**Java**.

* No diretório raiz (por exemplo, *projeto de exemplo*), inicialize o projeto com gradle:

```powershell
gradle init --type basic
```

* Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

* Atualize o `build.gradle.kts`  arquivo. Tenha em mente que você precisará atualizar seu `mainClassName` dependendo do exemplo:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Crie um arquivo Java no diretório **Java** e copie/cole o código do exemplo fornecido. Não se esqueça de adicionar a chave de assinatura e o ponto de extremidade.

* **Compile e execute o exemplo do diretório raiz**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)  

* Crie um novo projeto go.
* Adicione o código fornecido abaixo.
* Defina os valores de ponto de extremidade, chave de assinatura e URL do contêiner.
* Salve o arquivo com uma extensão ".go".
* Abra um prompt de comando em um computador com o Go instalado.
* Crie o arquivo. Por exemplo: ' go compilar exemplo-código. go '.
* Execute o arquivo, por exemplo: 'example-code'.

 ---

## <a name="make-document-translation-requests"></a>Fazer solicitações de tradução de documentos

Uma solicitação de tradução de documento em lote é enviada para o ponto de extremidade de serviço do tradutor por meio de uma solicitação POST. Se for bem-sucedido, o método POST retornará um `202 Accepted`  código de resposta e a solicitação em lote será criada pelo serviço.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os seguintes cabeçalhos estão incluídos em cada solicitação de API do tradutor de documento:

|Cabeçalho HTTP|Descrição|
|---|--|
|Ocp-Apim-Subscription-Key|**Obrigatório**: o valor é a chave de assinatura do Azure para seu tradutor ou recurso de serviços cognitivas.|
|Tipo de conteúdo|**Obrigatório**: especifica o tipo de conteúdo da carga. Os valores aceitos são Application/JSON ou CharSet = UTF-8.|
|Content-Length|**Obrigatório**: o comprimento do corpo da solicitação.|

### <a name="post-request-body-properties"></a>Propriedades do corpo da solicitação POST

* O corpo da solicitação POST é um objeto JSON chamado `inputs` .
* O `inputs` objeto contém os dois  `sourceURL` e os `targetURL`  endereços de contêiner para os pares de idiomas de origem e de destino e pode, opcionalmente, conter um `glossaryURL` endereço de contêiner.
* Os `prefix` `suffix` campos e (opcional) são usados para filtrar documentos no contêiner, incluindo pastas.
* Um valor para o  `glossaries`  campo (opcional) é aplicado quando o documento está sendo traduzido.
* O `targetUrl` idioma para cada destino deve ser exclusivo.

>[!NOTE]
> Se já existir um arquivo com o mesmo nome no destino, ele será substituído.

## <a name="post-a-translation-request"></a>POSTAR uma solicitação de tradução

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-to-translate-all-documents-in-a-container"></a>POSTAR o corpo da solicitação para converter todos os documentos em um contêiner

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```


### <a name="post-request-body-to-translate-a-specific-document-in-a-container"></a>POSTAR o corpo da solicitação para traduzir um documento específico em um contêiner

* Verifique se você especificou "storagetype": "File"
* Verifique se você criou a URL de origem & token SAS para o blob/documento específico (não para o contêiner) 
* Verifique se você especificou o nome de arquivo de destino como parte da URL de destino – embora o token SAS ainda seja para o contêiner.
* Exemplo de solicitação abaixo mostra um único documento sendo traduzido em duas linguagens de destino

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```


> [!IMPORTANT]
>
> Para obter os exemplos de código abaixo, você codificará sua chave e o ponto de extremidade, quando indicado; Lembre-se de remover a chave do seu código quando terminar e nunca publicá-la publicamente.  Consulte [segurança de serviços cognitivas do Azure](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) para obter maneiras de armazenar e acessar com segurança suas credenciais.
>
> Talvez seja necessário atualizar os seguintes campos, dependendo da operação:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (ID do trabalho)
>>

#### <a name="locating--the-id-value"></a>Localizando o `id` valor

* Você encontrará o trabalho `id`  no valor da URL do cabeçalho de resposta do método post `Operation-Location`  . O último parâmetro da URL é o trabalho da operação **`id`** :

|**Cabeçalho de resposta**|**URL do resultado**|
|-----------------------|----------------|
Operation-Location   | https://<<span>nome do seu recurso>. cognitiveservices.Azure.com/Translator/Text/batch/v1.0-Preview.1/batches/9dce0aa9-78dc-41BA-8cae-2e2f3c2ff8ec</span>

* Você também pode usar uma solicitação para **obter trabalhos** para recuperar um trabalho de tradução de documentos `id` .

>

## <a name="_post-document-translation_-request"></a>_Postar_ solicitação de tradução do documento

Envie uma solicitação de tradução de documento em lote para o serviço de tradução.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_OBTER formatos de arquivo_ 

Recupere uma lista de formatos de arquivo com suporte. Se for bem-sucedido, esse método retornará um `200 OK` código de resposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_OBTER status do trabalho_ 

Obtenha o status atual de um único trabalho e um resumo de todos os trabalhos em uma solicitação de tradução de documento. Se for bem-sucedido, esse método retornará um `200 OK` código de resposta.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_OBTER status do documento_

### <a name="brief-overview"></a>Visão geral resumida

Recuperar o status de um documento específico em uma solicitação de tradução de documento. Se for bem-sucedido, esse método retornará um `200 OK` código de resposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_EXCLUIR trabalho_ 

### <a name="brief-overview"></a>Visão geral resumida

Cancele o processamento ou trabalho em fila no momento. Somente os documentos para os quais a tradução não foi iniciada serão cancelados.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Limites de conteúdo

A tabela a seguir lista os limites de dados que você envia para a tradução de documentos (versão prévia).

|Atributo | Limite|
|---|---|
|Tamanho do documento| ≤ 40 MB |
|Número total de arquivos.|≤ 1000 |
|Tamanho total do conteúdo em um lote | ≤ 250 MB|
|Número de idiomas de destino em um lote| ≤ 10 |
|Tamanho do arquivo de memória de tradução| ≤ 10 MB|

> [!NOTE]
> Os limites de conteúdo acima estão sujeitos a alterações antes da versão pública.

## <a name="learn-more"></a>Saiba mais

* [Referência da API de Tradução v3](../reference/v3-0-reference.md)
* [Suporte ao idioma](../language-support.md)
* [Assinaturas no gerenciamento de API do Azure](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um sistema de idiomas personalizado usando o tradutor personalizado](../custom-translator/overview.md)
>
>
