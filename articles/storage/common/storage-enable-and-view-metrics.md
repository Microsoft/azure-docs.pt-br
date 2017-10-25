---
title: "Habilitando métricas de armazenamento no Portal do Azure | Microsoft Docs"
description: "Como habilitar métricas de armazenamento para os serviços Blob, Fila, Tabela e Arquivo"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: tamram
ms.openlocfilehash: 8abb4f968c1fa84e03c8cc807826d3684713847a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Habilitando métricas do Armazenamento do Azure e exibição de dados de métricas
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Visão geral
As Métricas de Armazenamento são habilitadas por padrão quando você cria uma nova conta de armazenamento. Você pode configurar o monitoramento por meio do [Portal do Azure](https://portal.azure.com) ou o Windows PowerShell ou programaticamente por meio de uma das bibliotecas de cliente de armazenamento.

Você pode configurar um período de retenção para os dados de métricas: este período determina quanto tempo o serviço de armazenamento mantém as métricas e as cobranças para o espaço necessário para armazená-los. Normalmente, você deve usar um período de retenção mais curto para métricas de minuto em vez de métricas por hora por causa do espaço extra significativo necessário para métricas de minuto. Escolha um período de retenção que você tenha tempo suficiente para analisar os dados e baixar qualquer métricas que você deseja manter para análise offline ou para fins de relatório. Lembre-se de que você também será cobrado para baixar dados de métrica de sua conta de armazenamento.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Como habilitar métricas usando o portal do Azure
Siga estas etapas para habilitar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento.
1. Selecione o **Diagnóstico** a partir do **painel do** Menu.
1. O **Status** deve ser definido como **Ativado**.
1. Selecione as métricas para os serviços que deseja monitorar.
1. Especifica uma política de retenção para indicar por quanto tempo deve-se manter as métricas e os dados de log.
1. Selecione **Salvar**.

O [portal do Azure](https://portal.azure.com) não permite atualmente configurar métricas de minuto em sua conta de armazenamento. Habilite a métrica de minutos usando o PowerShell ou programaticamente.

## <a name="how-to-enable-metrics-using-powershell"></a>Como habilitar métricas usando o PowerShell
Você pode usar o PowerShell no computador local para configurar as métricas de armazenamento na sua conta de armazenamento usando o cmdlet do PowerShell do Azure Get-AzureStorageServiceMetricsProperty para recuperar as configurações atuais, e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as configurações atuais.

Os cmdlets que controlam as métricas de armazenamento usam os seguintes parâmetros:

* Os valorespossíveis de MetricsType são hora e minuto.
* ServiceType: os possíveis valores são Blob, Fila e Tabela.
* MetricsLevel: os valores possíveis são None, Serviço e ServiceAndApi.

Por exemplo, o comando a seguir ativa a métrica de minutos para o Serviço Blob na conta de armazenamento padrão com o período de retenção definido para cinco dias:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

O comando a seguir recupera o nível de métricas por hora atual e dias de retenção para o serviço blob na conta de armazenamento padrão:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte: [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Como habilitar métricas de armazenamento por meio de programação
O trecho em C# a seguir mostra como habilitar métricas e a criação de log para o serviço Blob usando a biblioteca de cliente de armazenamento para .NET:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Exibindo as métricas de armazenamento
Após configurar as métricas Análise de Armazenamento para monitorar sua conta de armazenamento, a Análise de Armazenamento registra as métricas em um conjunto conhecido de tabelas na sua conta de armazenamento. Você pode configurar gráficos para exibir as métricas por hora no [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Selecione **Métricas** no painel do **Menu** para o serviço cujas métricas você deseja exibir.
1. Selecione **editar** no gráfico que você deseja configurar.
1. No painel **Editar gráfico**, selecione o **intervalo de tempo**, **tipo de gráfico**e as métricas que você deseja exibir no gráfico.
1. Selecione **OK**

Se você quiser baixar as métricas para um armazenamento a longo prazo ou para analisá-las localmente, você precisa:

* Usar uma ferramenta que reconheça essas tabelas e permita que você as exiba e as baixe.
* Escrever um aplicativo ou script personalizado para ler e armazenar as tabelas.

Muitas ferramentas de navegação de armazenamento de terceiros reconhecem essas tabelas e permitem que você as exiba diretamente.
Consulte as [ferramentas do cliente de armazenamento do Microsoft Azure](storage-explorers.md) para obter uma lista das ferramentas disponíveis.

> [!NOTE]
> Da versão 0.8.0 do [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/) em diante, você pode exibir e baixar as tabelas de métricas de análise.
> 
> 

Para acessar as tabelas de análise de forma programática, observe que as tabelas de análise não são mostradas quando você lista todas as tabelas em sua conta de armazenamento. Você pode acessá-las diretamente por nome ou usar [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) na biblioteca de cliente .NET para consultar os nomes de tabela.

### <a name="hourly-metrics"></a>Métricas por hora
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métricas por minuto
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidade
* $MetricsCapacityBlob

Você pode encontrar detalhes completos dos esquemas para essas tabelas no [Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |usuário;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

Neste exemplo dos dados de métrica de minutos, a chave de partição usa o tempo de resolução minuto. A tecla de registro identifica o tipo de informação que é armazenado na linha. A tecla de registro é composta por duas partes de informações, o tipo de acesso e o tipo de solicitação:

* O tipo de acesso é um usuário ou sistema, onde o usuário refere-se a todas as solicitações de usuário para o serviço de armazenamento e o sistema refere-se às solicitações feitas pela análise de armazenamento.
* O tipo de solicitação é tudo que nesse caso é uma linha de resumo, ou ele identifica a API específica, como QueryEntity ou UpdateEntity.

Os dados de exemplo acima mostram todos os registros de um minuto (iniciando às 11h00), para o número de solicitações de QueryEntities mais o número de solicitações de QueryEntity mais o número de solicitações de UpdateEntity adicionam até sete, que é o total mostrado na linha user:All. Da mesma forma, você pode derivar a latência média de ponta a ponta 104.4286 na linha user:All ao calcular ((143.8 * 5) + 3 + 9)/7.

## <a name="metrics-alerts"></a>Alertas de métricas
Considere configurar alertas no [Portal do Azure](https://portal.azure.com) para que as Métricas de Armazenamento possam notificá-lo automaticamente sobre todas as alterações importantes no comportamento dos seus serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Confira [Ferramentas de Cliente do Armazenamento do Azure](storage-explorers.md) para obter uma lista de ferramentas do gerenciador de armazenamento disponíveis. Você pode configurar os alertas no painel de **Regras de alerta**, acessível em **Monitoramento** no painel do Menu da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas por hora ou minuto correspondentes são registrados. Ao registrar as métricas de minutos, diversos minutos de dados podem ser gravados de uma só vez. As transações de minutos anteriores podem ser agregadas na transação do minuto atual. Quando isso acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar ao acionamento inesperado de alertas.
>

## <a name="accessing-metrics-data-programmatically"></a>Acessando dados de métrica programaticamente
A listagem a seguir mostra o código C# de exemplo, que acessa a métrica de minutos para um intervalo de minutos e exibe os resultados em uma janela de console. Ele usa a biblioteca de armazenamento do Azure versão 4, o que inclui a classe CloudAnalyticsClient que simplifica o acesso às tabelas de métricas no armazenamento.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Quais taxas você provoca quando habilita a métrica de armazenamento?
Gravar solicitações para criar entidades de tabela para métricas são cobradas de acordo com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.

Solicitações de leitura e exclusão por um cliente para dados de métrica também são faturáveis em taxas padrão. Se você configurou uma política de retenção de dados, que não seja cobrado quando o armazenamento do Azure excluir dados de métricas antigos. No entanto, se você excluir dados de análise, sua conta será cobrada para as operações de exclusão.

A capacidade usada pelas tabelas de métricas também é faturável. Você pode usar o seguinte para estimar a quantidade de capacidade usada para armazenar dados de métricas:

* Se cada hora de um serviço utiliza todas as APIs de cada serviço, então aproximadamente 148KB de dados serão armazenados nas tabelas de transações de métricas a cada hora se você tiver habilitado o serviço e o resumo de nível de API.
* Se cada hora de um serviço utiliza todas as APIs de cada serviço, então cerca de 12KB de dados serão armazenados nas tabelas de transação de métricas a cada hora se você tiver habilitado apenas o resumo do nível de serviço.
* A tabela de capacidade para blobs tem duas linhas adicionadas por dia (fornecida pelo usuário que optou pelos logs). Isso implica que todos os dias o tamanho dessa tabela aumenta em até aproximadamente 300 bytes.

## <a name="next-steps"></a>Próximas etapas
[Habilitando o armazenamento de log e acessando os dados de log](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
