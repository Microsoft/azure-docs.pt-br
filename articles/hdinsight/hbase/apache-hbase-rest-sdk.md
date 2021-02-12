---
title: Use o SDK do .NET HBase - HDInsight do Azure
description: Use o SDK do .NET HBase para criar e excluir tabelas e para ler e gravar dados.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: d1979e43adc76f4125097fc809ef137baee05f53
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939573"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Usar o SDK do .NET para o Apache HBase

O [HBase](apache-hbase-overview.md) fornece duas opções principais para trabalhar com seus dados: [consultas no Hive e chamadas à API RESTful do HBase](apache-hbase-tutorial-get-started-linux.md). Você pode trabalhar diretamente com a API REST usando o comando `curl` ou um utilitário semelhante.

Para aplicativos C# e .NET, a [Biblioteca de Cliente do .NET do HBase REST da Microsoft](https://www.nuget.org/packages/Microsoft.HBase.Client/) fornece uma biblioteca de cliente sobre o API REST do HBase.

## <a name="install-the-sdk"></a>Instalar o SDK

O SDK do .NET HBase é fornecido como um pacote do NuGet, que pode ser instalado do **Console Gerenciador de Pacotes do NuGet** do Visual Studio com o seguinte comando:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Criar um novo objeto HBaseClient

Para começar a usar a biblioteca, crie uma instância de um novo `HBaseClient` objeto, passando `ClusterCredentials` que contém o `Uri` ao cluster e o nome de usuário do Hadoop e a senha.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Substitua CLUSTERNAME pelo nome do cluster HDInsight HBase e por USERNAME e PASSWORD pelas credenciais do Apache Hadoop especificadas na criação do cluster. O nome de usuário Hadoop padrão é **admin**.

## <a name="create-a-new-table"></a>Criar uma nova tabela

O HBase armazena dados em tabelas. Uma tabela consiste em uma *Rowkey*, a chave primária e um ou mais grupos de colunas chamado *famílias de coluna*. Os dados em cada tabela horizontalmente são distribuídos por um intervalo de Rowkey em *regiões*. Cada região tem uma chave de início e término. Uma tabela pode ter uma ou mais regiões. À medida que os dados na tabela aumentam, o HBase divide regiões grandes em regiões menores. Regiões são armazenadas em *servidores de região*, em que um servidor de região pode armazenar várias regiões.

Os dados são armazenados fisicamente no *HFiles*. Um único HFile contém dados para uma tabela, uma região e uma família de coluna. As linhas no HFile são armazenadas classificadas pelo Rowkey. Cada HFile tem um índice *Árvore B+* para a recuperação rápida das linhas.

Para criar uma nova tabela, especifique um `TableSchema` e colunas. O código a seguir verifica se a tabela 'RestSDKTable' já existe - caso contrário, a tabela é criada.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Essa nova tabela tem famílias de duas colunas, T1 e T2. Como as famílias de colunas são armazenadas separadamente em HFiles diferentes, faz sentido ter uma família de colunas separada para os dados consultados com frequência. No exemplo [Inserir dados](#insert-data) a seguir, colunas são adicionadas à família de colunas t1.

## <a name="delete-a-table"></a>Excluir uma tabela

Para excluir uma tabela:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Inserir dados

Para inserir dados, especifique uma chave de linha exclusiva como o identificador de linha. Todos os dados são armazenados em uma matriz `byte[]`. O código a seguir define e adiciona as colunas `title`, `director`, e `release_date` para a família de colunas t1, já que essas colunas são acessados com mais frequência. As colunas `description` e `tagline` são adicionadas à família de colunas t2. Você pode dividir seus dados em famílias de colunas, conforme necessário.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

O HBase implementa o [Cloud BigTable](https://cloud.google.com/bigtable/), portanto, o formato de dados é semelhante à imagem a seguir:

![Saída de dados de exemplo do Apache HBase](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Selecionar dados

Para ler dados de uma tabela do HBase, passe o nome da tabela e a chave de linha para o método `GetCellsAsync` para retornar o `CellSet`.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

Nesse caso, o código retorna apenas a primeira correspondência de linha, já que deve haver apenas uma linha para uma chave exclusiva. O valor retornado é alterado para o formato `string` da matriz `byte[]`. Você também pode converter o valor para outros tipos, como um número inteiro para a data de lançamento do filme:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Verificar em linhas

O HBase usa `scan` para recuperar uma ou mais linhas. Este exemplo solicita várias linhas em lotes de 10 e recupera dados cujos valores de chave estão entre 25 e 35. Depois de recuperar todas as linhas, exclua o scanner para limpar os recursos.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Introdução com um exemplo do Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Crie um aplicativo de ponta a ponta com [Analise o sentimento do Twitter em tempo real com o Apache HBase](./apache-hbase-tutorial-get-started-linux.md)