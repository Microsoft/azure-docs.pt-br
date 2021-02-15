---
title: Passo a passo da API REST de Monitoramento do Azure
description: Como autenticar solicitações e usar a API REST do Azure Monitor para recuperar as definições de métrica e os valores de métrica disponíveis.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: 500d5242d5185a8014283918c1f3a22c5c22cf48
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012009"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Passo a passo da API REST de Monitoramento do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra como executar autenticação para que o seu código possa usar a [referência de API REST do Monitor do Microsoft Azure](/rest/api/monitor/).

A API do Azure Monitor possibilita recuperar de forma programática as definições de métrica padrão, a granularidade e os valores de métrica disponíveis. Os dados podem ser salvos em um armazenamento de dados separado, como o Banco de Dados SQL do Azure, o Azure Cosmos DB ou o Azure Data Lake. A partir daí análises adicionais podem ser executadas conforme necessário.

Além de trabalhar com vários pontos de dados de métrica, a API do Monitor também possibilita listar regras de alerta, exibir logs de atividades e muito mais. Para obter uma lista completa das operações disponíveis, consulte a [referência da API REST do Monitor do Microsoft Azure](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Autenticando solicitações do Azure Monitor

A primeira etapa é autenticar a solicitação.

Todas as tarefas executadas em relação à API do Azure Monitor usam o modelo de autenticação do Azure Resource Manager. Portanto, todas as solicitações devem ser autenticadas com o Azure Active Directory (Azure AD). Uma abordagem para autenticar o aplicativo cliente é criar uma entidade de serviço do Azure AD e recuperar o token de autenticação (JWT). O script de exemplo a seguir demonstra como criar uma entidade de serviço do Azure AD por meio do PowerShell. Para obter instruções mais detalhadas, consulte a documentação sobre como [usar o Azure PowerShell para criar uma entidade de serviço para acessar recursos](/powershell/azure/create-azure-service-principal-azureps). Também é possível [criar uma entidade de serviço por meio do portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Azure Monitor, o aplicativo cliente deve usar a entidade de serviço criada anteriormente para a autenticação. O script do PowerShell de exemplo a seguir mostra uma abordagem, o uso da [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (Biblioteca de Autenticação do Active Directory) para obter o token de autenticação JWT. O token JWT é passado como parte de um parâmetro de autorização HTTP em solicitações para a API REST do Azure Monitor.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

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

> [!NOTE]
> Para obter informações adicionais sobre como autenticar com a API REST do Azure, consulte a [referência da API REST do Azure](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperar as definições de métrica (API multidimensional)

Use a [API REST de definições da Métrica do Azure Monitor](/rest/api/monitor/metricdefinitions) para acessar a lista de métricas disponíveis para um serviço.

**Método**: GET

**URI de solicitação**: https: \/ \/ Management.Azure.com/subscriptions/*{SubscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType}* / *{resourceName}*/Providers/Microsoft.insights/metricDefinitions? API-Version =*{apiVersion}*

Por exemplo, para recuperar as definições de métrica de uma conta do Armazenamento do Azure, a solicitação será exibida da seguinte maneira:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Para recuperar as definições de métrica usando as API REST das métricas multidimensionais do Azure Monitor, use "2018-01-01" como a versão de API.
>
>

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo: (observe que a segunda métrica tem dimensões)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
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

Depois que as definições de métrica disponíveis forem conhecidas, poderá haver algumas métricas com dimensões. Antes de consultar a métrica, talvez você deseje descobrir o intervalo de valores que uma dimensão tem. De acordo com esses valores de dimensão, você pode então optar por filtrar ou segmentar as métricas com base nos valores de dimensão enquanto consulta as métricas.  Use a [API REST de Métricas do Azure Monitor](/rest/api/monitor/metrics) para isso.

Use o “value” do nome da métrica (não o “localizedValue”) para todas as solicitações de filtragem. Se nenhum filtro for especificado, a métrica padrão será retornada. O uso dessa API permite que apenas uma dimensão tenha um filtro curinga.

> [!NOTE]
> Para recuperar os valores de dimensão usando a API REST do Azure Monitor, use "2018-01-01" como a versão de API.
>
>

**Método**: GET

**URI da solicitação**: https \: //Management.Azure.com/subscriptions/*{Subscription-ID}*/resourceGroups/*{nome-do-grupo-de-recursos}*/Providers/*{Resource-Provider-namespace}* / *{tipo de recurso}* / *{nome-do-recurso}*/Providers/Microsoft.insights/Metrics de métricas = {*métrica}*&TimeSpan =*{StartTime/EndTime}*&$Filter =*{filtro}*&ResultType *{apiVersion}* = metadados&API-Version

Por exemplo, para recuperar a lista de valores de dimensão que foram emitidos para a 'dimensão nome da API' para a métrica 'Transações', em que a dimensão GeoType = 'Primary' durante o intervalo de tempo especificado, a solicitação seria:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
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
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperar valores de métrica (API multidimensional)

Depois que as definições de métrica disponíveis e os possíveis valores de dimensão forem conhecidos, é possível recuperar os valores de métrica relacionados.  Use a [API REST de Métricas do Azure Monitor](/rest/api/monitor/metrics) para isso.

Use o “value” do nome da métrica (não o “localizedValue”) para todas as solicitações de filtragem. Se nenhum filtro de dimensão for especificado, a métrica agregada acumulada será retornada. Se uma consulta de métrica retorna várias séries temporais, você pode usar os parâmetros de consulta 'Top' e 'OrderBy' para retornar uma lista ordenada limitada de série temporal.

> [!NOTE]
> Para recuperar os valores de métrica multidimensional usando a API REST do Azure Monitor, use "2018-01-01" como a versão de API.
>
>

**Método**: GET

**URI da solicitação**: https \/ :/Management.Azure.com/subscriptions/*{Subscription-ID}*/resourceGroups/*{nome-do-grupo-de-recursos}*/Providers/*{Resource-Provider-namespace}* / *{tipo de recurso}* / *{nome-do-recurso}*/Providers/Microsoft.insights/Metrics? metricnames =*{métrica}*&TimeSpan =*{StartTime/EndTime}*&$Filter =*{filtro}*&intervalo =*{timegranular}*&agregação =*{aggreation}*&API-Version =*{apiVersion}*

Por exemplo, para recuperar as 3 maiores APIs, da maior para a menor, pelo número de 'Transactions' durante um intervalo de 5 minutos, em que o GeotType era 'Primary', a solicitação seria:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
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
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Recuperar as definições de métrica

Use a [API REST de definições da Métrica do Azure Monitor](/rest/api/monitor/metricdefinitions) para acessar a lista de métricas disponíveis para um serviço.

**Método**: GET

**URI de solicitação**: https: \/ \/ Management.Azure.com/subscriptions/*{SubscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType}* / *{resourceName}*/Providers/Microsoft.insights/metricDefinitions? API-Version =*{apiVersion}*

Por exemplo, para recuperar as definições de métrica para um Aplicativo Lógico do Azure, a solicitação será exibida da seguinte maneira:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Para recuperar as definições de métricas usando a API REST do Azure Monitor, use "2016-03-01" como a versão de API.
>
>

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```json
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

Para obter mais informações, consulte a documentação [Listar as definições de métricas para um recurso na API REST do Azure Monitor](/rest/api/monitor/metricdefinitions) .

## <a name="retrieve-metric-values"></a>Recuperar valores de métrica

Depois que as definições de métrica disponíveis são conhecidas, é possível recuperar os valores de métrica relacionados. Use o “valor” do nome da métrica (não o “valor localizado”) para todas as solicitações de filtragem (por exemplo, recuperar os pontos de dados da métrica “Tempo de CPU” e “Solicitações”). Se nenhum filtro for especificado, a métrica padrão será retornada.

> [!NOTE]
> Para recuperar os valores de métrica usando a API REST do Azure Monitor, use “2016-09-01” como a versão de API.
>
>

**Método**: GET

**URI de solicitação**: `https:\//management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Por exemplo, para recuperar os pontos de dados da métrica RunsSucceeded para o intervalo de tempo determinado e para um intervalo de agregação de 1 hora, a solicitação seria a seguinte:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```json
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

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante será semelhante ao seguinte exemplo:

```json
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

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Recuperar a ID do recurso

Usar a API REST realmente pode ajudar a entender as definições de métrica disponíveis, granularidade e valores relacionados. Essas informações são úteis ao usar a [Biblioteca de Gerenciamento do Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).

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

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Para localizar a ID do recurso para um recurso desejado, uma abordagem útil é usar a ferramenta [Azure Resource Manager](https://resources.azure.com) . Navegue até o recurso desejado e, em seguida, verifique a ID exibida, conforme a tela a seguir:

![Alt "Explorador de Recursos do Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure

A ID do recurso também pode ser obtida no portal do Azure. Para fazer isso, navegue até o recurso desejado e, em seguida, selecione Propriedades. A ID do Recurso é exibida na seção Propriedades, conforme visto na seguinte captura de tela:

![Alt "ID do recurso exibido na folha de Propriedades no portal do Azure"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

A ID do recurso também pode ser recuperada usando cmdlets do Azure PowerShell. Por exemplo, para obter a ID do recurso de um Aplicativo Lógico do Azure, execute o cmdlet Get-AzureLogicApp, como no seguinte exemplo:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deverá ser semelhante ao seguinte exemplo:

```output
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

Para recuperar a ID de recurso para uma conta de armazenamento do Azure usando o CLI do Azure, execute o `az storage account show` comando, conforme mostrado no exemplo a seguir:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deverá ser semelhante ao seguinte exemplo:

```json
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

Além de definições de métrica e valores relacionados, também é possível usar a API REST do Azure Monitor para recuperar outros insights interessantes relacionados aos recursos do Azure. Por exemplo, é possível consultar os dados do [log de atividades](/rest/api/monitor/activitylogs) . As solicitações de exemplo a seguir usam a API REST Azure Monitor para consultar o log de atividades.

Obter logs de atividades com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obter logs de atividades com filtro e selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividades com selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividades sem filtro ou selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Próximas etapas

* Verifique a [Visão geral do monitoramento](../overview.md).
* Visualize as [Métricas compatíveis com o Azure Monitor](metrics-supported.md).
* Verifique a [referência da API REST do Monitor do Microsoft Azure](/rest/api/monitor/).
* Verifique a [Biblioteca de Gerenciamento do Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).

