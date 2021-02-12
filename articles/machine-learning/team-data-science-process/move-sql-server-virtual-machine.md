---
title: Mover dados para uma máquina virtual do SQL Server - Processo de Ciência de Dados da Equipe
description: Mover dados de arquivos simples ou de SQL Server locais para SQL Server na VM do Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320330"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server em uma máquina virtual do Azure

Este artigo descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de um SQL Server local para o SQL Server em uma máquina virtual do Azure. Essas tarefas para movimentar dados para a nuvem fazem parte do Processo de Ciência de Dados de Equipe.

Para um tópico que descreve as opções para movimentação de dados para um Banco de Dados SQL do Azure para Machine Learning, consulte [Mover dados para um banco de dados SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

A tabela a seguir resume as opções para mover dados para o SQL Server em uma máquina virtual do Azure.

| <b>ORIGINAL</b> | <b>DESTINO: SQL Server na VM do Azure</b> |
| --- | --- |
| <b>Arquivo simples</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia em massa de linha de comando (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção em massa </a><br> 3. <a href="#sql-builtin-utilities">utilitários internos gráficos no SQL Server</a> |
| <b>SQL Server local</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">implantar um banco de dados SQL Server em um assistente de VM Microsoft Azure</a><br> 2. <a href="#export-flat-file">exportar para um arquivo simples </a><br> 3. <a href="#sql-migration">Assistente de Migração de Banco de Dados SQL </a> <br> 4. <a href="#sql-backup">Backup e restauração de banco de dados </a><br> |

Este documento pressupõe que comandos SQL sejam executados a partir de SQL Server Management Studio ou do Visual Studio Gerenciador de Banco de Dados.

> [!TIP]
> Como alternativa, você pode usar o [Azure  Factory](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que move dados para uma VM do SQL Server no Azure. Para obter mais informações, consulte [Copiar dados com o Azure Data Factory (Atividade de Cópia)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Este tutorial presume que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md) . Depois de criar a conta de armazenamento, você precisará obter a chave de conta usada para acessar o armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* **SQL Server em uma VM do Azure** provisionado. Para obter instruções, consulte [Configurar uma máquina virtual do SQL Server do Azure como um servidor do IPython Notebook para análises avançadas](../data-science-virtual-machine/overview.md).
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Movimentação de dados de uma fonte de arquivo simples para o SQL Server em uma VM do Azure
Se os dados estiverem em um arquivo simples (organizado em um formato de linha/coluna), ele pode ser movido para a VM do SQL Server no Azure pelos métodos a seguir:

1. [Utilitário de cópia em massa de linha de comando (BCP)](#insert-tables-bcp)
2. [Consulta SQL de inserção em massa](#insert-tables-bulkquery)
3. [Utilitários gráficos internos no SQL Server (Importar/Exportar, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Utilitário de BCP (cópia em massa de linha de comando)
O BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das maneiras mais rápidas de mover dados. Ele funciona em todas as três SQL Server variantes (SQL Server local, SQL Azure e VM SQL Server no Azure).

> [!NOTE]
> **Onde os dados devem estar para o BCP?**  
> Embora não seja necessário, ter arquivos que contêm dados de origem localizados no mesmo computador que o SQL Server de destino possibilita transferências mais rápidas (velocidade de rede em comparação com velocidade de E/S do disco local). Você pode mover os arquivos simples que contêm dados para máquina em que o SQL Server está instalado usando várias ferramentas de cópia de arquivo, como [AZCopy](../../storage/common/storage-use-azcopy-v10.md), [Azure Storage Explorer](https://storageexplorer.com/) ou copiar/colar do Windows via protocolo RDP.
>
>

1. Certifique-se de que o banco de dados e as tabelas foram criados no banco de dados do SQL Server de destino. Aqui está um exemplo de como fazer isso usando os comandos `Create Database` e `Create Table`:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Gere o arquivo de formato que descreve o esquema da tabela emitindo o seguinte comando na linha de comando do computador em que o bcp está instalado.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Insira os dados no banco de dado usando o comando bcp, que deve funcionar na linha de comando quando SQL Server estiver instalado no mesmo computador:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Otimizando inserções de BCP** Consulte o artigo a seguir, ['Diretrizes para otimizar a importação de massa'](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) , para otimizar essas inserções.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Paralelização de inserções para movimentação de dados mais rápida
Se os dados que você está movendo forem grandes, você poderá acelerar as coisas executando simultaneamente vários comandos BCP em paralelo em um script do PowerShell.

> [!NOTE]
> **Ingestão de Big Data** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, particione suas tabelas de banco de dados lógicas e físicas usando várias tabelas de partição e grupos de arquivos. Para obter mais informações sobre como criar e carregar dados em tabelas de partição, consulte [Tabelas de partição do SQL de carga paralela](parallel-load-sql-partitioned-tables.md).
>
>

O script do PowerShell de exemplo a seguir demonstra inserções paralelas usando BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa
A [Consulta SQL de inserção em massa](/sql/t-sql/statements/bulk-insert-transact-sql) pode ser usada para importar dados para o banco de dados de arquivos com base em linha/coluna - os tipos com suporte são abordados no tópico [Preparar dados para exportação ou importação em massa (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server).

Aqui estão alguns comandos de exemplo para inserção em massa:  

1. Analise seus dados e defina as opções personalizadas antes da importação para certificar-se de que o banco de dados do SQL Server entende o mesmo formato para campos especiais, tal como datas. Aqui está um exemplo de como definir o formato de data como ano-mês-dia (se seus dados contiverem a data no formato de ano-mês-dia):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importe dados usando as instruções de importação em massa:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Utilitários internos no SQL Server
Você pode usar SQL Server Integration Services (SSIS) para importar dados para SQL Server VM no Azure de um arquivo simples.
O SSIS está disponível em dois ambientes de estúdio. Para obter detalhes, consulte [Ambientes do Integration Services (SSIS) e Estúdio](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Para obter detalhes sobre SQL Server Data Tools, consulte [Microsoft SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Para obter detalhes sobre o Assistente de Importação/Exportação, consulte [Assistente de Importação/Exportação do SQL Server](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Movimentação de dados do servidor SQL local para o SQL Server em uma VM do Azure
Você também pode usar as seguintes estratégias de migração:

1. [Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para arquivo simples](#export-flat-file)
3. [Assistente de migração do banco de dados SQL](#sql-migration)
4. [Backup e restauração de banco de dados](#sql-backup)

Descrevemos cada uma dessas opções abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure
O **Assistente para implantar um banco de dados do SQL Server em uma Máquina Virtual do Microsoft Azure** é uma maneira simples e recomendada de mover dados de uma instância do SQL Server local para o SQL Server em uma Máquina Virtual do Azure. Para obter etapas detalhadas, bem como uma discussão sobre outras alternativas, confira [Migrar um banco de dados para o SQL Server em uma Máquina Virtual do Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportar para Arquivo simples
Vários métodos podem ser usados para exportar dados em massa de um SQL Server local, conforme documentado no tópico [Importação e importação de dados em massa (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) . Este documento abordará o BCP (Programa de Cópia em Massa) como um exemplo. Depois que os dados são exportados para um arquivo simples, ele pode ser importado para outro SQL Server usando a importação em massa.

1. Exporte os dados do SQL Server local para um arquivo usando o utilitário bcp da seguinte maneira

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Crie o banco de dados e a tabela na VM do SQL Server no Azure usando `create database` e `create table` para o esquema de tabela exportado na etapa 1.
3. Crie um arquivo de formato para descrever o esquema da tabela de dados que está sendo importado/exportado. Os detalhes do formato de arquivo são descritos em [Criar um arquivo de formato (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Geração de arquivo de formato ao executar o BCP no computador SQL Server

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Geração de arquivo de formato ao executar o BCP remotamente em um SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Use qualquer um dos métodos descritos na seção [Movendo dados da origem do arquivo](#filesource_to_sqlonazurevm) para mover os dados em arquivos simples para o SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Assistente de migração de Banco de Dados SQL
[Assistente de Migração de Banco de Dados do SQL Server](https://sqlazuremw.codeplex.com/) fornece uma maneira amigável de mover dados entre duas instâncias do SQL Server. Ele permite que o usuário mapeie o esquema de dados entre as tabelas de origem e destino, escolha os tipos de coluna e vários outros recursos. Ele usa BCP (cópia em massa) nos bastidores. Abaixo está uma captura de tela da tela de boas-vindas para o Assistente de Migração de Banco de Dados SQL.  

![Assistente de Migração do SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Backup e restauração do banco de dados
O SQL Server dá suporte a:

1. [Funcionalidade de backup e restauração de banco de dados](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (para um arquivo local ou exportação de bacpac para blob) e [Aplicativos de Camada de Dados](/sql/relational-databases/data-tier-applications/data-tier-applications) (usando bacpac).
2. Capacidade de criar SQL Server VMs diretamente no Azure com um banco de dados copiado ou copiar para um banco de dados existente no banco de dados SQL. Para obter mais informações, consulte [Use the Copy Database Wizard](/sql/relational-databases/databases/use-the-copy-database-wizard).

Abaixo está uma captura de tela das opções de backup/restauração de banco de dados do SQL Server Management Studio.

![Ferramenta de Importação do SQL Server][1]

## <a name="resources"></a>Recursos
[Migrar um banco de dados para o SQL Server em uma VM do Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Visão geral do SQL Server nas Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png