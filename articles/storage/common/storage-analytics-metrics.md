---
title: Métricas de análise de armazenamento do Azure (clássico)
description: Saiba como usar as métricas no armazenamento do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 5fecced844b3580c83fd18d0c14c3a2083f7a4fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165732"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas de análise de armazenamento do Azure (clássico)

A análise de armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e os dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.  

 A métrica da Análise de Armazenamento vem habilitada por padrão nas novas contas de armazenamento. Você pode configurar as métricas na [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações de definir propriedades de serviço para habilitar a análise de armazenamento para cada serviço.  

> [!NOTE]
> Métricas do Storage Analytics estão disponíveis para os serviços Blob, fila, tabela e arquivo.
> Métricas do Storage Analytics agora são métricas clássico. A Microsoft recomenda usar [as métricas de armazenamento no Azure Monitor](storage-metrics-in-azure-monitor.md) em vez de métricas de análise de armazenamento.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registrado em intervalos de horas ou minutos para cada serviço de armazenamento e operações de API solicitadas, incluindo ingresso/egresso, disponibilidade, erros e percentuais da solicitação categorizados. Você pode ver uma lista completa dos detalhes da transação no tópico [Esquema da tabela de métricas da análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Os dados de transação são registrados em dois níveis: nível de serviço e nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação seja feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação foi solicitada nessa hora.  

 Por exemplo, se você executar uma operação **GetBlob** no serviço Blob, as métricas do Storage Analytics vão registrar a solicitação e as incluirá nos dados agregados para o serviço Blob, bem como a operação **GetBlob**. No entanto, se nenhum **GetBlob** operação é solicitada durante a hora, uma entidade será não ser gravada para o *$MetricsTransactionsBlob* para essa operação.  

 As métricas de transações são registradas para solicitações de usuários e solicitações feitas pela própria análise de armazenamento. Por exemplo, solicitações pela análise de armazenamento para gravar logs e entidades de tabela são registradas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade somente estão disponíveis para o serviço Blob.

 Os dados de capacidade são gravados diariamente para serviço de Blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e a outra fornece estatísticas sobre o contêiner de blob `$logs` usado pela análise de armazenamento. O *$MetricsCapacityBlob* tabela inclui as seguintes estatísticas:  

- **Capacity**: A quantidade de armazenamento usada pelo serviço Blob da conta de armazenamento, em bytes.  
- **ContainerCount**: O número de contêineres de blob no serviço de Blob da conta de armazenamento.  
- **ObjectCount**: O número de blobs de blocos ou páginas confirmados e não confirmados no serviço de Blob da conta de armazenamento.  

  Para saber mais sobre as métricas de capacidade, consulte [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para informações de transação, uma tabela de informações de transações de minutos e outra tabela para informações de capacidade. Informações de transações de transação de minuto consistem em dados de solicitação e resposta, e informações de capacidade consistem em dados de uso de armazenamento. Métricas de hora, métricas de minuto e capacidade para um armazenamento de serviço Blob do conta pode ser acessada nas tabelas que são nomeadas conforme descrito na tabela a seguir.  

|Nível de métricas|Nomes de tabela|Suporte para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas por hora, local principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versões anteriores a 15-08-2013 apenas. Embora esses nomes ainda ter suporte, é recomendável que você alterne para usar as tabelas listadas abaixo.|  
|Métricas por hora, local principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas as versões. Suporte para métricas de serviço de arquivo está disponível somente na versão 2015-04-05 e posterior.|  
|Métricas por minuto, local principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas as versões. Suporte para métricas de serviço de arquivo está disponível somente na versão 2015-04-05 e posterior.|  
|Métricas por hora, local secundário|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Métricas por minuto, local secundário|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Capacidade (apenas serviço Blob)|$MetricsCapacityBlob|Todas as versões.|  

 Essas tabelas são criadas automaticamente quando a análise de armazenamento está habilitada para um ponto de extremidade de serviço de armazenamento. Elas são acessadas por meio do namespace da conta de armazenamento, por exemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. As tabelas de métricas não aparecem em uma operação de listagem e devem ser acessadas diretamente por meio do nome da tabela.  

## <a name="enable-metrics-using-the-azure-portal"></a>Habilitar métricas usando o portal do Azure
Siga estas etapas para habilitar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento.
1. Selecione **as configurações de diagnóstico (clássico)** da **Menu** painel.
1. O **Status** deve ser definido como **Ativado**.
1. Selecione as métricas para os serviços que deseja monitorar.
1. Especifica uma política de retenção para indicar por quanto tempo deve-se manter as métricas e os dados de log.
1. Clique em **Salvar**.

O [portal do Azure](https://portal.azure.com) não permite atualmente configurar métricas de minuto em sua conta de armazenamento. Habilite a métrica de minutos usando o PowerShell ou programaticamente.

> [!NOTE]
>  Observe que o portal do Azure não permite atualmente configurar métricas de minuto em sua conta de armazenamento. Você deve habilitar as métricas de minuto usando o PowerShell ou programaticamente.

## <a name="enable-storage-metrics-using-powershell"></a>Habilitar métricas de armazenamento usando o PowerShell  
Você pode usar o PowerShell no computador local para configurar as métricas de armazenamento em sua conta de armazenamento usando o cmdlet do PowerShell do Azure **Get-AzureStorageServiceMetricsProperty** para recuperar as configurações atuais e o cmdlet  **Set-AzureStorageServiceMetricsProperty** para alterar as configurações atuais.  

Os cmdlets que controlam as métricas de armazenamento usam os seguintes parâmetros:  

* **ServiceType**, os valores possíveis são **Blob**, **fila**, **tabela**, e **arquivo**.
* **MetricsType**, os valores possíveis são **hora** e **minuto**.  
* **MetricsLevel**, os valores possíveis são:
* **Nenhum**: Desativa o monitoramento.
* **Serviço**: Coleta métricas como ingresso/egresso, disponibilidade, latência e porcentagens de êxitos, que são agregadas para o blob, fila, tabela e serviços de arquivo.
* **ServiceAndApi**: Além das métricas do serviço, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API de serviço de armazenamento do Azure.

Por exemplo, o comando a seguir ativa a métrica de minutos para o serviço blob na sua conta de armazenamento com a período de retenção definido para cinco dias: 

> [!NOTE]
> Esse comando presume que você tiver entrado em sua assinatura do Azure usando o `Connect-AzAccount` comando.

```  
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Substitua o `<resource-group-name>` valor de espaço reservado com o nome do seu grupo de recursos.

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.



O comando a seguir recupera o nível de métricas por hora atual e dias de retenção para o serviço blob na conta de armazenamento padrão:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Para saber mais sobre como configurar os cmdlets do Azure PowerShell para funcionar com sua assinatura do Azure e como escolher a conta de armazenamento padrão para usar, confira: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Habilitar métricas de armazenamento programaticamente  
Além de usar o portal do Azure ou os cmdlets do PowerShell do Azure para controlar as métricas de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se você estiver usando uma linguagem .NET, você pode usar a biblioteca de cliente de armazenamento.  

As classes **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, e **CloudFileClient** todos têm métodos como  **SetServiceProperties** e **SetServicePropertiesAsync** que utilizam um **ServiceProperties** objeto como um parâmetro. Você pode usar o **ServiceProperties** objeto configurar métricas de armazenamento. Por exemplo, a seguinte C# trecho de código mostra como alterar os dias de retenção e o nível de métricas para as métricas de fila por hora:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre como usar uma linguagem .NET para configurar métricas de armazenamento, consulte [biblioteca de cliente de armazenamento para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Para obter informações gerais sobre como configurar métricas de armazenamento usando a API REST, consulte [habilitando e configurando a análise de armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Exibindo as métricas de armazenamento  
Após configurar as métricas Análise de Armazenamento para monitorar sua conta de armazenamento, a Análise de Armazenamento registra as métricas em um conjunto conhecido de tabelas na sua conta de armazenamento. Você pode configurar gráficos para exibir as métricas por hora no [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Selecione **métricas (clássico)** na **Menu** folha para o serviço cujas métricas você deseja exibir.
1. Clique no gráfico que você deseja configurar.
1. No **Editar gráfico** folha, selecione o **intervalo de tempo**, **tipo de gráfico**e as métricas que deseja exibir no gráfico.

No **monitoramento (clássico)** seção da folha de menu da sua conta de armazenamento no portal do Azure, você pode configurar [regras de alerta](#metrics-alerts), como o envio de email alertas para notificá-lo quando uma métrica específica atinge um valor determinado.

Se você quiser baixar as métricas para armazenamento de longo prazo ou para analisá-los localmente, você deve usar uma ferramenta ou escrever um código para ler as tabelas. Você deve baixar a métrica de minutos para análise. As tabelas não aparecem quando você lista todas as tabelas em sua conta de armazenamento, mas você pode acessá-las diretamente por nome. Muitas ferramentas de navegação de armazenamento estão cientes dessas tabelas e permitem que você exiba diretamente (consulte [ferramentas de cliente de armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista das ferramentas disponíveis).

||||  
|-|-|-|  
|**Métricas**|**Nomes de tabela**|**Observações**|  
|Métricas por hora|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Em versões anteriores 2013-08-15, essas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas para o serviço de arquivos estão disponível desde a versão 2015-04-05.|  
|Métricas por minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só pode ser habilitado usando o PowerShell ou programaticamente.<br /><br /> As métricas para o serviço de arquivos estão disponível desde a versão 2015-04-05.|  
|Capacity|$MetricsCapacityBlob|Somente serviço BLOB.|  

Você pode encontrar detalhes completos dos esquemas para essas tabelas no [Esquema da tabela de métricas da análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema). As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilidade**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|usuário;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo dos dados de métrica de minutos, a chave de partição usa o tempo de resolução minuto. A chave de linha identifica o tipo de informação que é armazenado na linha, e isso é composto de duas partes de informações, o tipo de acesso e o tipo de solicitação:  

-   O tipo de acesso é **usuário** ou **sistema**, onde **usuário** refere-se a todas as solicitações de usuário para o serviço de armazenamento, e **sistema** refere-se ao solicitações feitas pela análise de armazenamento.  

-   O tipo de solicitação é **todos os** nesse caso, é uma linha de resumo, ou ele identifica a API específica, como **QueryEntity** ou **UpdateEntity**.  

Os dados de exemplo acima mostram todos os registros para um único minuto (começando às 11h,), portanto, o número de **QueryEntities** solicitações mais o número de **QueryEntity** solicitações mais o número de  **UpdateEntity** solicitações adicionam até sete, que é o total mostrado em de **user: All** linha. Da mesma forma, você pode derivar a latência média de ponta a ponta 104,4286 na **user: All** linha Calculando ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Considere configurar alertas na [portal do Azure](https://portal.azure.com) para que você será notificado automaticamente das alterações importantes no comportamento dos seus serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Confira [Ferramentas de Cliente do Armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista de ferramentas do gerenciador de armazenamento disponíveis. Você pode configurar alertas na **alerta (clássico)** folha, acessível sob **monitoramento (clássico)** na folha de menu da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas por hora ou minuto correspondentes são registrados. No caso de métricas por minuto, diversos minutos de dados podem ser gravados de uma só vez. Isso pode levar à agregação de transações de minutos anteriores à transação do minuto atual. Quando isso acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar ao acionamento inesperado de alertas.
>

## <a name="accessing-metrics-data-programmatically"></a>Acessando dados de métrica programaticamente  
A listagem a seguir mostra o código C# de exemplo, que acessa a métrica de minutos para um intervalo de minutos e exibe os resultados em uma janela de console. O exemplo de código usa a versão da biblioteca de cliente de armazenamento do Azure 4. x ou posterior, que inclui o **CloudAnalyticsClient** classe que simplifica o acesso às tabelas de métricas no armazenamento.  

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
     // (StartsWith is not supported using LINQ with Azure Table storage)  
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

## <a name="billing-on-storage-metrics"></a>As métricas de armazenamento de cobrança  
Gravar solicitações para criar entidades de tabela para métricas são cobradas de acordo com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.  

Solicitações de leitura e exclusão de dados de métricas por um cliente também são faturáveis em taxas padrão. Se você configurou uma política de retenção de dados, que não seja cobrado quando o armazenamento do Azure excluir dados de métricas antigos. No entanto, se você excluir dados de análise, sua conta será cobrada para as operações de exclusão.  

A capacidade usada pelas tabelas de métricas também é faturável. Você pode usar o seguinte para estimar a quantidade de capacidade usada para armazenar dados de métricas:  

-   Se a cada hora de um serviço utiliza todas as APIs de cada serviço, aproximadamente 148KB de dados é armazenado nas tabelas de métricas de transação a cada hora se você tiver habilitado os dois e API de nível de serviço resumo.  
-   Se dentro de cada hora, um serviço utiliza todas as APIs de serviço, em seguida, cerca de 12KB de dados é armazenado nas tabelas de métricas de transação a cada hora se você tiver habilitado o resumo de nível de serviço apenas.  
-   A tabela de capacidade para blobs tem duas linhas adicionadas por dia, desde que você ter tiver optado-in para logs. Isso implica que todos os dias, o tamanho dessa tabela aumenta em até aproximadamente 300 bytes.

## <a name="next-steps"></a>Próximas etapas
* [Como monitorar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema da tabela de métricas da análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Mensagens de operações e status registradas de análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registro em log da Análise de Armazenamento](storage-analytics-logging.md)
