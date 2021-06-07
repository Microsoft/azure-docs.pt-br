---
title: Guia de início rápido do Assistente de Métricas para C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 31cdb1529ce06906aef2f546ab1c173361e7d779
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444881"
---
[Documentação de referência](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois que tiver sua assinatura do Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Criar um recurso do Assistente de Métricas"  target="_blank"> crie um recurso do Assistente de Métricas </a> no portal do Azure para implantar sua instância do Assistente de Métricas.  
* Seu Banco de Dados SQL com alguns dados de série temporal.
   
> [!TIP]
> * Encontre exemplos do Assistente de Métricas para .NET no [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * Poderá levar de 10 a 30 minutos para que o recurso do Assistente de Métricas implante uma instância de serviço para seu uso. Clique em **Ir para o recurso** quando ele for implantado com êxito. Após a implantação, você pode começar a usar sua instância do Assistente de Métricas com o portal da Web e com a API REST. 
> * Você pode encontrar a URL para a API REST no portal do Azure, na seção **Visão geral** do recurso. Ele terá a seguinte aparência:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Depois de criar um projeto, instale a biblioteca de clientes clicando com o botão direito do mouse na solução do projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Azure.AI.MetricsAdvisor`. Selecione a versão `1.0.0-beta.2` e, em seguida, **Instalar**. 

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `metrics-advisor-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Se você estiver usando um IDE diferente do Visual Studio, instale a biblioteca de clientes do Assistente de Métricas para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.2
```

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), que contém os exemplos de código neste início rápido.

No diretório do projeto, abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

No método `Main()` do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Elas serão criadas posteriormente.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modelo de objeto

As classes a seguir lidam com alguns dos principais recursos do SDK do Assistente de Métricas para C#.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Usada para**: <br> – Listar incidentes <br> – Listar a causa raiz de incidentes <br> – Recuperar dados de série temporal originais e dados de série temporal enriquecidos pelo serviço. <br> – Listar alertas <br> – Adicionar comentários para ajustar seu modelo |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Permite a você:** <br> – Gerenciar feeds de dados <br> – Definir as configurações de detecção de anomalias <br> – Definir as configurações de alerta de anomalias <br> – Gerenciar ganchos  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **O que o Assistente de Métricas ingere da fonte de dados. Um `DataFeed` contém linhas de:** <br> – Carimbos de data/hora <br> – Zero ou mais dimensões <br> – Uma ou mais medidas  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| Uma `DataFeedMetric` é uma medida quantificável usada para monitorar e avaliar o status de um processo empresarial específico. Pode ser uma combinação de vários valores de série temporal divididos em dimensões. Por exemplo, uma métrica de integridade da Web pode conter dimensões para contagem de usuários e mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes do Assistente de Métricas para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verificar o status da ingestão](#check-the-ingestion-status)
* [Configurar a detecção de anomalias](#configure-anomaly-detection)
* [Criar um gancho](#create-a-hook)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Consultar o alerta](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Na classe `Program` do aplicativo, crie variáveis para as chaves e o ponto de extremidade do recurso.

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso do Assistente de Métricas que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas Etapas**. Encontre as chaves de assinatura e o ponto de extremidade na página **Chave e Ponto de Extremidade** do recurso, em **Gerenciamento de Recursos**. <br><br>Para recuperar sua chave de API, acesse [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Selecione as opções apropriadas: **Diretório**, **Assinaturas** e **Workspace** para seu recurso e escolha **Introdução**. Depois, você poderá recuperar suas chaves de API em [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

Depois que você tiver a assinatura e as chaves de API, crie uma MetricsAdvisorKeyCredential. Com o ponto de extremidade e a credencial de chave, você pode criar um [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs):

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Você também pode criar um [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) para executar operações administrativas:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

O Assistente de Métricas dá suporte a vários tipos de fontes de dados. Neste exemplo, ilustraremos como criar um `DataFeed` que extrai dados de um SQL Server. Substitua `connection_String` pela sua cadeia de conexão do SQL Server e substitua `query` por uma consulta que retorne seus dados em um só carimbo de data/hora. Você também precisará ajustar os valores de `DataFeedMetric` e `DataFeedDimension` com base nos seus dados personalizados.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Verificar o status da ingestão

Verificar o status da ingestão de um `DataFeed` criado anteriormente

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    TopCount = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Configurar a detecção de anomalias 

Crie uma configuração de detecção de anomalias para informar ao serviço quais pontos de dados devem ser considerados anomalias.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>Criar um gancho

O Assistente de Métricas dá suporte às classes `EmailNotificationHook` e `WebNotificationHook` como meio de assinar notificações de alertas. Neste exemplo, ilustraremos como criar um `EmailNotificationHook`. Você precisa passar o gancho para uma configuração de alerta de anomalias para começar a receber notificações. Confira o exemplo [Criar uma configuração de alerta de anomalias](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) para obter mais informações.

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Crie uma `AnomalyAlertConfiguration` para informar ao serviço quais anomalias devem disparar alertas.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>Consultar o alerta

Examine os alertas criados por uma determinada configuração do alerta de anomalias.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    TopCount = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

Depois de saber qual é a ID de um alerta, liste as [anomalias](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly) que dispararam esse alerta.

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { TopCount = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```