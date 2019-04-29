---
title: Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure
description: Este artigo descreve como usar o SDK do .NET para escrever aplicativos que gerenciam trabalhos do Data Lake Analytics, fontes de dados e os usuários.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 0a10af73d754596e9b5bb34b2974d7f1647d06f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617700"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Gerenciar o Azure Data Lake Analytics usando um aplicativo .NET

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando um aplicativo codificado com o SDK do .NET do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado**.
* **SDK do Microsoft Azure para .NET versão 2.5 ou posterior**.  Instale-o usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* **Pacotes NuGet necessários**

### <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

|Pacote|Versão|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0 – versão prévia|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0 – versão prévia|

Você pode instalar esses pacotes por meio da linha de comando do NuGet com os seguintes comandos:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Variáveis comuns

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Autenticação

Você tem várias opções para fazer logon no Azure Data Lake Analytics. O snippet a seguir mostra um exemplo de autenticação usando a autenticação de usuário interativo com um pop-up.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

O código-fonte para **GetCreds_User_Popup** e o código para outras opções de autenticação são abordados em [Data Lake Analytics .NET authentication options](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) (Opções de autenticação .NET do Data Lake Analytics)


## <a name="create-the-client-management-objects"></a>Criar objetos de gerenciamento do cliente

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Gerenciar Contas

### <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure

Se você ainda não criou um, deve ter um Grupo de Recursos do Azure para criar os componentes do Data Lake Analytics. Você precisa das suas credenciais de autenticação, da ID de assinatura e de um local. O código abaixo mostra como criar um grupo de recursos:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Para obter mais informações, confira Grupos de Recursos do Azure e Data Lake Analytics.

### <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake

Cada conta do ADLA exige uma conta do ADLS. Se ainda não tiver uma para usar, você poderá criar uma com o código a seguir:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake

O código a seguir cria uma conta do ADLS

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Listar contas do Data Lake Store

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Listar contas da Análise Data Lake

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Verificando se uma conta existe

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Obter informações sobre uma conta

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Excluir uma conta

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Obter a conta padrão do Data Lake Store

Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store. Use este código para determinar a conta de Armazenamento padrão para uma conta do Analytics.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Gerenciar as fontes de dados

No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Conta de Armazenamento do Azure](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Vincular a uma conta de armazenamento do Azure

Você pode criar links para as contas de Armazenamento do Azure.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Listar fontes de dados de Armazenamento do Azure

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Listar as fontes de dados do Data Lake Store

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Carregar e baixar arquivos e pastas

Você pode usar o objeto de gerenciamento de cliente do sistema de arquivos do Data Lake Store para carregar e baixar arquivos ou pastas individuais do Azure para seu computador local, usando os seguintes métodos:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

O primeiro parâmetro para esses métodos é o nome da conta de armazenamento do Data Lake Store, seguido de parâmetros para o caminho de origem e o caminho de destino.

O exemplo a seguir mostra como baixar uma pasta no Data Lake Store.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Criar um arquivo em uma conta do Data Lake Store

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Verificar os caminhos da conta de Armazenamento do Azure

O código a seguir verifica se existe uma conta de Armazenamento do Azure (storageAccntName) em uma conta do Data Lake Analytics (analyticsAccountName) e se existe um contêiner (containerName) na conta de Armazenamento do Azure.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Gerenciar catálogo e trabalhos

O objeto DataLakeAnalyticsCatalogManagementClient fornece métodos para gerenciar o banco de dados SQL fornecido para cada conta do Azure Data Lake Analytics. O DataLakeAnalyticsJobManagementClient fornece métodos para enviar e gerenciar os trabalhos executados no banco de dados com os scripts U-SQL.

### <a name="list-databases-and-schemas"></a>Listar bancos de dados e esquemas

Entre as várias coisas que você pode listar, as mais comuns são os bancos de dados e seu esquema. O código a seguir obtém uma coleção de bancos de dados, em seguida, enumera o esquema de cada banco de dados.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Listar colunas da tabela

O código a seguir mostra como acessar o banco de dados com um cliente de gerenciamento de Catálogo do Data Lake Analytics para listar as colunas em uma tabela especificada.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Enviar um trabalho do U-SQL

O código a seguir mostra como usar um cliente de gerenciamento de trabalho do Data Lake Analytics para enviar um trabalho.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Listar trabalhos com falha

O código a seguir lista informações sobre os trabalhos que falharam.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Listar pipelines

O código a seguir lista informações sobre cada pipeline de trabalhos enviados para a conta.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Listar recorrências

O código a seguir lista informações sobre cada recorrência de trabalhos enviados para a conta.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Cenários comuns de grafo

### <a name="look-up-user-in-the-aad-directory"></a>Pesquisar o usuário no diretório do AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Obter o ObjectId de um usuário no diretório do AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Gerenciar políticas de computação

O objeto DataLakeAnalyticsAccountManagementClient fornece métodos para gerenciar as políticas de computação para uma conta do Data Lake Analytics.

### <a name="list-compute-policies"></a>Listar políticas de computação

O código a seguir recupera uma lista de políticas de computação para uma conta do Data Lake Analytics.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Criar uma nova política de computação

O código a seguir cria uma nova política de computação para uma conta do Data Lake Analytics, definindo a quantidade máxima de AUs disponíveis para o usuário especificado como 50 e a prioridade mínima de trabalho como 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)