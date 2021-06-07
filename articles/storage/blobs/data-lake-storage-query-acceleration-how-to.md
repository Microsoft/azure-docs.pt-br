---
title: Filtrar dados usando a aceleração de consulta Azure Data Lake Storage | Microsoft Docs
description: Use a aceleração de consulta para recuperar um subconjunto de dados de sua conta de armazenamento.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a925d3f55395d094c7f19f65de4b72fd20a11a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213667"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrar dados usando a aceleração de consulta Azure Data Lake Storage

Este artigo mostra como usar a aceleração de consulta para recuperar um subconjunto de dados de sua conta de armazenamento. 

A aceleração de consulta permite que aplicativos e estruturas de análise otimizem drasticamente o processamento de dados recuperando apenas os dados necessários para executar uma determinada operação. Para saber mais, consulte [aceleração de consulta Azure data Lake Storage](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Pré-requisitos

- Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento de **uso geral v2** . consulte [criar uma conta de armazenamento](../common/storage-account-create.md).

- Escolha uma guia para exibir os pré-requisitos específicos do SDK.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Não aplicável

  ### <a name="net"></a>[.NET](#tab/dotnet)

  O [SDK do .net](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Este artigo pressupõe que você criou um projeto Java usando o Apache Maven. Para obter um exemplo de como criar um projeto usando o Apache Maven, consulte [Configurando](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 ou superior.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Não há pré-requisitos adicionais necessários para usar o SDK do Node.js.

---

## <a name="enable-query-acceleration"></a>Habilitar aceleração de consulta

Para usar a aceleração de consulta, você deve registrar o recurso de aceleração de consulta com sua assinatura. Depois de verificar se o recurso está registrado, você deve registrar o provedor de recursos de armazenamento do Azure. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Etapa 1: registrar o recurso de aceleração de consulta

Para usar a aceleração de consulta, primeiro você deve registrar o recurso de aceleração de consulta com sua assinatura. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra uma janela de comando do Windows PowerShell.

1. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

3. Registre o recurso de aceleração de consulta usando o comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md) ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

3. Registre o recurso de aceleração de consulta usando o comando [AZ Feature Register](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Etapa 2: verificar se o recurso está registrado

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se o registro foi concluído, use o comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se o registro foi concluído, use o comando [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Etapa 3: registrar o provedor de recursos de armazenamento do Azure

Depois que o registro for aprovado, você deverá registrar novamente o provedor de recursos de armazenamento do Azure. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para registrar o provedor de recursos, use o comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para registrar o provedor de recursos, use o comando [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Configurar seu ambiente

### <a name="step-1-install-packages"></a>Etapa 1: instalar pacotes 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instale o módulo AZ Version 4.6.0 ou superior.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Para atualizar de uma versão mais antiga do AZ, execute o seguinte comando:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Abra um prompt de comando e altere o diretório ( `cd` ) para a pasta do projeto, por exemplo:

   ```console
   cd myProject
   ```

2. Instale a `12.5.0-preview.6` versão ou posterior do pacote da biblioteca de cliente do armazenamento de BLOBs do Azure para .NET usando o `dotnet add package` comando. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Os exemplos que aparecem neste artigo analisam um arquivo CSV usando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) . Para usar essa biblioteca, use o comando a seguir.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Abra o arquivo *pom.xml* do seu projeto em um editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Instale a biblioteca de cliente Azure Data Lake Storage para Python usando [Pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Instale Data Lake biblioteca de cliente para JavaScript abrindo uma janela de terminal e, em seguida, digitando o comando a seguir.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Etapa 2: Adicionar instruções

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Não aplicável

#### <a name="net"></a>[.NET](#tab/dotnet)

Adicione essas `using` instruções à parte superior do seu arquivo de código.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

A aceleração de consulta recupera dados CSV e JSON formatados. Portanto, certifique-se de adicionar instruções using para qualquer biblioteca de análise de CSV ou JSON que você escolher usar. Os exemplos que aparecem neste artigo analisam um arquivo CSV usando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) que está disponível no NuGet. Portanto, adicionaremos essas `using` instruções à parte superior do arquivo de código.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Para compilar exemplos apresentados neste artigo, também será necessário adicionar essas `using` instruções.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Adicione essas `import` instruções à parte superior do seu arquivo de código.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Adicione essas instruções de importação na parte superior do seu arquivo de código.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Inclua o `storage-blob` módulo colocando essa instrução na parte superior do seu arquivo de código. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

A aceleração de consulta recupera dados CSV e JSON formatados. Portanto, certifique-se de adicionar instruções para quaisquer módulos de análise CSV ou JSON que você escolher usar. Os exemplos que aparecem neste artigo analisam um arquivo CSV usando o módulo de [CSV rápido](https://www.npmjs.com/package/fast-csv) . Portanto, adicionamos essa instrução à parte superior do arquivo de código.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperar dados usando um filtro

Você pode usar o SQL para especificar os predicados de filtro de linha e as projeções de coluna em uma solicitação de aceleração de consulta. O código a seguir consulta um arquivo CSV no armazenamento e retorna todas as linhas de dados em que a terceira coluna corresponde ao valor `Hemingway, Ernest` . 

- Na consulta SQL, a palavra-chave `BlobStorage` é usada para denotar o arquivo que está sendo consultado.

- As referências de coluna são especificadas como `_N` onde a primeira coluna é `_1` . Se o arquivo de origem contiver uma linha de cabeçalho, você poderá se referir a colunas pelo nome especificado na linha de cabeçalho. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

O método Async `BlobQuickQueryClient.QueryAsync` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para o aplicativo como um objeto [Stream](/dotnet/api/system.io.stream) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

O método `BlobQuickQueryClient.openInputStream()` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para o aplicativo como um `InputStream` objeto que pode ser lido como qualquer outro objeto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este exemplo envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta. O `blob` objeto passado para a `queryHemingway` função auxiliar é do tipo [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Para saber mais sobre como obter um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) , consulte [início rápido: gerenciar BLOBs com o SDK do JavaScript V12 no Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperar colunas específicas

Você pode fazer o escopo dos resultados para um subconjunto de colunas. Dessa forma, você recupera apenas as colunas necessárias para executar um determinado cálculo. Isso melhora o desempenho do aplicativo e reduz o custo porque menos dados são transferidos pela rede. 

Esse código recupera apenas a `BibNum` coluna de todos os livros no conjunto de dados. Ele também usa as informações da linha de cabeçalho no arquivo de origem para fazer referência a colunas na consulta.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

O código a seguir combina a filtragem de linha e as projeções de coluna na mesma consulta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Aceleração de consulta Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Referência da linguagem SQL de aceleração de consulta](query-acceleration-sql-reference.md)
