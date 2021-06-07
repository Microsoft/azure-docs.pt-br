---
title: API do Coletor de Dados HTTP do Azure Monitor | Microsoft Docs
description: É possível usar a API do Coletor de Dados HTTP do Azure Monitor para adicionar dados JSON POST a um espaço de trabalho do Log Analytics de qualquer cliente que possa chamar a API REST. Este artigo descreve como usar a API e tem exemplos de como publicar dados usando diferentes linguagens de programação.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: e32bf95ef52fdd081eeaa476f44bf5dab99657d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452111"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Enviar dados de log para o Azure Monitor com a API do Coletor de Dados HTTP (visualização pública)
Este artigo mostra como usar a API do Coletor de Dados HTTP para enviar dados de log para o Azure Monitor a partir de um cliente API REST.  O artigo descreve como formatar dados coletados pelo script ou aplicativo, incluí-los em uma solicitação e ter essa solicitação autorizada pelo Azure Monitor.  Os exemplos são fornecidos para PowerShell, C# e Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> A API do Coletor de Dados HTTP do Azure Monitor está em visualização pública.

## <a name="concepts"></a>Conceitos
É possível usar a API do Coletor de Dados HTTP para enviar dados de log a um espaço de trabalho do Log Analytics no Azure Monitor de qualquer cliente que possa chamar uma API REST.  Pode ser um runbook no Automação do Azure que coleta dados de gerenciamento do Azure ou de outra nuvem, ou pode ser um sistema de gerenciamento alternativo que usa o Azure Monitor para consolidar e analisar dados de log.

Todos os dados no espaço de trabalho do Log Analytics são armazenados como um registro com um tipo de registro específico.  Formate seus dados para enviar à API do Coletor de Dados HTTP como vários registros em JSON.  Quando você envia os dados, um registro individual é criado no repositório de cada registro no conteúdo da solicitação.


![Visão geral do Coletor de Dados HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Criar uma solicitação
Para usar a API do Coletor de Dados HTTP, crie uma solicitação POST que inclua os dados a serem enviados em JSON (JavaScript Object Notation).  As próximas três tabelas listam os atributos que são necessários para cada solicitação. Descrevemos cada atributo em mais detalhes posteriormente neste artigo.

### <a name="request-uri"></a>URI da solicitação
| Atributo | Propriedade |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de conteúdo |aplicativo/json |

### <a name="request-uri-parameters"></a>Solicitar parâmetros de URI (Uniform Resource Identifier)
| Parâmetro | Descrição |
|:--- |:--- |
| CustomerID |O identificador exclusivo do espaço de trabalho do Log Analytics. |
| Recurso |O nome do recurso de API: /api/logs. |
| Versão da API |A versão da API a ser usada com esta solicitação. Atualmente, ela é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos da solicitação
| parâmetro | Descrição |
|:--- |:--- |
| Autorização |A assinatura de autorização. Posteriormente neste artigo, você pode ler sobre como criar um cabeçalho HMAC-SHA256. |
| Log-Type |Especifique o tipo de registro dos dados que estão sendo enviados. Pode conter apenas letras, números e sublinhado (_) e não pode exceder 100 caracteres. |
| x-ms-date |A data em que a solicitação foi processada, no formato RFC 1123. |
| x-MS-AzureResourceId | ID de recurso do recurso do Azure ao qual os dados devem ser associados. Isso popula a propriedade [_ResourceId](./log-standard-columns.md#_resourceid) e permite que os dados sejam incluídos em consultas de [contexto de recurso](./design-logs-deployment.md#access-mode) . Se esse campo não for especificado, os dados não serão incluídos nas consultas de contexto de recurso. |
| time-generated-field | O nome de um campo nos dados que contém o carimbo de data/hora do item de dados. Se você especificar um campo, seu conteúdo será usado para **TimeGenerated**. Se esse campo não for especificado, o padrão para **TimeGenerated** será a hora em que a mensagem é incluída. O conteúdo do campo de mensagem deve seguir o formato ISO 8601 AAAA-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autorização
Todas as solicitações para a API do Coletor de Dados HTTP do Azure Monitor devem incluir um cabeçalho de autorização. Para autenticar uma solicitação, você deve assinar a solicitação com a chave primária ou secundária para o workspace que está fazendo a solicitação. Em seguida, passe essa assinatura como parte da solicitação.   

Aqui está o formato do cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador exclusivo do espaço de trabalho do Log Analytics. *Signature* é um [HMAC (Código de Autenticação de Mensagem Baseado em Hash)](/dotnet/api/system.security.cryptography.hmacsha256) que é construído a partir da solicitação e, em seguida, calculado usando o [algoritmo SHA256](/dotnet/api/system.security.cryptography.sha256). Em seguida, você o codifica usando a codificação Base64.

Use este formato para codificar a cadeia de caracteres de assinatura **SharedKey**:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Aqui está um exemplo de uma cadeia de caracteres de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando você tiver a cadeia de caracteres de assinatura, codifique-a usando o algoritmo HMAC-SHA256 na sequência de caracteres codificada em UTF-8 e codifique o resultado em Base64. Use este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Os exemplos nas seções a seguir têm o código de exemplo para ajudá-lo a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo da solicitação
O corpo da mensagem deve ser em JSON. Ele deve incluir um ou mais registros com os pares nome e valor da propriedade no formato a seguir. O nome da propriedade só pode conter letras, números e sublinhado (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Você pode criar lotes de vários registros em uma única solicitação usando o formato a seguir. Todos os registros devem ser do mesmo tipo de registro.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Propriedades e o tipo de registro
Você define um tipo de registro personalizado quando envia dados por meio da API do Coletor de Dados HTTP do Azure Monitor. No momento, você não pode gravar dados em tipos de registro existentes que foram criados por outras soluções e tipos de dados. O Azure Monitor lê os dados recebidos e, em seguida, cria propriedades que correspondem aos tipos de dados dos valores inseridos.

Cada solicitação para a API do Coletor de Dados deve incluir um cabeçalho **Log-Type** com o nome do tipo de registro. O sufixo **_CL** é acrescentado automaticamente ao nome inserido para distingui-lo de outros tipos de log como um log personalizado. Por exemplo, se você inserir o nome **MyNewRecordType**, o Azure Monitor criará um registro com o tipo **MyNewRecordType_CL**. Isso ajuda a garantir que não haja nenhum conflito entre os nomes de tipo criados pelo usuário e aqueles fornecidos nas soluções atuais ou futuras da Microsoft.

Para identificar o tipo de dados de uma propriedade, o Azure Monitor adiciona um sufixo ao nome da propriedade. Se uma propriedade contiver um valor nulo, ela não será incluída no registro. Esta tabela lista o tipo de dados de propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Data/hora |_t |
| GUID (armazenado como uma cadeia de caracteres) |_g |

> [!NOTE]
> Valores de cadeia de caracteres que parecem ser GUIDs receberão o sufixo _g e formatados como um GUID, mesmo que o valor de entrada não inclua traços. Por exemplo, "8145d822-13a7-44ad-859c-36f31a84f6dd" e "8145d82213a744ad859c36f31a84f6dd" serão armazenados como "8145d822-13a7-44ad-859c-36f31a84f6dd". As únicas diferenças entre essa e outra cadeia de caracteres são os _g no nome e na inserção de traços, se eles não forem fornecidos na entrada. 

O tipo de dados que o Azure Monitor usa para cada propriedade depende se o tipo de registro para o novo registro já existe.

* Se o tipo de registro não existir, o Azure Monitor criará um novo usando a inferência de tipos JSON para determinar o tipo de dados de cada propriedade para o novo registro.
* Se o tipo de registro existir, o Azure Monitor tentará criar um novo registro com base nas propriedades existentes. Se o tipo de dados de uma propriedade no novo registro não corresponder e não puder ser convertido no tipo existente, ou se o registro incluir uma propriedade que não existe, o Azure Monitor criará uma nova propriedade que tenha o sufixo relevante.

Por exemplo, esta entrada de envio criaria um registro com três propriedades, **number_d**, **boolean_b** e **string_s**:

![Registro de exemplo 1](media/data-collector-api/record-01.png)

Se você enviasse essa próxima entrada, com todos os valores formatados como cadeias de caracteres, as propriedades não seriam alteradas. Esses valores podem ser convertidos para tipos de dados existentes:

![Registro de exemplo 2](media/data-collector-api/record-02.png)

Mas, se você fizesse esse próximo envio, o Azure Monitor criaria as novas propriedades **boolean_d** e **string_d**. Esses valores não podem ser convertidos:

![Registro de exemplo 3](media/data-collector-api/record-03.png)

Se você enviasse a seguinte entrada, antes que o tipo de registro fosse criado, o Azure Monitor criaria um registro com três propriedades, **number_s**, **boolean_s** e **string_s**. Nesta entrada, cada um dos valores iniciais é formatado como uma cadeia de caracteres:

![Registro de exemplo 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Propriedades reservadas
As propriedades a seguir são reservadas e não devem ser usadas em um tipo de registro personalizado. Você receberá um erro se sua carga incluir qualquer um desses nomes de propriedade.

- locatário

## <a name="data-limits"></a>Limites de dados
Há algumas restrições acerca dos dados publicados na API de Coleta de Dados do Azure Monitor.

* Máximo de 30 MB por post na API do Coletor de Dados do Azure Monitor. Este é um limite de tamanho para um único post. Se os dados de uma única postagem excederem 30 MB, será necessário dividi-los em partes menores e enviá-los simultaneamente.
* Limite máximo de 32 KB para valores de campo. Se o valor do campo for maior do que 32 KB, os dados serão truncados.
* O número máximo recomendado de campos para um determinado tipo é 50. Este é um limite prático de uma perspectiva de experiência de pesquisa e usabilidade.  
* Uma tabela em um espaço de trabalho do Log Analytics apenas dá suporte para até 500 colunas (referido como um campo neste artigo). 
* O número máximo de caracteres para o nome da coluna é 500.

## <a name="return-codes"></a>Códigos de retorno
O código de status HTTP 200 significa que a solicitação foi recebida para processamento. Isso indica que a operação foi concluída com êxito.

Esta tabela lista o conjunto completo de códigos de status que o serviço pode retornar:

| Código | Status | Código do erro | Descrição |
|:--- |:--- |:--- |:--- |
| 200 |OK | |A solicitação foi aceita com êxito. |
| 400 |Solicitação incorreta |InactiveCustomer |O workspace foi fechado. |
| 400 |Solicitação incorreta |InvalidApiVersion |A versão da API que você especificou não foi reconhecida pelo serviço. |
| 400 |Solicitação incorreta |InvalidCustomerId |A ID do workspace especificada é inválida. |
| 400 |Solicitação incorreta |InvalidDataFormat |JSON inválido foi enviado. O corpo da resposta pode conter mais informações sobre como resolver o erro. |
| 400 |Solicitação incorreta |InvalidLogType |O tipo de log especificado continha caracteres especiais ou numéricos. |
| 400 |Solicitação incorreta |MissingApiVersion |A versão da API não foi especificada. |
| 400 |Solicitação incorreta |MissingContentType |O tipo de conteúdo não foi especificado. |
| 400 |Solicitação incorreta |MissingLogType |O tipo de log de valor necessário não foi especificado. |
| 400 |Solicitação incorreta |UnsupportedContentType |O tipo de conteúdo não foi definido como **application/json**. |
| 403 |Proibido |InvalidAuthorization |O serviço falhou ao autenticar a solicitação. Verifique se a chave de conexão e a ID do workspace são válidos. |
| 404 |Não encontrado | | A URL fornecida está incorreta ou a solicitação é muito grande. |
| 429 |Número Excessivo de Solicitações | | O serviço está recebendo um alto volume de dados da sua conta. Tente fazer novamente a solicitação. |
| 500 |Erro interno do servidor |UnspecifiedError |O serviço encontrou um erro interno. Tente novamente a solicitação. |
| 503 |Serviço indisponível |ServiceUnavailable |No momento, o serviço está indisponível para receber solicitações. Tente novamente a sua solicitação. |

## <a name="query-data"></a>Consultar dados
Para consultar dados enviados pela API do Coletor de Dados HTTP do Azure Monitor, pesquise registros com **Tipo** que seja igual ao valor **LogType** especificado, anexado com **_CL**. Por exemplo, se você usasse **MyCustomLog**, retornariam todos os registros com `MyCustomLog_CL`.

## <a name="sample-requests"></a>Solicitações de exemplo
Nas próximas seções, você encontrará exemplos de como enviar dados para a API do Coletor de Dados HTTP do Azure Monitor, usando diferentes linguagens de programação.

Para cada exemplo, realize essas etapas para definir as variáveis para o cabeçalho de autorização:

1. No portal do Azure, localize seu espaço de trabalho do Log Analytics.
2. Selecione **Gerenciamento de agentes**.
2. À direita da **ID do Workspace**, selecione o ícone de cópia e cole a ID como o valor da variável **Customer ID**.
3. À direita da **Chave Primária**, selecione o ícone de cópia e cole a ID como o valor da variável **Shared Key**.

Como alternativa, você pode alterar as variáveis para o tipo de log e dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemplo de C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Exemplo de Python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Exemplo do Python 3
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Exemplo de Java

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternativas e considerações
Embora a API do coletor de dados deva abranger a maioria das suas necessidades de coletar dados de forma livre nos logs do Azure, há instâncias em que uma alternativa pode ser necessária para superar algumas das limitações da API. Todas as suas opções são as seguintes principais considerações incluídas:

| Alternativa | Descrição | Mais indicado para |
|---|---|---|
| [Eventos personalizados](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): ingestão baseada em SDK nativo no Application insights | Application Insights, normalmente instrumentados por meio de um SDK dentro de seu aplicativo, oferece a capacidade de enviar dados personalizados por meio de eventos personalizados. | <ul><li> Dados que são gerados em seu aplicativo, mas não são selecionados pelo SDK por meio de um dos tipos de dados padrão (solicitações, dependências, exceções e assim por diante).</li><li> Dados que costumam ser correlacionados a outros dados de aplicativo no Application Insights </li></ul> |
| API do coletor de dados em logs de Azure Monitor | A API do coletor de dados em logs de Azure Monitor é uma maneira completamente aberta de ingerir dados. Todos os dados formatados em um objeto JSON podem ser enviados aqui. Depois de enviado, ele será processado e estará disponível nos logs para serem correlacionados a outros dados nos logs ou a outros dados de Application Insights. <br/><br/> É bem fácil carregar os dados como arquivos em um blob de blob do Azure, de onde esses arquivos serão processados e carregados em Log Analytics. Consulte [este](./create-pipeline-datacollector-api.md) artigo para obter uma implementação de exemplo desse pipeline. | <ul><li> Dados que não são necessariamente gerados em um aplicativo instrumentado dentro de Application Insights.</li><li> Os exemplos incluem tabelas de pesquisa e de fatos, dados de referência, estatísticas pré-configuradas e assim por diante. </li><li> Destinado a dados que serão referenciados entre outros dados de Azure Monitor (Application Insights, outros tipos de dados de logs, central de segurança, informações de contêiner/VMs e assim por diante). </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | O Azure Data Explorer (ADX) é a plataforma de dados que capacita Application Insights análise e Azure Monitor logs. Agora disponível ("GA"), usar a plataforma de dados em sua forma bruta oferece flexibilidade total (mas exigindo a sobrecarga de gerenciamento) sobre o cluster (kubernetes RBAC, a taxa de retenção, o esquema e assim por diante). O ADX fornece muitas [Opções de ingestão](/azure/data-explorer/ingest-data-overview#ingestion-methods) , incluindo arquivos [CSV, TSV e JSON](/azure/kusto/management/mappings) . | <ul><li> Dados que não serão correlacionados a outros dados em Application Insights ou logs. </li><li> Os dados que exigem recursos avançados de ingestão ou processamento não estão disponíveis atualmente nos logs de Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Próximas etapas
- Use a [API de Pesquisa de Logs](./log-query-overview.md) para recuperar dados do espaço de trabalho do Log Analytics.

- Saiba mais sobre como [criar um pipeline de dados com a API do Coletor de Dados ](create-pipeline-datacollector-api.md) usando fluxo de trabalho de Aplicativos Lógicos para Azure Monitor.