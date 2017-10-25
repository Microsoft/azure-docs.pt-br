---
title: "Mover dados para o SQL Server em uma máquina virtual do Azure | Microsoft Docs"
description: Mover dados de arquivos simples ou de um SQL Server local para o SQL Server em uma VM do Azure.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: bd9289ea528c3a80f63380daf52161d2477a6771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server em uma máquina virtual do Azure
Este tópico descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de um SQL Server local para o SQL Server em uma máquina virtual do Azure. Essas tarefas para movimentar dados para a nuvem fazem parte do Processo de Ciência de Dados de Equipe.

Para um tópico que descreve as opções para movimentação de dados para um Banco de Dados SQL do Azure para Machine Learning, consulte [Mover dados para um banco de dados SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

O **menu** abaixo vincula-se a tópicos que descrevem a inclusão de dados em outros ambientes de destino em que os dados podem ser armazenados e processados durante o TDSP (Processo de Ciência de Dados de Equipe).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A tabela a seguir resume as opções para mover dados para o SQL Server em uma máquina virtual do Azure.

| <b>FONTE</b> | <b>DESTINO: SQL Server na VM do Azure</b> |
| --- | --- |
| <b>Arquivo simples</b> |1. <a href="#insert-tables-bcp">Utilitário de BCP (cópia em massa de linha de comando)</a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção em massa </a><br> 3. <a href="#sql-builtin-utilities">Utilitários gráficos internos no SQL Server</a> |
| <b>SQL Server local</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportar para um arquivo simples </a><br> 3. <a href="#sql-migration">Assistente de Migração de Banco de Dados SQL</a> <br> 4. <a href="#sql-backup">Backup e restauração de banco de dados</a><br> |

Observe que este documento pressupõe que os comandos SQL sejam executados no SQL Server Management Studio ou no Gerenciador de Banco de Dados do Visual Studio.

> [!TIP]
> Como alternativa, você pode usar o [Azure  Factory](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que move dados para uma VM do SQL Server no Azure. Para obter mais informações, consulte [Copiar dados com o Azure Data Factory (Atividade de Cópia)](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial presume que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) . Depois de criar a conta de armazenamento, você precisará obter a chave de conta usada para acessar o armazenamento. Confira [Gerenciar as chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* **SQL Server em uma VM do Azure**provisionado. Para obter instruções, consulte [Configurar uma máquina virtual do SQL Server do Azure como um servidor do IPython Notebook para análises avançadas](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Movimentação de dados de uma fonte de arquivo simples para o SQL Server em uma VM do Azure
Se os dados estiverem em um arquivo simples (organizado em um formato de linha/coluna), ele pode ser movido para a VM do SQL Server no Azure pelos métodos a seguir:

1. [Utilitário de BCP (cópia em massa de linha de comando)](#insert-tables-bcp)
2. [Consulta SQL de inserção em massa ](#insert-tables-bulkquery)
3. [Utilitários gráficos internos no SQL Server (Importar/Exportar, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Utilitário de BCP (cópia em massa de linha de comando)
O BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das maneiras mais rápidas de mover dados. Ele funciona em todas as três variantes do SQL Server (SQL Server local, SQL Azure e VM do SQL Server no Azure).

> [!NOTE]
> **Onde os dados devem estar para o BCP?**  
> Embora não seja necessário, ter arquivos que contêm dados de origem localizados no mesmo computador que o SQL Server de destino possibilita transferências mais rápidas (velocidade de rede em comparação com velocidade de E/S do disco local). Você pode mover os arquivos simples que contêm dados para máquina em que o SQL Server está instalado usando várias ferramentas de cópia de arquivo, como [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](http://storageexplorer.com/) ou copiar/colar do Windows via protocolo RDP.
>
>

1. Certifique-se de que o banco de dados e as tabelas foram criados no banco de dados do SQL Server de destino. Aqui está um exemplo de como fazer isso usando os comandos `Create Database` e `Create Table`:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Gere o arquivo de formato que descreve o esquema da tabela emitindo o comando a seguir na linha de comando do computador em que o BCP está instalado.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Insira os dados no banco de dados usando o comando bcp da seguinte maneira. Isso deve funcionar na linha de comando, supondo que o SQL Server esteja instalado no mesmo computador:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Otimizando inserções de BCP** Consulte o artigo a seguir, ['Diretrizes para otimizar a importação de massa'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) , para otimizar essas inserções.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Paralelização de inserções para movimentação de dados mais rápida
Se os dados que você está movendo forem grandes, você pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo em um Script do PowerShell.

> [!NOTE]
> **Ingestão de big data** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, particione suas tabelas de banco de dados lógicas e físicas usando várias tabelas de partição e grupos de arquivos. Para obter mais informações sobre como criar e carregar dados em tabelas de partição, consulte [Tabelas de partição do SQL de carga paralela](parallel-load-sql-partitioned-tables.md).
>
>

O script do PowerShell de exemplo abaixo demonstra inserções paralelas usando BCP:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
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


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa
A [Consulta SQL de inserção em massa](https://msdn.microsoft.com/library/ms188365) pode ser usada para importar dados para o banco de dados de arquivos com base em linha/coluna - os tipos com suporte são abordados no tópico [Preparar dados para exportação ou importação em massa (SQL Server)](https://msdn.microsoft.com/library/ms188609).

Aqui estão alguns comandos de exemplo para inserção em massa:  

1. Analise seus dados e defina as opções personalizadas antes da importação para certificar-se de que o banco de dados do SQL Server entende o mesmo formato para campos especiais, tal como datas. Aqui está um exemplo de como definir o formato de data como ano-mês-dia (se seus dados contiverem a data no formato de ano-mês-dia):

        SET DATEFORMAT ymd;    
2. Importe dados usando as instruções de importação em massa:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Utilitários internos no SQL Server
Você pode usar o SSIS (Serviços de Integrações do SQL Server) para importar dados para a VM do SQL Server no Azure por meio de um arquivo simples.
O SSIS está disponível em dois ambientes de estúdio. Para obter detalhes, consulte [Ambientes do Integration Services (SSIS) e Estúdio](https://technet.microsoft.com/library/ms140028.aspx):

* Para obter detalhes sobre SQL Server Data Tools, consulte [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Para obter detalhes sobre o Assistente de Importação/Exportação, consulte [Assistente de Importação/Exportação do SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Movimentação de dados do servidor SQL local para o SQL Server em uma VM do Azure
Você também pode usar as seguintes estratégias de migração:

1. [Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para arquivo simples](#export-flat-file)
3. [Assistente de Migração de Banco de Dados SQL](#sql-migration)
4. [Backup e restauração de banco de dados](#sql-backup)

Descrevemos cada um deles abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure
O **Assistente para implantar um banco de dados do SQL Server em uma Máquina Virtual do Microsoft Azure** é uma maneira simples e recomendada de mover dados de uma instância do SQL Server local para o SQL Server em uma Máquina Virtual do Azure. Para obter etapas detalhadas, bem como uma discussão sobre outras alternativas, confira [Migrar um banco de dados para o SQL Server em uma Máquina Virtual do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar para arquivo simples
Vários métodos podem ser usados para exportar dados em massa de um SQL Server local, conforme documentado no tópico [Importação e importação de dados em massa (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Este documento abordará o BCP (Programa de Cópia em Massa) como um exemplo. Depois que os dados são exportados para um arquivo simples, ele pode ser importado para outro SQL Server usando a importação em massa.

1. Exporte os dados do SQL Server local para um arquivo usando o utilitário bcp da seguinte maneira

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Crie o banco de dados e a tabela na VM do SQL Server no Azure usando `create database` e `create table` para o esquema de tabela exportado na etapa 1.
3. Crie um arquivo de formato para descrever o esquema da tabela de dados que está sendo importado/exportado. Os detalhes do formato de arquivo são descritos em [Criar um arquivo de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Geração de arquivo de formato ao executar o BCP no computador do SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Geração de arquivo de formato ao executar o BCP remotamente em um SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Use qualquer um dos métodos descritos na seção [Movendo dados da origem do arquivo](#filesource_to_sqlonazurevm) para mover os dados em arquivos simples para o SQL Server.

### <a name="sql-migration"></a>Assistente de Migração de Banco de Dados SQL
[Assistente de Migração de Banco de Dados do SQL Server](http://sqlazuremw.codeplex.com/) fornece uma maneira amigável de mover dados entre duas instâncias do SQL Server. Ele permite que o usuário mapeie o esquema de dados entre as tabelas de origem e destino, escolha os tipos de coluna e vários outros recursos. Ele usa BCP (cópia em massa) nos bastidores. Abaixo está uma captura de tela da tela de boas-vindas para o Assistente de Migração de Banco de Dados SQL.  

![Assistente de Migração do SQL Server][2]

### <a name="sql-backup"></a>Backup e restauração de banco de dados
O SQL Server dá suporte a:

1. [Funcionalidade de backup e restauração de banco de dados](https://msdn.microsoft.com/library/ms187048.aspx) (para um arquivo local ou exportação de bacpac para blob) e [Aplicativos de Camada de Dados](https://msdn.microsoft.com/library/ee210546.aspx) (usando bacpac).
2. Capacidade de criar VMs do SQL Server diretamente no Azure com um banco de dados copiado ou copiar um banco de dados do SQL do Azure existente. Para obter mais detalhes, consulte [Usar o Assistente para Cópia de Banco de Dados](https://msdn.microsoft.com/library/ms188664.aspx).

Abaixo está uma captura de tela das opções de backup/restauração de banco de dados do SQL Server Management Studio.

![Ferramenta de Importação do SQL Server][1]

## <a name="resources"></a>Recursos
[Migrar um banco de dados para o SQL Server em uma VM do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Visão geral do SQL Server em máquinas virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
