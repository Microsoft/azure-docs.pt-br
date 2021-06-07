---
title: Guia de início rápido da API REST do Assistente de Métricas
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/23/2020
ms.author: mbullwin
ms.openlocfilehash: c3269d771c43e6233459b9c194c1bab4f3602728
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445172"
---
## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Depois que tiver sua assinatura do Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Criar um recurso do Assistente de Métricas"  target="_blank"> crie um recurso do Assistente de Métricas </a> no portal do Azure para implantar sua instância do Assistente de Métricas.  
* A versão atual do [cURL](https://curl.haxx.se/). Várias opções de linha de comando são usadas neste artigo, que são indicadas na [documentação do cURL](https://curl.haxx.se/docs/manpage.html).
    * Os exemplos de BASH a seguir usam o caractere de continuação de linha `\`. Se o console ou o terminal usar um caractere de continuação de linha diferente, use esse caractere.

> [!TIP]
> * Você pode encontrar uma amostra de Python que chama a API REST no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/MetricsAdvisor).
> * Poderá levar de 10 a 30 minutos para que o recurso do Assistente de Métricas implante uma instância de serviço para seu uso. Clique em **Ir para o recurso** quando ele for implantado com êxito. Após a implantação, você pode começar a usar sua instância do Assistente de Métricas com o portal da Web e com a API REST. 
> * Você pode encontrar a URL para a API REST no portal do Azure, na seção **Visão geral** do recurso. Ele terá esta aparência:
>    * `https://<instance-name>.cognitiveservices.azure.com/`

Você precisará de duas chaves para começar a usar a API REST:

* A chave para seu recurso do Assistente de Métricas. Encontre-a na seção **Chaves e ponto de extremidade** de seu recurso no portal do Azure.
    * Posteriormente, você substituirá `Ocp-Apim-Subscription-Key` nos exemplos por essa chave. 
* A chave de API de sua instância do Assistente de Métricas. Encontre-a no portal da Web do Assistente de Métricas, em **Chaves de API** no menu de navegação à esquerda.
    * Posteriormente, você substituirá `x-api-key` nos exemplos por essa chave.

## <a name="add-a-data-feed-from-a-sample-or-data-source"></a>Adicionar um feed de dados de uma amostra ou fonte de dados

Para começar a monitorar seus dados de série temporal, você precisa adicionar um feed de dados. Para adicionar um feed de dados, você precisa fornecer um esquema de dados de acordo com o tipo e os parâmetros da fonte de dados. Salve o corpo da solicitação JSON abaixo em um arquivo chamado *body.json* e execute o comando cURL.

```json
{
        "dataSourceType": "SqlServer",
        "dataFeedName": "test_data_feed_00000001",
        "dataFeedDescription": "",
        "dataSourceParameter": {
            "connectionString": "Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
            "query": "select * from adsample3 where Timestamp = @StartTime"
        },
        "granularityName": "Daily",
        "granularityAmount": 0,
        "metrics": [
            {
                "metricName": "revenue",
                "metricDisplayName": "revenue",
                "metricDescription": ""
            },
            {
                "metricName": "cost",
                "metricDisplayName": "cost",
                "metricDescription": ""
            }
        ],
        "dimension": [
            {
                "dimensionName": "city",
                "dimensionDisplayName": "city"
            },
            {
                "dimensionName": "category",
                "dimensionDisplayName": "category"
            }
        ],
        "timestampColumn": "timestamp",
        "dataStartFrom": "2020-06-01T00:00:00.000Z",
        "startOffsetInSeconds": 0,
        "maxConcurrency": -1,
        "minRetryIntervalInSeconds": -1,
        "stopRetryAfterInSeconds": -1,
        "needRollup": "NoRollup",
        "fillMissingPointType": "SmartFilling",
        "fillMissingPointValue": 0,
        "viewMode": "Private",
        "admins": [
            "xxx@microsoft.com"
        ],
        "viewers": [
        ],
        "actionLinkTemplate": ""
}
```

O comando cURL é executado em um shell BASH. Edite esse comando com o nome de seu recurso, a chave do recurso e os valores JSON.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Exemplo de resposta

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/datafeeds/b5921405-8001-42b2-8746-004ddeeb780d
x-envoy-upstream-service-time: 564
apim-request-id: 4e4fe70b-d663-4fb7-a804-b9dc14ba02a3
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Thu, 03 Sep 2020 18:29:27 GMT
```
Na resposta acima, o cabeçalho **Local** é a URL do feed de dados que você criou e contém **dataFeedID**. 

Usando a URL acima, você pode consultar informações detalhadas sobre o feed de dados criado na etapa anterior. (Usaremos **metricID** nas informações do feed de dados nas etapas a seguir)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "dataFeedId":"90919c03-be13-4efa-86e5-aa9dc72764ce",
   "dataFeedName":"test_data_feed_00000007",
   "metrics":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "metricName":"cost",
         "metricDisplayName":"cost",
         "metricDescription":""
      },
      {
         "metricId":"82bbc63d-3739-4d57-b190-accb69721b6a",
         "metricName":"revenue",
         "metricDisplayName":"revenue",
         "metricDescription":""
      }
   ],
   "dimension":[
      {
         "dimensionName":"category",
         "dimensionDisplayName":"category"
      },
      {
         "dimensionName":"city",
         "dimensionDisplayName":"city"
      }
   ],
   "dataStartFrom":"2020-06-01T00:00:00Z",
   "dataSourceType":"SqlServer",
   "timestampColumn":"timestamp",
   "startOffsetInSeconds":0,
   "maxQueryPerMinute":30.0,
   "granularityName":"Daily",
   "granularityAmount":null,
   "allUpIdentification":null,
   "needRollup":"NoRollup",
   "fillMissingPointType":"SmartFilling",
   "fillMissingPointValue":0.0,
   "rollUpMethod":"None",
   "rollUpColumns":[
      
   ],
   "dataFeedDescription":"",
   "stopRetryAfterInSeconds":-1,
   "minRetryIntervalInSeconds":-1,
   "maxConcurrency":-1,
   "viewMode":"Private",
   "admins":[
      "xyz@microsoft.com"
   ],
   "viewers":[
      
   ],
   "creator":"xyz@microsoft.com",
   "status":"Active",
   "createdTime":"2020-09-08T08:39:28Z",
   "isAdmin":true,
   "actionLinkTemplate":"",
   "dataSourceParameter":{
      "connectionString":"Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
      "query":"select * from adsample3 where Timestamp = @StartTime"
   }
}
```



## <a name="check-ingestion-status"></a>Verificar o status da ingestão

Depois de adicionar o feed de dados, se desejar verificar o progresso de um trabalho de ingestão, você poderá verificar seu status. Salve o corpo da solicitação JSON abaixo em um arquivo chamado *body.json* e execute o comando cURL.

```json
{
  "startTime": "2020-01-01T00:00:00.0000000+00:00",
  "endTime": "2020-01-04T00:00:00.0000000+00:00"
}
```

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.


```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID/ingestionStatus/query \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```


#### <a name="example-response"></a>Exemplo de resposta

```json
{
  "@nextLink": "https://demo.example.com/datafeeds/01234567-8901-2345-6789-012345678901/ingestionStatus/query?$skip=3&$top=1",
  "value": [
    {
      "timestamp": "2020-09-03T00:00:00.0000000+00:00",
      "status": "Running",
      "message": ""
    },
    {
      "timestamp": "2020-09-02T00:00:00.0000000+00:00",
      "status": "Succeeded",
      "message": ""
    },
    {
      "timestamp": "2020-09-01T00:00:00.0000000+00:00",
      "status": "Failed",
      "message": "No valid record pulled from source for current timestamp 2020-01-01T00:00:00Z"
    }
  ]
}
```

##  <a name="configure-anomaly-detection-configuration"></a>Definir a configuração da detecção de anomalias

Embora uma configuração padrão seja aplicada automaticamente a cada métrica, você pode ajustar os modos de detecção usados nos dados. Salve o corpo da solicitação JSON abaixo em um arquivo chamado *body.json* e execute o comando cURL.

```json
{
        "name": "test_detection_config0000000001",
        "description": "string",
        "metricId": "8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
        "wholeMetricConfiguration": {
            "smartDetectionCondition": {
                "sensitivity": 100,
                "anomalyDetectorDirection": "Both",
                "suppressCondition": {
                    "minNumber": 1,
                    "minRatio": 1
                }
            }
        },
        "dimensionGroupOverrideConfigurations": [
        ],
        "seriesOverrideConfigurations": [
        ]
}
```

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Exemplo de resposta

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations/6a977d61-f0f5-488a-a162-2feb4643ae09
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

No cabeçalho **Local** acima, está a URL do novo recurso criado (configuração de detecção). 

Usando a URL no cabeçalho **Local** acima, você pode consultar a configuração de detecção criada (usaremos **anomalyDetectionConfigurationId** no conteúdo de resposta nas etapas a seguir)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations/REPLACE-WITH-YOUR-DETECTION-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
   "name":"test_detection_config0000000001",
   "description":"string",
   "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
   "wholeMetricConfiguration":{
      "smartDetectionCondition":{
         "sensitivity":100.0,
         "anomalyDetectorDirection":"Both",
         "suppressCondition":{
            "minNumber":1,
            "minRatio":1.0
         }
      }
   },
   "dimensionGroupOverrideConfigurations":[
      
   ],
   "seriesOverrideConfigurations":[
      
   ]
}
```



### <a name="configure-alert-configuration"></a>Definir a configuração de alerta

Antes de configurar o alerta, você precisa criar um gancho que será usado para notificar sobre ele. Há duas maneiras de ser notificado se um alerta for disparado, com um web hook ou por email. Você pode especificar uma dessas opções na configuração do gancho, como o tipo de gancho, ao criá-lo.

Salve o corpo da solicitação JSON abaixo em um arquivo chamado *body.json* e execute o comando cURL.

```json
{
        "hookType": "Webhook",
        "hookName": "test_web_hook000001",
        "description": "",
        "externalLink": "",
        "hookParameter": {
            "endpoint": "https://www.xxx.com/aaa",
            "username": "",
            "password": ""
        }
}
```

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/hooks \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Exemplo de resposta

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/hooks/34d677bd-0875-4760-8bf6-24d48abde7c3
x-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
x-envoy-upstream-service-time: 1640
apim-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 10:37:59 GMT
```

Usando a URL no cabeçalho **Local** acima, você pode consultar o web hook criado.

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/hooks/REPLACE-WITH-YOUR-HOOK-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "hookId":"34d677bd-0875-4760-8bf6-24d48abde7c3",
   "hookName":"test_web_hook000001",
   "hookType":"Webhook",
   "externalLink":"",
   "description":"",
   "admins":[
      "bowgong@microsoft.com"
   ],
   "hookParameter":{
      "endpoint":"https://www.xxx.com/aaa",
      "username":"",
      "password":"",
      "headers":{
         

      },
      "certificateKey":"",
      "certificatePassword":""

   }
}
```

Ao definir a configuração do alerta, você pode especificar a condição de detecção que pode ser usada para disparar o alerta. Salve o corpo da solicitação JSON abaixo em um arquivo chamado *body.json* e execute o comando cURL.

```json
{
        "name": "test_alert_config00000001",
        "description": "",
        "crossMetricsOperator": "AND",
        "hookIds": [
           "34d677bd-0875-4760-8bf6-24d48abde7c3" 
        ],
        "metricAlertingConfigurations": [
            {
                "anomalyDetectionConfigurationId": "6a977d61-f0f5-488a-a162-2feb4643ae09",
                "anomalyScopeType": "All",
                "severityFilter": {
                    "minAlertSeverity": "Low",
                    "maxAlertSeverity": "High"
                },
                "snoozeFilter": {
                    "autoSnooze": 0,
                    "snoozeScope": "Metric",
                    "onlyForSuccessive": true
                },
            }
        ]
}
```

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Exemplo de resposta

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/alert/anomaly/configurations/40004c91-6996-47c0-b8c8-fd20a8f4f0ab
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

No cabeçalho **Local** acima, está a URL do novo recurso criado (configuração de detecção). 

Usando a URL no cabeçalho **Local** acima, você pode consultar a configuração do alerta criado. (Usaremos **anomalyAlertingConfigurationId** na configuração do alerta nas etapas a seguir)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "anomalyAlertingConfigurationId":"40004c91-6996-47c0-b8c8-fd20a8f4f0ab",
   "name":"test_alert_config00000001",
   "description":"",
   "hookIds":[
      "34d677bd-0875-4760-8bf6-24d48abde7c3"
   ],
   "metricAlertingConfigurations":[
      {
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "anomalyScopeType":"All",
         "negationOperation":false,
         "severityFilter":{
            "minAlertSeverity":"Low",
            "maxAlertSeverity":"High"
         },
         "snoozeFilter":{
            "autoSnooze":0,
            "snoozeScope":"Metric",
            "onlyForSuccessive":true
         }
      }
   ]
}
```

### <a name="query-anomaly-detection-results"></a>Consultar os resultados da detecção de anomalias

Há diferentes maneiras de obter o resultado da detecção. Por exemplo, você pode consultar ativamente o resultado da detecção periodicamente usando a configuração de detecção criada ou pode ser notificado por meio de alertas e, então, usar os alertas para consultar as anomalias correspondentes.

O exemplo a seguir mostra como consultar o alerta e como usá-lo para consultar anomalias relacionadas a ele.

#### <a name="query-alert"></a>Consultar alerta

Você pode usar a configuração do alerta criada na etapa acima para consultar o alerta.

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/query \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "value":[
      {
         "alertId":"17465dcc000",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.532Z",
         "modifiedTime":"2020-09-08T19:12:46.588Z"
      }
   ],
   "@nextLink":null
}
```

Na resposta acima, recebemos um alerta. Usando a **alertID**, é possível consultar todas as anomalias relacionadas que causaram esse alerta.

(Outra maneira de obter o alerta é configurar o web hook e receber um alerta passivo quando ele for encontrado)

#### <a name="query-anomalies-using-alertid"></a>Consultar anomalias usando alertID

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/REPLACE-WITH-YOUR-ALERTID/anomalies \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Exemplo de resposta

```json
{
   "value":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Caucasian Fir"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      }
   ],
   "@nextLink":null
}
```
