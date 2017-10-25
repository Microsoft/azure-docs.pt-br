---
title: Passo a passo da API REST de Monitoramento do Azure | Microsoft Docs
description: "Como autenticar solicitações e usar a API REST do Azure Monitor para recuperar as definições de métrica e os valores de métrica disponíveis."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ab522b444c234e1159acfea1780bae1801c4d047
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Passo a passo da API REST de Monitoramento do Azure
Este artigo mostra como executar autenticação para que o seu código possa usar a [referência de API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A API do Azure Monitor possibilita recuperar de forma programática as definições de métrica padrão, a granularidade e os valores de métrica disponíveis. Os dados podem ser salvos em um armazenamento de dados separado, como o Banco de Dados SQL do Azure, o Azure Cosmos DB ou o Azure Data Lake. A partir daí análises adicionais podem ser executadas conforme necessário.

Além de trabalhar com vários pontos de dados de métrica, a API do Monitor também possibilita listar regras de alerta, exibir logs de atividades e muito mais. Para obter uma lista completa das operações disponíveis, consulte a [referência da API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticando solicitações do Azure Monitor
A primeira etapa é autenticar a solicitação.

Todas as tarefas executadas em relação à API do Azure Monitor usam o modelo de autenticação do Azure Resource Manager. Portanto, todas as solicitações devem ser autenticadas com o Azure Active Directory (Azure AD). Uma abordagem para autenticar o aplicativo cliente é criar uma entidade de serviço do Azure AD e recuperar o token de autenticação (JWT). O script de exemplo a seguir demonstra como criar uma entidade de serviço do Azure AD por meio do PowerShell. Para obter instruções mais detalhadas, consulte a documentação sobre como [usar o Azure PowerShell para criar uma entidade de serviço para acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). Também é possível [criar uma entidade de serviço por meio do portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Azure Monitor, o aplicativo cliente deve usar a entidade de serviço criada anteriormente para a autenticação. O script do PowerShell de exemplo a seguir mostra uma abordagem, o uso da [ADAL](../active-directory/active-directory-authentication-libraries.md) (Biblioteca de Autenticação do Active Directory) para obter o token de autenticação JWT. O token JWT é passado como parte de um parâmetro de autorização HTTP em solicitações para a API REST do Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Após a autenticação, as consultas podem então ser executadas na API REST do Azure Monitor. Há duas consultas úteis:

1. Listar as definições de métricas para um recurso
2. Recuperar os valores de métrica

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperar as definições de métrica (API multidimensional)

Use a [API REST de definições da Métrica do Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) para acessar a lista de métricas disponíveis para um serviço.

**Método**: GET

**URI de Solicitação**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para recuperar as definições de métrica de uma conta do Armazenamento do Azure, a solicitação será exibida da seguinte maneira:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Para recuperar as definições de métrica usando as API REST das métricas multidimensionais do Azure Monitor, use “2017-05-01-preview” como a versão de API.
>
>

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo: (observe que a segunda métrica tem dimensões)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Recuperar valores de dimensão (API multidimensional)
Depois que as definições de métrica disponíveis forem conhecidas, poderá haver algumas métricas com dimensões. Antes de consultar a métrica, talvez você deseje descobrir o intervalo de valores que uma dimensão tem. De acordo com esses valores de dimensão, você pode então optar por filtrar ou segmentar as métricas com base nos valores de dimensão enquanto consulta as métricas. Use o “valor” do nome da métrica (não o “valor localizado”) para todas as solicitações de filtragem (por exemplo, recuperar os pontos de dados da métrica “Tempo de CPU” e “Solicitações”). Se nenhum filtro for especificado, a métrica padrão será retornada.

> [!NOTE]
> Para recuperar os valores de dimensão usando a API REST do Azure Monitor, use “2017-05-01-preview” como a versão de API.
>
>

**Método**: GET

**URI de solicitação**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

Por exemplo, para recuperar a lista de valores possíveis de “dimensão de Nome da API” para a métrica “Transações” durante determinado intervalo de tempo, a solicitação será exibida da seguinte maneira:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperar valores de métrica (API multidimensional)
Depois que as definições de métrica disponíveis e os possíveis valores de dimensão forem conhecidos, é possível recuperar os valores de métrica relacionados. Use o “value” do nome da métrica (não o “localizedValue”) para todas as solicitações de filtragem. Se nenhum filtro de dimensão for especificado, a métrica agregada acumulada será retornada.

> [!NOTE]
> Para recuperar os valores de métrica multidimensional usando a API REST do Azure Monitor, use “2017-05-01-preview” como a versão de API.
>
>

**Método**: GET

**URI de solicitação**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregation=*{aggreation}*&api-version=*{apiVersion}*

Por exemplo, para recuperar os valores de métrica da métrica de armazenamento “Transações” durante um intervalo de 5 minutos, para todas as transações para o nome de API “GetBlobProperties”, a solicitação será exibida da seguinte maneira:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Recuperar as definições de métrica
Use a [API REST de definições da Métrica do Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acessar a lista de métricas disponíveis para um serviço.

**Método**: GET

**URI de solicitação**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para recuperar as definições de métrica para um Aplicativo Lógico do Azure, a solicitação será exibida da seguinte maneira:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Para recuperar as definições de métricas usando a API REST do Azure Monitor, use "2016-03-01" como a versão de API.
>
>

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Para obter mais informações, consulte a documentação [Listar as definições de métricas para um recurso na API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperar valores de métrica
Depois que as definições de métrica disponíveis são conhecidas, é possível recuperar os valores de métrica relacionados. Use o “valor” do nome da métrica (não o “valor localizado”) para todas as solicitações de filtragem (por exemplo, recuperar os pontos de dados da métrica “Tempo de CPU” e “Solicitações”). Se nenhum filtro for especificado, a métrica padrão será retornada.

> [!NOTE]
> Para recuperar os valores de métrica usando a API REST do Azure Monitor, use “2016-09-01” como a versão de API.
>
>

**Método**: GET

**URI de solicitação**: https://management.azure.com/subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/*{namespace-do-provedor-recursos}*/*{tipo-de-recurso}*/*{nome-do-recurso}*/providers/microsoft.insights/metrics?$filter=*{filtro}*&api-version=*{versão-da-api}*

Por exemplo, para recuperar os pontos de dados da métrica RunsSucceeded para o intervalo de tempo determinado e para um intervalo de agregação de 1 hora, a solicitação seria a seguinte:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Para recuperar vários pontos de dados ou de agregação, adicione os nomes de definição da métrica e os tipos de agregação para o filtro, conforme mostrado no exemplo a seguir:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Use o ARMClient
Outra abordagem é usar o [ARMClient](https://github.com/projectkudu/armclient) no computador Windows. O ARMClient cuida da autenticação do Azure AD (e do token JWT resultante) automaticamente. As seguintes etapas descrevem o uso do ARMClient para recuperar dados de métrica:

1. Instalar [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/armclient).
2. Em uma janela de terminal, digite *armclient.exe login*. Se você fizer isso, deverá fazer logon no Azure.
3. Digite *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Digite *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Por exemplo, para recuperar as definições de métrica para um Aplicativo Lógico específico, emita o seguinte comando:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Recuperar a ID do recurso
Usar a API REST realmente pode ajudar a entender as definições de métrica disponíveis, granularidade e valores relacionados. Essas informações são úteis ao usar a [Biblioteca de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

No código anterior, a ID do recurso a ser usada é o caminho completo para o recurso do Azure desejado. Por exemplo, para consultar um aplicativo Web do Azure, a ID do recurso seria:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista a seguir contém alguns exemplos de formatos de ID do recurso para vários recursos do Azure:

* **Hub IoT** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Devices/IotHubs/*{nome-do-hub-iot}*
* **Pool SQL Elástico** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **Banco de Dados SQL (v12)** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Sql/servers/*{nome-do-servidor}*/databases/*{nome-do-banco-de-dados}*
* **Barramento de Serviço** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Conjuntos de dimensionamento de máquinas virtuais** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VMs** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Hubs de Eventos** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Há abordagens alternativas para recuperar a ID do recurso, incluindo o uso do Azure Resource Manager, exibindo o recurso desejado no portal do Azure e por meio do PowerShell ou da CLI do Azure.

### <a name="azure-resource-explorer"></a>Gerenciador de Recursos do Azure
Para localizar a ID do recurso para um recurso desejado, uma abordagem útil é usar a ferramenta [Azure Resource Manager](https://resources.azure.com) . Navegue até o recurso desejado e, em seguida, verifique a ID exibida, conforme a tela a seguir:

![Alt "Explorador de Recursos do Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure
A ID do recurso também pode ser obtida no portal do Azure. Para fazer isso, navegue até o recurso desejado e, em seguida, selecione Propriedades. A ID do Recurso é exibida na seção Propriedades, conforme visto na seguinte captura de tela:

![Alt "ID do recurso exibido na folha de Propriedades no portal do Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
A ID do recurso também pode ser recuperada usando cmdlets do Azure PowerShell. Por exemplo, para obter a ID do recurso de um Aplicativo Lógico do Azure, execute o cmdlet Get-AzureLogicApp, como no seguinte exemplo:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deverá ser semelhante ao seguinte exemplo:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>CLI do Azure
Para recuperar a ID do recurso de uma conta do Armazenamento do Azure usando a CLI do Azure, execute o comando “az storage account show”, conforme mostrado no seguinte exemplo:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deverá ser semelhante ao seguinte exemplo:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Os Aplicativos Lógicos do Azure ainda não estão disponíveis por meio da CLI do Azure e, portanto, uma conta do Armazenamento do Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperar dados do Log de Atividades
Além de definições de métrica e valores relacionados, também é possível usar a API REST do Azure Monitor para recuperar outros insights interessantes relacionados aos recursos do Azure. Por exemplo, é possível consultar os dados do [log de atividades](https://msdn.microsoft.com/library/azure/dn931934.aspx) . O exemplo a seguir demonstra como usar o API REST do Azure Monitor para consultar dados de log de atividade dentro de um intervalo de datas específico para uma assinatura do Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Próximas etapas
* Verifique a [Visão geral do monitoramento](monitoring-overview.md).
* Visualize as [Métricas compatíveis com o Azure Monitor](monitoring-supported-metrics.md).
* Verifique a [referência da API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Verifique a [Biblioteca de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

