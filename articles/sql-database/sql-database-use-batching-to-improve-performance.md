---
title: Como usar o envio em lote para melhorar o desempenho do aplicativo Banco de Dados SQL do Azure
description: "O tópico fornece provas de que as operações de banco de dados de envio em lote melhorar consideravelmente a velocidade e a escalabilidade de seus aplicativos de Banco de Dados SQL do Azure. Embora essas técnicas de envio em lote funcionem para qualquer banco de dados do SQL Server, o foco do artigo é no Azure."
services: sql-database
documentationcenter: na
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 563862ca-c65a-46f6-975d-10df7ff6aa9c
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/12/2016
ms.author: sstein
ms.openlocfilehash: 22cff47444306e599325ba3035d83a0266d69c72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como usar o envio em lote para melhorar o desempenho do aplicativo Banco de Dados SQL
O envio de operações em lote para o Banco de Dados SQL do Azure melhora consideravelmente o desempenho e a escalabilidade dos aplicativos. Para compreender os benefícios, a primeira parte deste artigo aborda alguns exemplos de resultados de teste que comparam solicitações sequenciais e em lote para um Banco de Dados SQL. O restante do artigo mostra as técnicas, os cenários e considerações para ajudar você a usar o envio em lote com sucesso em seus aplicativos do Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Por que o envio em lote é importante para o Banco de Dados SQL?
Envio de chamadas em lote para um serviço remoto é uma estratégia conhecida para aumentar o desempenho e a escalabilidade. Há custos de processamento fixos para todas as interações com um serviço remoto, como a serialização, a transferência de rede e a desserialização. O empacotamento de muitas transações separadas em um único lote minimiza esses custos.

Neste documento, queremos examinar as vária estratégias e cenários de envio em lote do Banco de Dados SQL. Embora essas estratégias também sejam importantes para aplicativos locais que usam o SQL Server, há vários motivos para destacar o uso do envio em lote para o Banco de Dados SQL:

* Há potencialmente maior latência de rede no acesso ao Banco de Dados SQL, especialmente se você estiver acessando o Banco de Dados SQL fora do mesmo datacenter do Microsoft Azure.
* As características multilocatário do Banco de Dados SQL significam que a eficiência da camada de acesso a dados correlaciona com a escalabilidade geral do banco de dados. O Banco de Dados SQL deve impedir que qualquer locatário/usuário monopolize os recursos do banco de dados em detrimento de outros locatários. Em resposta ao uso em excesso das cotas predefinidas, o Banco de Dados SQL pode reduzir a produtividade ou responder com exceções de limitação. As eficiências, como o envio em lote, permitem que você execute mais trabalhos no Banco de Dados SQL antes de alcançar esses limites. 
* O envio em lote também é eficaz para arquiteturas que usam vários bancos de dados (fragmentação). A eficiência da sua interação com cada unidade de banco de dados ainda é um fator fundamental em sua escalabilidade total. 

Um dos benefícios de usar o Banco de Dados SQL é que você não precisa gerenciar os servidores que hospedam o banco de dados. No entanto, essa infraestrutura gerenciada também significa que você precisa pensar de forma diferente sobre a otimizações do banco de dados. Você não pode mais procurar melhorar a infraestrutura de hardware ou de rede do banco de dados. O Microsoft Azure controla esses ambientes. A principal área que você pode controlar é como seu aplicativo interage com o Banco de Dados SQL. O envio em lote é uma dessas otimizações. 

A primeira parte do documento examina várias técnicas de envio em lote para aplicativos .NET que usam o Banco de Dados SQL. As duas últimas seções abordam diretrizes e cenários de envio em lote.

## <a name="batching-strategies"></a>Estratégias de envio em lote
### <a name="note-about-timing-results-in-this-topic"></a>Observação sobre os resultados de tempo neste tópico
> [!NOTE]
> Os resultados não são parâmetros de comparação, mas têm como finalidade mostrar o **desempenho relativo**. Os intervalos se baseiam em uma média de pelo menos 10 execuções de teste. As operações são inserções em uma tabela vazia. Esses testes foram medidos antes do V12 e não correspondem necessariamente à produtividade que você pode obter em um banco de dados V12 usando as novas [camadas de serviço](sql-database-service-tiers.md). O benefício relativo da técnica de envio em lote deve ser semelhante.
> 
> 

### <a name="transactions"></a>Transações
Parece estranho iniciar uma análise do envio em lote discutindo transações. Mas o uso de transações no lado do cliente tem um efeito sutil no envio em lote do lado do servidor que melhora o desempenho. E é possível adicionar transações com apenas algumas linhas de código, portanto elas fornecem uma maneira rápida de melhorar o desempenho de operações sequenciais.

Considere o seguinte código C# que contém uma sequência de operações de inserção e de atualização em uma tabela simples.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

O seguinte código ADO.NET executa sequencialmente essas operações.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

A melhor maneira de otimizar esse código é implementar alguma forma de envio em lote do lado do cliente dessas chamadas. Mas há uma maneira simples de aumentar o desempenho do código simplesmente encapsulando a sequência de chamadas em uma transação. Este é o mesmo código que usa uma transação.

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

Na verdade, as transações estão sendo usadas nos dois exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita encapsula todas as chamadas. Conforme a documentação do [log de transações write-ahead](https://msdn.microsoft.com/library/ms186259.aspx), registros de log são liberados no disco quando a transação é confirmada. Então, incluindo mais chamadas em uma transação, a gravação no log de transações pode atrasar até que a transação seja confirmada. Na verdade, você está habilitando o envio em lote das gravações no log de transações do servidor.

A tabela a seguir mostra alguns resultados de teste ad hoc. Os testes executaram as mesmas inserções sequenciais, com e sem transações. Para obter uma perspectiva maior, o primeiro conjunto de testes foi executado remotamente de um laptop para o banco de dados no Microsoft Azure. O segundo conjunto de testes foi executado de um serviço de nuvem e de um banco de dados localizados no mesmo datacenter do Microsoft Azure (Oeste dos Estados Unidos). A tabela a seguir mostra a duração em milissegundos de inserções sequenciais, com e sem transações.

**Local para o Azure**:

| Operações | Sem transação (ms) | Com transação (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Do Azure para o Azure (mesmo datacenter)**:

| Operações | Sem transação (ms) | Com transação (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

Com base nos resultados do teste anterior, a disposição de uma única operação em uma transação reduz o desempenho. Mas, à medida que você aumenta o número de operações em uma única transação, o aprimoramento do desempenho fica mais evidente. A diferença de desempenho também é mais perceptível quando todas as operações ocorrem dentro do datacenter do Microsoft Azure. O aumento da latência devido ao uso do Banco de Dados SQL fora do datacenter do Microsoft Azure ofusca o ganho de desempenho do uso das transações.

Embora o uso das transações possa aumentar o desempenho, continue a [seguir as práticas recomendadas para transações e conexões](https://msdn.microsoft.com/library/ms187484.aspx). Mantenha a transação a mais curta possível, e feche a conexão do banco de dados após a conclusão do trabalho. A instrução using no exemplo anterior garante o fechamento da conexão após a conclusão do bloco de códigos subsequente.

O exemplo anterior demonstra que você pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. As transações oferecem uma maneira rápida de melhorar o desempenho do código que faz as operações de inserção sequencial, atualização e de exclusão. No entanto, para obter o melhor desempenho, considere alterar o código ainda mais para aproveitar o envio em lote no lado do cliente, por exemplo, com os parâmetros com valor de tabela.

Para saber mais sobre transações no ADO.NET, consulte [Transações locais no ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parâmetros com valor de tabela
Os parâmetros com valor de tabela oferecem suporte a tipos de tabela definidos pelo usuário como parâmetros em instruções Transact-SQL, procedimentos armazenados e funções. Essa técnica de envio em lote no lado do cliente permite o envio de várias linhas de dados dentro do parâmetro com valor de tabela. Para usar os parâmetros com valor de tabela, primeiro defina um tipo de tabela. A instrução Transact-SQL a seguir cria um tipo de tabela denominado **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


No código, você cria uma **DataTable** com exatamente os mesmos nomes e tipos do tipo de tabela. Passe essa **DataTable** em um parâmetro em uma consulta de texto ou em uma chamada de procedimento armazenado. O exemplo a seguir mostra detalhes desta técnica:

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

No exemplo anterior, o objeto **SqlCommand** insere linhas de um parâmetro com valor de tabela, **@TestTvp**. O objeto **DataTable** criado anteriormente é atribuído a esse parâmetro com o método **SqlCommand.Parameters.Add**. O envio em lote de inserções em uma chamada aumenta consideravelmente o desempenho com inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, use um procedimento armazenado e não um comando baseado em texto. O comando Transact-SQL a seguir cria um procedimento armazenado que utiliza o parâmetro com valor de tabela **SimpleTestTableType** .

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Em seguida, altere a declaração do objeto **SqlCommand** no exemplo de código anterior para o seguinte.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Na maioria dos casos, os parâmetros com valor de tabela têm um desempenho equivalente ou superior às outras técnicas de envio em lote. Normalmente, a preferência fica com os parâmetros com valor de tabela, pois são mais flexíveis do que as outras opções. Por exemplo, outras técnicas, como cópia em massa do SQL, só permitem a inserção de novas linhas. Porém, com os parâmetros com valor de tabela, você pode usar lógica no procedimento armazenado para determinar quais linhas serão atualizações e quais serão inserções. O tipo de tabela também pode ser modificado para conter uma coluna "Operação" que indica se a linha especificada deve ser inserida, atualizada ou excluída.

A tabela a seguir mostra os resultados do teste ad hoc do uso de parâmetros com valor de tabela em milissegundos.

| Operações | Local para o Azure (ms) | Mesmo datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

O ganho de desempenho com o envio em lote fica imediatamente aparente. No teste sequencial anterior, 1000 operações levaram 129 segundos fora do datacenter e 21 segundos dentro do datacenter. Mas, com os parâmetros com valor de tabela, 1000 operações levam somente 2,6 segundos fora do datacenter e 0,4 segundos dentro do datacenter.

Para saber mais sobre parâmetros com valor de tabela, consulte [Parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Cópia em massa do SQL
Cópia em massa do SQL é outra maneira de inserir grandes quantidades de dados em um banco de dados de destino. Aplicativos .NET podem usar a classe **SqlBulkCopy** para executar operações de inserção em massa. **SqlBulkCopy** tem uma função semelhante à ferramenta de linha de comando, **Bcp.exe**, ou à instrução Transact-SQL, **BULK INSERT**. O exemplo de código a seguir mostra como copiar em massa as linhas na tabela de origem **DataTable**, para a tabela de destino no SQL Server, MyTable.

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

Há alguns casos nos quais é preferível usar a cópia em massa do que os parâmetros com valor de tabela. Consulte a tabela de comparação de Parâmetros com valor de tabela versus operações BULK INSERT no tópico [Parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb510489.aspx).

Os seguintes resultados do teste ad hoc mostram o desempenho do envio em lote com **SqlBulkCopy** em milissegundos.

| Operações | Local para o Azure (ms) | Mesmo datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

Em lotes menores, o uso dos parâmetros com valor de tabela superaram a classe **SqlBulkCopy** . No entanto, **SqlBulkCopy** teve um desempenho de 12 a 31% mais rápido do que os parâmetros com valor de tabela nos testes de 1.000 e 10.000 linhas. Assim como os parâmetros com valor de tabela, **SqlBulkCopy** é uma boa opção para inserções em lotes, especialmente quando comparado ao desempenho de operações que não são feitas em lotes.

Para saber mais sobre a cópia em massa no ADO.NET, consulte [Operações de cópia em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções INSERT com parâmetros de várias linhas
Uma alternativa para lotes pequenos é a construção de uma grande instrução INSERT com parâmetros que insira várias linhas. O exemplo de código a seguir demonstra esta técnica.

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


Esse exemplo tem como objetivo mostrar o conceito básico. Um cenário mais realista percorreria as entidades necessárias a fim de construir simultaneamente a cadeia de caracteres de consulta e os parâmetros de comando. Você está limitado a um total de 2100 parâmetros de consulta, e isso limita o número total de linhas que podem ser processadas dessa maneira.

Os seguintes resultados do teste ad hoc mostram o desempenho desse tipo de instrução insert em milissegundos.

| Operações | Parâmetros com valor de tabela (ms) | Instrução INSERT única (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

Essa abordagem pode ser ligeiramente mais rápida para lotes com menos de 100 linhas. Embora o aprimoramento seja pequeno, essa técnica é outra opção que pode funcionar bem em seu cenário de aplicativo específico.

### <a name="dataadapter"></a>DataAdapter
A classe **DataAdapter** permite que você modifique um objeto **DataSet** e envie as alterações como operações INSERT, UPDATE e DELETE. Se você estiver usando o **DataAdapter** dessa maneira, é importante observar são realizadas chamadas separadas para cada operação distinta. Para melhorar o desempenho, use a propriedade **UpdateBatchSize** de acordo com o número de operações que devem ser enviadas em lote por vez. Para saber mais, consulte [Executando operações em lote usando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entity Framework
No momento, o Entity Framework não oferece suporte para o envio em lote. Vários desenvolvedores da comunidade tentaram demonstrar soluções alternativas, como a substituição do método **SaveChanges** . Mas normalmente as soluções são complexas e personalizadas para o aplicativo e o modelo de dados. Atualmente, o projeto codeplex do Entity Framework tem uma página de discussão sobre essa solicitação de recurso. Para exibir esta discussão, confira [Anotações da reunião de design – 2 de agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Para abordarmos tudo, achamos que é importante falar sobre XML como uma estratégia de envio em lote. No entanto, o uso de XML não apresenta vantagens sobre outros métodos e impõe várias desvantagens. A abordagem é semelhante aos parâmetros com valor de tabela, mas um arquivo ou cadeia de caracteres XML é passado para um procedimento armazenado, em vez de uma tabela definida pelo usuário. O procedimento armazenado analisa os comandos no procedimento armazenado.

Há várias desvantagens nessa abordagem:

* Trabalhar com XML pode ser complicado e pode apresentar muitos erros.
* Analisar o XML no banco de dados pode causar a utilização de muita CPU.
* Na maioria dos casos, esse método é mais lento se comparado ao uso de parâmetros com valor de tabela.

Por esses motivos, o uso de XML para consultas em lote não é recomendado.

## <a name="batching-considerations"></a>Considerações sobre o envio em lote
As seções a seguir fornecem mais orientações sobre o uso do envio em lote em aplicativos do Banco de Dados SQL.

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
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

Veja que o melhor desempenho para 1000 linhas é enviá-las ao mesmo tempo. Em outros testes (não mostrados aqui), houve um pequeno ganho de desempenho ao dividir um lote de 10000 linhas em dois lotes de 5000. Como o esquema da tabela para esses testes é relativamente simples, você pode executar testes em seus dados e tamanhos de lote específicos a fim de verificar essas conclusões.

Outro fator a ser considerado é que, se o lote total ficar muito grande, o Banco de Dados SQL poderá aplicar uma limitação e recusar-se a confirmar o lote. Para obter melhores resultados, teste seu cenário específico para determinar se há um tamanho de lote ideal. Torne o tamanho do lote configurável no tempo de execução para permitir ajustes rápidos com base no desempenho ou em erros.

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
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste tópico](#note-about-timing-results-in-this-topic)
> 
> 

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

Se parâmetros com valor de tabela usarem um procedimento armazenado, você poderá usar o comando **SET NOCOUNT ON** no início do procedimento. Essa instrução suprime o retorno da contagem de linhas afetadas no procedimento. No entanto, em nossos testes, o uso de **SET NOCOUNT ON** não teve qualquer efeito ou diminuiu o desempenho. O procedimento armazenado de teste foi simples com um único comando **INSERT** do parâmetro com valor de tabela. É possível que outros procedimentos armazenados mais complexos possam se beneficiar dessa instrução. Mas não suponha que a adição de **SET NOCOUNT ON** ao procedimento armazenado aprimorará automaticamente o desempenho. Para entender o efeito, teste o procedimento armazenado com e sem a instrução **SET NOCOUNT ON** instrução.

## <a name="batching-scenarios"></a>Cenários de envio em lote
As seções a seguir descrevem como usar os parâmetros com valor de tabela em três cenários de aplicativo. O primeiro cenário mostra como o armazenamento em buffer e o envio em lote podem trabalhar juntos. O segundo cenário melhora o desempenho por meio da execução de operações mestre-detalhes em uma chamada com um único procedimento armazenado. O cenário final mostra como usar os parâmetros com valor de tabela em uma operação "UPSERT".

### <a name="buffering"></a>Armazenamento em buffer
Embora alguns cenários sejam candidatos óbvios ao envio em lote, há muitos cenários que poderiam se beneficiar do envio em lote por meio do processamento atrasado. No entanto, o processamento atrasado também representa um risco maior de que os dados sejam perdidos no caso de uma falha inesperada. É importante entender esse risco e considerar as consequências.

Por exemplo, considere um aplicativo Web que controla o histórico de navegação de cada usuário. Em cada solicitação de página, o aplicativo pode fazer uma chamada de banco de dados para registrar o modo de exibição de página do usuário. Mas é possível obter mais desempenho e escalabilidade por meio do armazenamento em buffer das atividades de navegação dos usuários e enviar esses dados para o banco de dados em lotes. Você pode disparar a atualização do banco de dados por tempo decorrido e/ou tamanho do buffer. Por exemplo, uma regra poderia especificar que o lote deve ser processado após 20 segundos, ou quando o buffer atingir 1000 itens.

O exemplo de código a seguir usa [Extensões Reativas - Rx](https://msdn.microsoft.com/data/gg577609) para processar eventos em buffer gerados por uma classe de monitoramento. Quando o buffer é preenchido ou um tempo limite é atingido, o lote de dados do usuário é enviado ao banco de dados com um parâmetro com valor de tabela.

A classe NavHistoryData a seguir modela os detalhes de navegação do usuário. Ela contém informações básicas, como o identificador do usuário, a URL acessada e o tempo de acesso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

A classe NavHistoryDataMonitor é responsável pelo armazenamento em buffer dos dados de navegação do usuário no banco de dados. Ela contém um método, RecordUserNavigationEntry, que responde gerando um evento **OnAdded** . O código a seguir mostra a lógica do construtor que usa Rx para criar uma coleção observável com base no evento. Em seguida, ele assina essa coleção observável com o método de Buffer. A sobrecarga especifica que o buffer deve ser enviado a cada 20 segundos ou 1000 entradas.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

O manipulador converte todos os itens armazenados em buffer em um tipo com valor de tabela e passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para as classes NavHistoryDataEventArgs e NavHistoryDataMonitor.

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

        public void RecordUserNavigationEntry(NavHistoryData data)
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

Para usar essa classe de armazenamento em buffer, o aplicativo cria um objeto NavHistoryDataMonitor estático. Sempre que um usuário acessa uma página, o aplicativo chama o método NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica do buffer continua a fim de enviar em lotes essas entradas ao banco de dados.

### <a name="master-detail"></a>Detalhes da tabela mestra
Parâmetros com valor de tabela são úteis para cenários INSERT simples. No entanto, pode ser mais desafiador executar inserções em lotes que envolvem mais de uma tabela. O cenário "mestre/detalhes" é um bom exemplo. A tabela mestra identifica a entidade principal. Uma ou mais tabelas de detalhes armazenam mais dados sobre a entidade. Nesse cenário, as relações de chave estrangeiras impõem a relação de detalhes a uma entidade mestre exclusiva. Considere uma versão simplificada de uma tabela PurchaseOrder e sua tabela associada OrderDetail. O Transact-SQL a seguir cria a tabela PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e Status.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Cada pedido contém uma ou mais compras de produtos. Essas informações são capturadas na tabela PurchaseOrderDetail. O Transact-SQL a seguir cria a tabela PurchaseOrderDetail com cinco colunas: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

A coluna OrderID na tabela PurchaseOrderDetail deve fazer referência a um pedido da tabela PurchaseOrder. A seguinte definição de uma chave estrangeira impõe essa restrição.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Para usar parâmetros com valor de tabela, você deve ter um tipo de tabela definido pelo usuário para cada tabela de destino.

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

Em seguida, defina um procedimento armazenado que aceite tabelas desses tipos. Esse procedimento permite que um aplicativo envie em lote localmente um conjunto de pedidos e detalhes do pedido em uma única chamada. O seguinte Transact-SQL fornece a declaração completa do procedimento armazenado para esse exemplo de ordem de compra.

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

Nesse exemplo, a tabela @IdentityLink definida localmente armazena os valores reais de OrderID das linhas recentemente inseridas. Esses identificadores de pedido são diferentes dos valores temporários de OrderID nos parâmetros com valor de tabela @orders e @details. Por esse motivo, a tabela @IdentityLink conecta os valores de OrderID do parâmetro @orders com os valores reais de OrderID para as novas linhas na tabela PurchaseOrder. Após esta etapa, a tabela @IdentityLink pode facilitar a inserção dos detalhes do pedido com o OrderID, o que atende à restrição de chave estrangeira.

Esse procedimento armazenado pode ser usado do código ou de outras chamadas Transact-SQL. Consulte a seção de parâmetros com valor de tabela deste documento para obter um exemplo de código. O Transact-SQL a seguir mostra como chamar o sp_InsertOrdersBatch.

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

Essa solução permite que cada lote use um conjunto de valores de OrderID que começam com 1. Os valores temporários de OrderID descrevem as relações no lote, mas os valores reais de OrderID são determinados no momento da operação de inserção. Você pode executar as mesmas instruções do exemplo anterior repetidamente e gerar pedidos exclusivos no banco de dados. Por esse motivo, considere a adição de mais código ou lógica de banco de dados que impeça os pedidos duplicados ao usar esta técnica de envio em lote.

Este exemplo demonstra que até mesmo as operações de banco de dados mais complexas, como operações de mestre-detalhes, podem ser enviadas em lote usando parâmetros com valor de tabela.

### <a name="upsert"></a>UPSERT
Outro cenário de envio em lote envolve a atualização simultânea de linhas existentes e a inserção de novas linhas. Essa operação é chamada às vezes de operação "UPSERT" (atualização + inserção). Em vez de fazer chamadas separadas para INSERT e UPDATE, a instrução MERGE é mais adequada para essa tarefa. A instrução MERGE pode executar as duas operações, inserção e atualização, em uma única chamada.

Parâmetros com valor de tabela podem ser usados com a instrução MERGE para executar atualizações e inserções. Por exemplo, considere uma tabela simplificada de funcionários que contém as seguintes colunas: EmployeeID, FirstName, LastName, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

Neste exemplo, você pode usar o fato de que o SocialSecurityNumber é exclusivo para a execução de uma instrução MERGE de vários funcionários. Primeiro, crie o tipo de tabela definido pelo usuário:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Em seguida, crie um procedimento armazenado ou escreva um código que use a instrução MERGE para executar a atualização e a inserção. O exemplo a seguir usa a instrução MERGE em um parâmetro com valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo da tabela @employees não é mostrado aqui.

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

Para saber mais, consulte a documentação e exemplos da instrução MERGE. Embora o mesmo trabalho possa ser executado em uma chamada de procedimento armazenado com várias etapas, com operações INSERT e UPDATE separadas, a instrução MERGE é mais eficiente. O código do banco de dados também pode construir chamadas Transact-SQL que usam a instrução MERGE diretamente sem exigir duas chamadas de banco de dados para INSERT e UPDATE.

## <a name="recommendation-summary"></a>Resumo de recomendações
A lista a seguir fornece um resumo das recomendações de envio em lote discutidas neste tópico:

* Use o armazenamento em buffer e o envio em lote para aumentar o desempenho e a escalabilidade de aplicativos do Banco de Dados SQL.
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
  * Teste o maior lote para verificar se o Banco de Dados SQL não irá rejeitá-lo.
  * Crie definições de configuração que controlam o envio em lote, como o tamanho do lote ou o período de armazenamento em buffer. Essas configurações fornecem flexibilidade. Você pode alterar o comportamento do lote em produção sem reimplantar o serviço de nuvem.
* Evite a execução paralela de lotes que operam em uma única tabela em um banco de dados. Se você optar por dividir um único lote entre vários threads de trabalho, execute testes para determinar o número ideal de threads. Após um limite não especificado, uma quantidade maior de threads diminuirá o desempenho em vez de aumentá-lo.
* Considere o armazenamento em buffer de acordo com o tamanho e o tempo como uma maneira de implementar o envio em lote para mais cenários.

## <a name="next-steps"></a>Próximas etapas
Este artigo se concentrou em como o design do banco de dados e as técnicas de codificação relacionadas ao envio em lote podem melhorar o desempenho e a escalabilidade do aplicativo. Mas isso é apenas um fator em sua estratégia geral. Para conhecer outras maneiras de melhorar o desempenho e a escalabilidade, consulte [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados individuais](sql-database-performance-guidance.md) e [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md).

