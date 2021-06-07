---
title: Usar as APIs REST para fazer o CI/CD para Azure Stream Analytics no IoT Edge
description: Saiba como implementar um pipeline de implantação e integração contínua para o Azure Stream Analytics usando as APIs REST.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c3f776ad0996fa0b7422f0fca2d899a35e853d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016126"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementar CI/CD para Stream Analytics no IoT Edge usando APIs

É possível habilitar implantação e integração contínua para trabalhos do Azure Stream Analytics usando APIs REST. Este artigo fornece exemplos sobre quais APIs usar e como usá-las. Não há suporte para APIs REST no Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Chamar APIs a partir de ambientes diferentes

APIs REST podem ser chamadas do Linux e do Windows. Os comandos a seguir demonstram a sintaxe adequada para a chamada à API. O uso da API específica será descrito em seções posteriores deste artigo.

### <a name="linux"></a>Linux

Para o Linux, você pode usar os comandos `Curl` ou `Wget`:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Para o Windows, use o PowerShell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Criar um trabalho ASA no Edge 
 
Para criar o trabalho do Stream Analytics, chame o método PUT usando a API do Stream Analytics.

|Método|URL de Solicitação|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Exemplo de uso do comando **curl**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Exemplo de corpo da solicitação em JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Para obter mais informações, consulte a [Documentação da API](/rest/api/streamanalytics/).  
 
## <a name="publish-edge-package"></a>Publicar o pacote do Edge 
 
Para publicar um trabalho de Stream Analytics no IoT Edge, chame o método POST usando a API Edge Package Publish.

|Método|URL de Solicitação|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Essa operação assíncrona retorna um status de 202 até que o trabalho tenha sido publicado com êxito. O cabeçalho de resposta de localização contém o URI usado para obter o status do processo. Enquanto o processo está em execução, uma chamada para o URI no cabeçalho de localização retorna um status de 202. Enquanto o processo é finalizado, o URI no cabeçalho de localização retorna um status de 200. 

Exemplo de uma chamada de publicação de pacote do Edge usando **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Depois de fazer a chamada POST, você deve esperar uma resposta com um corpo vazio. Procure a URL localizada no HEAD da resposta e registre-o para uso posterior.
 
Exemplo da URL do HEAD da resposta:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Uma espera por um ou dois minutos antes de executar o comando a seguir para fazer uma chamada de API com a URL é encontrada no HEAD da resposta. Repita o comando se você não obtiver uma resposta 200.
 
Exemplo de como fazer a chamada à API com URL retornada usando **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A resposta inclui as informações necessárias para adicionar ao script de implantação do Edge. Os exemplos abaixo mostram quais informações você precisa coletar e onde adicioná-las no manifesto de implantação.
 
Exemplo de corpo de resposta após a publicação com êxito:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Exemplo do manifesto de implantação: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Após a configuração do manifesto de implantação, consulte [Implantar módulos do Azure IoT Edge com CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md) para a implantação.


## <a name="next-steps"></a>Próximas etapas 
 
* [O Stream Analytics do Azure no IoT Edge](stream-analytics-edge.md)
* [ASA no tutorial de IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Desenvolver trabalhos de Borda do Stream Analytics utilizando ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)