---
title: Como usar o envio em lote para melhorar o desempenho do aplicativo
description: O tópico fornece evidências de que o envio em lote de operações de banco de dados melhora muito a velocidade e a escalabilidade do banco de dados SQL do Azure e dos aplicativos do Azure SQL Instância Gerenciada Embora essas técnicas de envio em lote funcionem para qualquer banco de dados SQL, o foco do artigo está no Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 7f45e7d1515f0d6fc4467b36d95242ef8697c75d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641388"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Como usar o envio em lote para melhorar o desempenho do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada do aplicativo
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As operações de envio em lote para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada aprimoram significativamente o desempenho e a escalabilidade de seus aplicativos. Para entender os benefícios, a primeira parte deste artigo aborda alguns exemplos de resultados de teste que comparam solicitações sequenciais e em lote a um banco de dados no banco de dados SQL do Azure ou no Azure SQL Instância Gerenciada. O restante do artigo mostra as técnicas, os cenários e considerações para ajudar você a usar o envio em lote com sucesso em seus aplicativos do Azure.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Por que o envio em lote é importante para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada?

Envio de chamadas em lote para um serviço remoto é uma estratégia conhecida para aumentar o desempenho e a escalabilidade. Há custos de processamento fixos para todas as interações com um serviço remoto, como a serialização, a transferência de rede e a desserialização. O empacotamento de muitas transações separadas em um único lote minimiza esses custos.

Neste artigo, queremos examinar várias estratégias e cenários de envio em lote. Embora essas estratégias também sejam importantes para aplicativos locais que usam SQL Server, há várias razões para realçar o uso de envio em lote para o banco de dados SQL do Azure e o Instância Gerenciada SQL do Azure:

* Há potencialmente maior latência de rede ao acessar o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada, especialmente se você estiver acessando o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada de fora do mesmo datacenter Microsoft Azure.
* As características de multilocatários do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada significa que a eficiência da camada de acesso a data se correlaciona com a escalabilidade geral do banco de dado. Em resposta ao uso em excesso de cotas predefinidas, o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada podem reduzir a taxa de transferência ou responder com exceções de limitação. As eficiências, como o envio em lote, permitem que você faça mais trabalho antes de atingir esses limites.
* O envio em lote também é eficaz para arquiteturas que usam vários bancos de dados (fragmentação). A eficiência da sua interação com cada unidade de banco de dados ainda é um fator fundamental em sua escalabilidade total.

Um dos benefícios de usar o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada é que você não precisa gerenciar os servidores que hospedam o banco de dados. No entanto, essa infraestrutura gerenciada também significa que você precisa pensar de forma diferente sobre a otimizações do banco de dados. Você não pode mais procurar melhorar a infraestrutura de hardware ou de rede do banco de dados. O Microsoft Azure controla esses ambientes. A área principal que você pode controlar é como seu aplicativo interage com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada. O envio em lote é uma dessas otimizações.

A primeira parte deste artigo examina várias técnicas de envio em lote para aplicativos .NET que usam o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada. As duas últimas seções abordam diretrizes e cenários de envio em lote.

## <a name="batching-strategies"></a>Estratégias de envio em lote

### <a name="note-about-timing-results-in-this-article"></a>Observação sobre os resultados de tempo neste artigo

> [!NOTE]
> Os resultados não são parâmetros de comparação, mas têm como finalidade mostrar o **desempenho relativo**. Os intervalos se baseiam em uma média de pelo menos 10 execuções de teste. As operações são inserções em uma tabela vazia. Esses testes foram medidos antes do V12 e não correspondem necessariamente à produtividade que você pode obter em um banco de dados V12 usando as novas [camadas de serviço DTU](database/service-tiers-dtu.md) ou [camadas de serviço vCore](database/service-tiers-vcore.md). O benefício relativo da técnica de envio em lote deve ser semelhante.

### <a name="transactions"></a>Transactions

Parece estranho iniciar uma análise do envio em lote discutindo transações. Mas o uso de transações no lado do cliente tem um efeito sutil no envio em lote do lado do servidor que melhora o desempenho. E é possível adicionar transações com apenas algumas linhas de código, portanto elas fornecem uma maneira rápida de melhorar o desempenho de operações sequenciais.

Considere o seguinte código C# que contém uma sequência de operações de inserção e de atualização em uma tabela simples.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

O seguinte código ADO.NET executa sequencialmente essas operações.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

A melhor maneira de otimizar esse código é implementar alguma forma de envio em lote do lado do cliente dessas chamadas. Mas há uma maneira simples de aumentar o desempenho do código simplesmente encapsulando a sequência de chamadas em uma transação. Este é o mesmo código que usa uma transação.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Na verdade, as transações estão sendo usadas nos dois exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita encapsula todas as chamadas. De acordo com a documentação do [log de transações write-ahead](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15&preserve-view=true#WAL), os registros de log são liberados para o disco quando a transação é confirmada. Então, incluindo mais chamadas em uma transação, a gravação no log de transações pode atrasar até que a transação seja confirmada. Na verdade, você está habilitando o envio em lote para as gravações no log de transações do servidor.

A tabela a seguir mostra alguns resultados de testes ad hoc. Os testes executaram as mesmas inserções sequenciais, com e sem transações. Para obter uma perspectiva maior, o primeiro conjunto de testes foi executado remotamente de um laptop para o banco de dados no Microsoft Azure. O segundo conjunto de testes foi executado de um serviço de nuvem e de um banco de dados localizados no mesmo datacenter do Microsoft Azure (Oeste dos Estados Unidos). A tabela a seguir mostra a duração em milissegundos de inserções sequenciais, com e sem transações.

**De Local para o Azure**:

| Operations | Nenhuma transação (MS) | Com transação (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Do Azure para o Azure (mesmo datacenter)**:

| Operations | Nenhuma transação (MS) | Com transação (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Com base nos resultados do teste anterior, a disposição de uma única operação em uma transação reduz o desempenho. Mas, à medida que você aumenta o número de operações em uma única transação, o aprimoramento do desempenho fica mais evidente. A diferença de desempenho também é mais perceptível quando todas as operações ocorrem dentro do datacenter do Microsoft Azure. A maior latência de usar o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada de fora do Microsoft Azure datacenter ultrapassa o ganho de desempenho do uso de transações.

Embora o uso de transações possa aumentar o desempenho, continue [seguindo as práticas recomendadas para transações e conexões](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105)). Mantenha a transação a mais curta possível, e feche a conexão do banco de dados após a conclusão do trabalho. A instrução using no exemplo anterior garante o fechamento da conexão após a conclusão do bloco de códigos subsequente.

O exemplo anterior demonstra que você pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. As transações oferecem uma maneira rápida de melhorar o desempenho do código que faz as operações de inserção sequencial, atualização e de exclusão. No entanto, para obter o melhor desempenho, considere alterar o código ainda mais para aproveitar o envio em lote no lado do cliente, por exemplo, com os parâmetros com valor de tabela.

Para saber mais sobre transações no ADO.NET, consulte [Transações locais no ADO.NET](/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parâmetros com valor de tabela

Os parâmetros com valor de tabela oferecem suporte a tipos de tabela definidos pelo usuário como parâmetros em instruções Transact-SQL, procedimentos armazenados e funções. Essa técnica de envio em lote no lado do cliente permite o envio de várias linhas de dados dentro do parâmetro com valor de tabela. Para usar os parâmetros com valor de tabela, primeiro defina um tipo de tabela. A instrução Transact-SQL a seguir cria um tipo de tabela denominado **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

No código, você cria uma **DataTable** com exatamente os mesmos nomes e tipos do tipo de tabela. Passe essa **DataTable** em um parâmetro em uma consulta de texto ou em uma chamada de procedimento armazenado. O exemplo a seguir mostra detalhes desta técnica:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

No exemplo anterior, o objeto **SqlCommand** insere linhas de um parâmetro com valor de tabela, **\@ TestTvp**. O objeto **DataTable** criado anteriormente é atribuído a esse parâmetro com o método **SqlCommand.Parameters.Add**. O envio em lote de inserções em uma chamada aumenta consideravelmente o desempenho com inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, use um procedimento armazenado e não um comando baseado em texto. O comando Transact-SQL a seguir cria um procedimento armazenado que utiliza o parâmetro com valor de tabela **SimpleTestTableType** .

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Em seguida, altere a declaração do objeto **SqlCommand** no exemplo de código anterior para o seguinte.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Na maioria dos casos, os parâmetros com valor de tabela têm um desempenho equivalente ou superior às outras técnicas de envio em lote. Normalmente, a preferência fica com os parâmetros com valor de tabela, pois são mais flexíveis do que as outras opções. Por exemplo, outras técnicas, como cópia em massa do SQL, só permitem a inserção de novas linhas. Porém, com os parâmetros com valor de tabela, você pode usar lógica no procedimento armazenado para determinar quais linhas serão atualizações e quais serão inserções. O tipo de tabela também pode ser modificado para conter uma coluna de "operação" que indica se a linha especificada deve ser inserida, atualizada ou excluída.

A tabela a seguir mostra os resultados de teste ad hoc para o uso de parâmetros com valor de tabela em milissegundos.

| Operations | Local para o Azure (MS) | Mesmo datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

O ganho de desempenho com o envio em lote fica imediatamente aparente. No teste sequencial anterior, 1000 operações levaram 129 segundos fora do datacenter e 21 segundos dentro do datacenter. Mas, com os parâmetros com valor de tabela, 1000 operações levam somente 2,6 segundos fora do datacenter e 0,4 segundos dentro do datacenter.

Para saber mais sobre parâmetros com valor de tabela, consulte [Parâmetros com valor de tabela](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>Cópia em massa do SQL

Cópia em massa do SQL é outra maneira de inserir grandes quantidades de dados em um banco de dados de destino. Aplicativos .NET podem usar a classe **SqlBulkCopy** para executar operações de inserção em massa. **SqlBulkCopy** tem uma função semelhante à ferramenta de linha de comando, **Bcp.exe**, ou à instrução Transact-SQL, **BULK INSERT**. O exemplo de código a seguir mostra como copiar em massa as linhas na **DataTable** de origem, tabela, para a tabela de destino, MyTable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Há alguns casos nos quais é preferível usar a cópia em massa do que os parâmetros com valor de tabela. Consulte a tabela de comparação de Parâmetros com valor de tabela versus operações BULK INSERT no artigo [Parâmetros com valor de tabela](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

Os seguintes resultados de teste ad hoc mostram o desempenho do envio em lote com **SqlBulkCopy** em milissegundos.

| Operations | Local para o Azure (MS) | Mesmo datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Em lotes menores, o uso dos parâmetros com valor de tabela superaram a classe **SqlBulkCopy** . No entanto, o **SqlBulkCopy** realizou 12-31% mais rápido do que os parâmetros com valor de tabela para os testes de 1.000 e 10.000 linhas. Assim como os parâmetros com valor de tabela, **SqlBulkCopy** é uma boa opção para inserções em lotes, especialmente quando comparado ao desempenho de operações que não são feitas em lotes.

Para obter mais informações sobre cópia em massa no ADO.NET, consulte [operações de cópia em massa](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções INSERT com parâmetros de várias linhas

Uma alternativa para lotes pequenos é a construção de uma grande instrução INSERT com parâmetros que insira várias linhas. O exemplo de código a seguir demonstra esta técnica.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Esse exemplo tem como objetivo mostrar o conceito básico. Um cenário mais realista percorreria as entidades necessárias a fim de construir simultaneamente a cadeia de caracteres de consulta e os parâmetros de comando. Você está limitado a um total de 2100 parâmetros de consulta, e isso limita o número total de linhas que podem ser processadas dessa maneira.

Os seguintes resultados de teste ad hoc mostram o desempenho desse tipo de instrução INSERT em milissegundos.

| Operations | Parâmetros com valor de tabela (ms) | Instrução INSERT única (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Essa abordagem pode ser ligeiramente mais rápida para lotes com menos de 100 linhas. Embora o aprimoramento seja pequeno, essa técnica é outra opção que pode funcionar bem em seu cenário de aplicativo específico.

### <a name="dataadapter"></a>DataAdapter

A classe **DataAdapter** permite que você modifique um objeto **DataSet** e envie as alterações como operações INSERT, UPDATE e DELETE. Se você estiver usando o **DataAdapter** dessa maneira, é importante observar são realizadas chamadas separadas para cada operação distinta. Para melhorar o desempenho, use a propriedade **UpdateBatchSize** de acordo com o número de operações que devem ser enviadas em lote por vez. Para obter mais informações, consulte [Executando operações em lotes usando DataAdapters](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters).

### <a name="entity-framework"></a>Entity Framework

O [Entity Framework Core](/ef/efcore-and-ef6/#saving-data) dá suporte ao envio em lote.

### <a name="xml"></a>XML

Para abordarmos tudo, achamos que é importante falar sobre XML como uma estratégia de envio em lote. No entanto, o uso de XML não apresenta vantagens sobre outros métodos e impõe várias desvantagens. A abordagem é semelhante aos parâmetros com valor de tabela, mas um arquivo ou cadeia de caracteres XML é passado para um procedimento armazenado, em vez de uma tabela definida pelo usuário. O procedimento armazenado analisa os comandos no procedimento armazenado.

Há várias desvantagens nessa abordagem:

* Trabalhar com XML pode ser complicado e pode apresentar muitos erros.
* Analisar o XML no banco de dados pode causar a utilização de muita CPU.
* Na maioria dos casos, esse método é mais lento se comparado ao uso de parâmetros com valor de tabela.

Por esses motivos, o uso de XML para consultas em lote não é recomendado.

## <a name="batching-considerations"></a>Considerações sobre o envio em lote

As seções a seguir fornecem mais diretrizes para o uso de envio em lote no banco de dados SQL do Azure e em aplicativos do Azure SQL Instância Gerenciada.

### <a name="tradeoffs"></a>Compensações

Dependendo de sua arquitetura, o envio em lote pode envolver uma compensação entre desempenho e resiliência. Por exemplo, considere o cenário no qual sua função fica inesperadamente inativa. Se você perder uma linha de dados, o impacto é menor do que o impacto de perder um lote grande de linhas não enviadas. Há um risco maior quando você armazena em buffer as linhas antes de enviá-las ao banco de dados em um período especificado.

Devido a essa compensação, avalie o tipo das operações que você envia em lote. Utilize o envio em lote de forma mais agressiva (lotes maiores e períodos maiores) com dados menos críticos.

### <a name="batch-size"></a>Tamanho do lote

Em nossos testes, geralmente não houve vantagem em dividir lotes grandes em partes menores. Na verdade, frequentemente essa subdivisão resultou em um desempenho mais lento do que enviar um único lote grande. Por exemplo, considere um cenário no qual você deseja inserir 1000 linhas. A tabela a seguir mostra quanto tempo leva para usar parâmetros com valor de tabela para inserir 1000 linhas divididas em lotes menores.

| Tamanho do lote | Iterações | Parâmetros com valor de tabela (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Veja que o melhor desempenho para 1000 linhas é enviá-las ao mesmo tempo. Em outros testes (não mostrados aqui), houve um pequeno lucro de desempenho para quebrar um lote de 10000 linhas em dois lotes de 5000. Como o esquema da tabela para esses testes é relativamente simples, você pode executar testes em seus dados e tamanhos de lote específicos a fim de verificar essas conclusões.

Outro fator a ser considerado é que, se o lote total se tornar muito grande, o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada poderá limitar e recusar a confirmação do lote. Para obter melhores resultados, teste seu cenário específico para determinar se há um tamanho de lote ideal. Torne o tamanho do lote configurável no runtime para permitir ajustes rápidos com base no desempenho ou em erros.

Por fim, equilibre o tamanho do lote com os riscos associados ao envio em lote. Se houver erros transitórios ou a função falhar, considere as consequências da repetição da operação ou da perda dos dados no lote.

### <a name="parallel-processing"></a>Processamento paralelo

E se você adotasse a abordagem de redução do tamanho de lote, mas usasse vários threads para executar o trabalho? Novamente, nossos testes mostraram que vários lotes menores multithreaded geralmente apresentaram um desempenho pior do que um único lote maior. O teste a seguir tenta inserir 1000 linhas em um ou mais lotes paralelos. Este teste mostra como uma quantidade maior de lotes simultâneos na verdade diminuiu o desempenho.

| Tamanho do lote [Iterações] | Dois threads (ms) | Quatro threads (ms) | Seis threads (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Veja a [observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Há várias razões possíveis para a degradação do desempenho devido ao paralelismo:

* Há várias chamadas simultâneas de rede em vez de uma.
* Várias operações em uma única tabela podem resultar em contenção e bloqueio.
* Há sobrecargas associadas ao multithreading.
* O fardo de abrir várias conexões supera o benefício do processamento paralelo.

Se você selecionar tabelas ou bancos de dados diferentes, poderá perceber algum ganho de desempenho com essa estratégia. A fragmentação ou federações do banco de dados seria um cenário para essa abordagem. A fragmentação usa vários bancos de dados e encaminha dados diferentes para cada banco de dados. Se cada lote pequeno for encaminhada para um banco de dados diferente, então a execução das operações em paralelo pode ser mais eficiente. No entanto, o ganho de desempenho não é considerável o suficiente para usá-lo como base para uma decisão de uso da fragmentação de banco de dados em sua solução.

Em alguns designs, a execução paralela de lotes menores pode resultar em uma produtividade maior das solicitações em um sistema sob carga. Nesse caso, embora seja mais rápido processar um único lote maior, o processamento de vários lotes em paralelo pode ser mais eficiente.

Se você usar a execução paralela, considere a possibilidade de controlar o número máximo de threads de trabalho. Uma quantidade menor poderia resultar em menos contenção e um tempo de execução mais rápido. Além disso, considere a carga adicional que isso impõe sobre o banco de dados de destino, tanto nas conexões quanto nas transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados

As orientações típicas sobre o desempenho do banco de dados também dizem respeito ao envio em lote. Por exemplo, ocorre a redução do desempenho de inserção para tabelas com uma chave primária grande ou vários índices não clusterizados.

Se parâmetros com valor de tabela usarem um procedimento armazenado, você poderá usar o comando **SET NOCOUNT ON** no início do procedimento. Essa instrução suprime o retorno da contagem de linhas afetadas no procedimento. No entanto, em nossos testes, o uso de **SET NOCOUNT ON** não teve qualquer efeito ou diminuiu o desempenho. O procedimento armazenado de teste foi simples com um único comando **INSERT** do parâmetro com valor de tabela. É possível que outros procedimentos armazenados mais complexos possam se beneficiar dessa instrução. Mas não presuma que a adição de **SET NOCOUNT ON** ao seu procedimento armazenado melhora o desempenho automaticamente. Para entender o efeito, teste o procedimento armazenado com e sem a instrução **SET NOCOUNT ON** instrução.

## <a name="batching-scenarios"></a>Cenários de envio em lote

As seções a seguir descrevem como usar os parâmetros com valor de tabela em três cenários de aplicativo. O primeiro cenário mostra como o armazenamento em buffer e o envio em lote podem trabalhar juntos. O segundo cenário melhora o desempenho por meio da execução de operações mestre-detalhes em uma chamada com um único procedimento armazenado. O cenário final mostra como usar parâmetros com valor de tabela em uma operação "UPSERT".

### <a name="buffering"></a>de resposta

Embora alguns cenários sejam candidatos óbvios ao envio em lote, há muitos cenários que poderiam se beneficiar do envio em lote por meio do processamento atrasado. No entanto, o processamento atrasado também representa um risco maior de que os dados sejam perdidos no caso de uma falha inesperada. É importante entender esse risco e considerar as consequências.

Por exemplo, considere um aplicativo Web que controla o histórico de navegação de cada usuário. Em cada solicitação de página, o aplicativo pode fazer uma chamada de banco de dados para registrar a exibição de página do usuário. Mas o desempenho e a escalabilidade mais altos podem ser obtidos armazenando em buffer as atividades de navegação dos usuários e enviando esses dados para o banco de dado em lotes. Você pode disparar a atualização do banco de dados por tempo decorrido e/ou tamanho do buffer. Por exemplo, uma regra poderia especificar que o lote deve ser processado após 20 segundos, ou quando o buffer atingir 1000 itens.

O exemplo de código a seguir usa [Extensões Reativas - Rx](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) para processar eventos em buffer gerados por uma classe de monitoramento. Quando o buffer é preenchido ou um tempo limite é atingido, o lote de dados do usuário é enviado ao banco de dados com um parâmetro com valor de tabela.

A classe NavHistoryData a seguir modela os detalhes de navegação do usuário. Ela contém informações básicas, como o identificador do usuário, a URL acessada e o tempo de acesso.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

A classe NavHistoryDataMonitor é responsável pelo armazenamento em buffer dos dados de navegação do usuário no banco de dados. Ela contém um método, RecordUserNavigationEntry, que responde gerando um evento **OnAdded** . O código a seguir mostra a lógica do construtor que usa Rx para criar uma coleção observável com base no evento. Em seguida, ele assina essa coleção observável com o método de Buffer. A sobrecarga especifica que o buffer deve ser enviado a cada 20 segundos ou 1000 entradas.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

O manipulador converte todos os itens armazenados em buffer em um tipo com valor de tabela e passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para as classes NavHistoryDataEventArgs e NavHistoryDataMonitor.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

O manipulador converte todos os itens armazenados em buffer em um tipo com valor de tabela e passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para as classes NavHistoryDataEventArgs e NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Para usar essa classe de armazenamento em buffer, o aplicativo cria um objeto NavHistoryDataMonitor estático. Sempre que um usuário acessa uma página, o aplicativo chama o método NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica do buffer continua a fim de enviar em lotes essas entradas ao banco de dados.

### <a name="master-detail"></a>Detalhes da tabela mestra

Parâmetros com valor de tabela são úteis para cenários INSERT simples. No entanto, pode ser mais desafiador executar inserções em lotes que envolvem mais de uma tabela. O cenário de "mestre/detalhes" é um bom exemplo. A tabela mestra identifica a entidade principal. Uma ou mais tabelas de detalhes armazenam mais dados sobre a entidade. Nesse cenário, as relações de chave estrangeiras impõem a relação de detalhes a uma entidade mestre exclusiva. Considere uma versão simplificada de uma tabela PurchaseOrder e sua tabela associada OrderDetail. O Transact-SQL a seguir cria a tabela PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Cada pedido contém uma ou mais compras de produtos. Essas informações são capturadas na tabela PurchaseOrderDetail. O Transact-SQL a seguir cria a tabela PurchaseOrderDetail com cinco colunas: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

A coluna OrderID na tabela PurchaseOrderDetail deve fazer referência a um pedido da tabela PurchaseOrder. A seguinte definição de uma chave estrangeira impõe essa restrição.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Para usar parâmetros com valor de tabela, você deve ter um tipo de tabela definido pelo usuário para cada tabela de destino.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Em seguida, defina um procedimento armazenado que aceite tabelas desses tipos. Esse procedimento permite que um aplicativo envie em lote localmente um conjunto de pedidos e detalhes do pedido em uma única chamada. O seguinte Transact-SQL fornece a declaração completa do procedimento armazenado para esse exemplo de ordem de compra.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

Nesse exemplo, a tabela @IdentityLink definida localmente armazena os valores reais de OrderID das linhas recentemente inseridas. Esses identificadores de pedido são diferentes dos valores temporários de OrderID nos parâmetros com valor de tabela @orders e @details. Por esse motivo, a tabela @IdentityLink conecta os valores de OrderID do parâmetro @orders com os valores reais de OrderID para as novas linhas na tabela PurchaseOrder. Após esta etapa, a tabela @IdentityLink pode facilitar a inserção dos detalhes do pedido com o OrderID, o que atende à restrição de chave estrangeira.

Esse procedimento armazenado pode ser usado do código ou de outras chamadas Transact-SQL. Consulte a seção de parâmetros com valor de tabela deste documento para obter um exemplo de código. O Transact-SQL a seguir mostra como chamar o sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Essa solução permite que cada lote use um conjunto de valores de OrderID que começam com 1. Os valores temporários de OrderID descrevem as relações no lote, mas os valores reais de OrderID são determinados no momento da operação de inserção. Você pode executar as mesmas instruções do exemplo anterior repetidamente e gerar pedidos exclusivos no banco de dados. Por esse motivo, considere a adição de mais código ou lógica de banco de dados que impeça os pedidos duplicados ao usar esta técnica de envio em lote.

Este exemplo demonstra que até mesmo as operações de banco de dados mais complexas, como operações de mestre-detalhes, podem ser enviadas em lote usando parâmetros com valor de tabela.

### <a name="upsert"></a>UPSERT

Outro cenário de envio em lote envolve a atualização simultânea de linhas existentes e a inserção de novas linhas. Essa operação às vezes é chamada de operação "UPSERT" (atualização + inserção). Em vez de fazer chamadas separadas para INSERT e UPDATE, a instrução MERGE é mais adequada para essa tarefa. A instrução MERGE pode executar as duas operações, inserção e atualização, em uma única chamada.

Parâmetros com valor de tabela podem ser usados com a instrução MERGE para executar atualizações e inserções. Por exemplo, considere uma tabela simplificada de funcionários que contém as seguintes colunas: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

Neste exemplo, você pode usar o fato de que o SocialSecurityNumber é exclusivo para a execução de uma instrução MERGE de vários funcionários. Primeiro, crie o tipo de tabela definido pelo usuário:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Em seguida, crie um procedimento armazenado ou escreva um código que use a instrução MERGE para executar a atualização e a inserção. O exemplo a seguir usa a instrução MERGE em um parâmetro com valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo da tabela @employees não é mostrado aqui.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Para saber mais, consulte a documentação e exemplos da instrução MERGE. Embora o mesmo trabalho possa ser executado em uma chamada de procedimento armazenado com várias etapas, com operações INSERT e UPDATE separadas, a instrução MERGE é mais eficiente. O código do banco de dados também pode construir chamadas Transact-SQL que usam a instrução MERGE diretamente sem exigir duas chamadas de banco de dados para INSERT e UPDATE.

## <a name="recommendation-summary"></a>Resumo de recomendações

A lista a seguir fornece um resumo das recomendações de envio em lote discutidas neste artigo:

* Use o armazenamento em buffer e o envio em lote para aumentar o desempenho e a escalabilidade do banco de dados SQL do Azure e dos aplicativos do Azure SQL Instância Gerenciada.
* Entenda as compensações entre o envio em lote/armazenamento em buffer e resiliência. Durante uma falha de função, o risco de perder um lote não processado de dados críticos para os negócios pode superar o benefício do desempenho do envio em lote.
* Tente manter todas as chamadas para o banco de dados em um único datacenter para reduzir a latência.
* Se você escolher uma única técnica de envio em lote, os parâmetros com valor de tabela oferecem o melhor desempenho e flexibilidade.
* Para obter o desempenho mais rápido de inserção, siga estas diretrizes gerais, mas teste seu cenário:
  * Para menos de 100 linhas, use um único comando INSERT com parâmetros.
  * Para menos de 1000 linhas, use parâmetros com valor de tabela.
  * Para 1000 linhas ou mais, use SqlBulkCopy.
* Para operações de atualização e exclusão, use parâmetros com valor de tabela com a lógica de procedimento armazenado que determina a operação correta em cada linha no parâmetro de tabela.
* Diretrizes para o tamanho do lote:
  * Use os maiores tamanhos de lote adequados aos seus requisitos de aplicativo e de negócios.
  * Equilibre o ganho de desempenho de lotes grandes com os riscos de falhas catastróficas ou temporárias. Qual é a consequência de repetições ou perda de dados no lote?
  * Teste o maior tamanho de lote para verificar se o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada não o rejeita.
  * Crie definições de configuração que controlam o envio em lote, como o tamanho do lote ou o período de armazenamento em buffer. Essas configurações fornecem flexibilidade. Você pode alterar o comportamento do lote em produção sem reimplantar o serviço de nuvem.
* Evite a execução paralela de lotes que operam em uma única tabela em um banco de dados. Se você optar por dividir um único lote entre vários threads de trabalho, execute testes para determinar o número ideal de threads. Após um limite não especificado, uma quantidade maior de threads diminuirá o desempenho em vez de aumentá-lo.
* Considere o armazenamento em buffer de acordo com o tamanho e o tempo como uma maneira de implementar o envio em lote para mais cenários.

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentrou em como o design do banco de dados e as técnicas de codificação relacionadas ao envio em lote podem melhorar o desempenho e a escalabilidade do aplicativo. Mas isso é apenas um fator em sua estratégia geral. Para obter mais maneiras de melhorar o desempenho e a escalabilidade, consulte [diretrizes de desempenho do banco de dados](database/performance-guidance.md) e [Considerações sobre preço e desempenho para um pool elástico](database/elastic-pool-overview.md).