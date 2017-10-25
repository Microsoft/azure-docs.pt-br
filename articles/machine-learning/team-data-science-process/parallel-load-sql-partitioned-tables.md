---
title: "Compilar e otimizar tabelas para importação paralela rápida de dados para um SQL Server em uma VM do Azure | Microsoft Docs"
description: "Importação de Dados em Massa Paralela Usando Tabelas de Partição do SQL"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 899f20b3642612386f2513c9c8649cd845be826e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Importação de Dados em Massa Paralela Usando Tabelas de Partição do SQL
Este documento descreve como compilar tabelas particionadas para rápida importação de dados em massa paralela para um banco de dados do SQL Server. Para carregamento/transferência de Big Data para um banco de dados SQL, a importação de dados para o banco de dados SQL e consultas posteriores podem ser melhoradas usando *Exibições e Tabelas Particionadas*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Criar um novo banco de dados e um conjunto de grupos de arquivos
* [Crie um novo banco de dados](https://technet.microsoft.com/library/ms176061.aspx), se ainda não houver um.
* Adicione grupos de arquivos de banco de dados ao banco de dados que contém os arquivos físicos particionados. Isso poderá ser feito com [CRIAR BANCO DE DADOS](https://technet.microsoft.com/library/ms176061.aspx), se for novo, ou [ALTERAR BANCO DE DADOS](https://msdn.microsoft.com/library/bb522682.aspx), se o banco de dados já existir.
* Adicione um ou mais arquivos (conforme necessário) para cada grupo de arquivos de banco de dados.
  
  > [!NOTE]
  > Especifique o grupo de arquivos de destino que contém os dados dessa partição e os nomes dos arquivos de banco de dados físico em que serão armazenados os dados do grupo de arquivos.
  > 
  > 

O exemplo a seguir cria um novo banco de dados com três grupos de arquivos que não são os grupos principal e de registro, contendo um arquivo físico cada um. Os arquivos de banco de dados são criados na pasta de Dados do SQL Server padrão, conforme configurado na instância do SQL Server. Para obter mais informações sobre os locais de arquivo padrão, consulte [Locais de arquivo para instâncias padrão e nomeadas do SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Criar uma tabela particionada
Crie tabelas particionadas de acordo com o esquema de dados mapeado para os grupos de arquivos de banco de dados criados na etapa anterior. Quando dados são importados em massa para a tabela particionada, os registros serão distribuídos entre os grupos de arquivos de acordo com um esquema de partição, conforme descrito abaixo.

**Para criar uma tabela de partição, você precisa:**

* [Criar uma função de partição](https://msdn.microsoft.com/library/ms187802.aspx), que define o intervalo de valores/limites a serem incluídos em cada tabela de partição individual, por exemplo, para limitar as partições por month(some\_datetime\_field) no ano de 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )
* [Criar um esquema de partição](https://msdn.microsoft.com/library/ms179854.aspx) , que mapeia cada intervalo de partição na função de partição para um grupo de arquivos físicos, por exemplo:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Para verificar se os intervalos em vigor em cada partição de acordo com a função/esquema, execute a seguinte consulta:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>
* [Crie tabelas particionadas](https://msdn.microsoft.com/library/ms174979.aspx)de acordo com seu esquema de dados e especifique o campo de esquema e de restrição usados para particionar a tabela, por exemplo:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Para obter mais informações, consulte [Criar tabelas e índices particionados](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Importe os dados em massa para cada tabela de partição individual
* Você pode usar o BCP, BULK INSERT ou outros métodos como o [Assistente de Migração do SQL Server](http://sqlazuremw.codeplex.com/). O exemplo fornecido usa o método BCP.
* [Altere o banco de dados](https://msdn.microsoft.com/library/bb522682.aspx) para alterar o esquema de registro em log de transações como BULK_LOGGED para minimizar a sobrecarga de registros, por exemplo:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Para acelerar o carregamento de dados, inicie as operações de importação em massa paralela. Para obter dicas sobre como acelerar a importação em massa de big data em bancos de dados do SQL Server, consulte [Carregar 1 TB em menos de 1 hora](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

O script do PowerShell a seguir é um exemplo de carregamento de dados paralela que usa o BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Crie índices para otimizar o desempenho de associações e consultas
* Se você pretende extrair dados de modelagem de várias tabelas, crie índices nas chaves de associação para melhorar o desempenho da junção.
* [Crie índices](https://technet.microsoft.com/library/ms188783.aspx) (em cluster ou não clusterizados) direcionando o mesmo grupo de arquivos para cada partição, por exemplo:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  ou
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Você pode optar por criar os índices antes de importar os dados em massa. Criar índices antes da importação em massa retardará o carregamento de dados.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Exemplo de Processo e Tecnologia de Análise Avançada em ação
Para obter um exemplo passo a passo completo do Processo de Análise do Cortana com um conjunto de dados público, consulte [Processo de Análise do Cortana em ação: usando o SQL Server](sql-walkthrough.md).

