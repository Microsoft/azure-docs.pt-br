---
title: 'Tutorial: carregar dados de dos táxis de Nova York'
description: O tutorial usa portal do Azure e SQL Server Management Studio para carregar dados de dos táxis de Nova York de um blob do Azure para Synapse SQL.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1490a0e094c6ce2665e28f7d32540ad58d53cb2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600132"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Tutorial: carregar o conjunto de dos táxis de Nova York

Este tutorial usa a [instrução Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar o conjunto de dos táxis New York de uma conta de armazenamento de BLOBs do Azure. Este tutorial usa o [Portal do Azure](https://portal.azure.com) e o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio) para:

> [!div class="checklist"]
>
> * Criar um usuário designado para carregar dados
> * Criar as tabelas para o conjunto de exemplos 
> * Use a instrução COPY T-SQL para carregar dados em seu data warehouse
> * Exibir o andamento dos dados enquanto estão sendo carregados

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este tutorial, baixe e instale a versão mais recente do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio).  

Este tutorial pressupõe que você já criou um pool SQL dedicado do [tutorial](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin)a seguir.

## <a name="create-a-user-for-loading-data"></a>Criar um usuário para carregar dados

A conta do administrador do servidor é destinada a executar operações de gerenciamento, e não é adequada para executar consultas nos dados do usuário. O carregamento de dados é uma operação com uso intensivo de memória. Os máximos de memória são definidos de acordo com as [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) e a [classe de recurso](resource-classes-for-workload-management.md) configurada.

É melhor criar um logon e um usuário dedicados para carregar dados. Em seguida, adicione o usuário carregado a uma [classe de recurso](resource-classes-for-workload-management.md) que permita uma alocação máxima de memória adequada.

Conecte-se como o administrador do servidor para que você possa criar logons e usuários. Use estas etapas para criar um logon e um usuário chamado **LoaderRC20**. Em seguida, atribua o usuário à classe de recurso **staticrc20**.

1. No SSMS, selecione **mestre** ao lado do botão direito para mostrar um menu suspenso e escolha **nova consulta**. Uma janela de nova consulta é aberta.

    ![Nova consulta no mestre](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Na janela de consulta, insira esses comandos T-SQL para criar um logon e um usuário chamado LoaderRC20, substituindo sua própria senha para 'a123STRONGpassword!'.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selecione **Executar**.

4. Clique com o botão direito do mouse em **mySampleDataWarehouse** e escolha **Nova consulta**. Uma nova janela de consulta é aberta.  

    ![Nova consulta no data warehouse de exemplo](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Insira os comandos T-SQL a seguir para criar um usuário de banco de dados chamado LoaderRC20 para o logon LoaderRC20. A segunda linha concede ao novo usuário permissões de CONTROLE sobre o novo data warehouse.  Essas permissões são semelhantes para tornar o usuário o proprietário do banco de dados. A terceira linha adiciona o novo usuário como um membro da [classe de recurso](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selecione **Executar**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Conectar-se ao servidor como o usuário carregado

A primeira etapa para carregar os dados é fazer logon como LoaderRC20.  

1. No Pesquisador de objetos, selecione o menu suspenso **conectar** e selecione **mecanismo de banco de dados**. A caixa de diálogo **Conectar-se ao Servidor** é exibida.

    ![Conectar-se com o novo logon](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Insira o nome do servidor totalmente qualificado, e vez insira **LoaderRC20** como o Logon.  Insira sua senha para LoaderRC20.

3. Selecione **Conectar**.

4. Quando sua conexão estiver pronta, você verá duas conexões de servidor no Pesquisador de Objetos. Uma conexão como ServerAdmin e outra como MedRCLogin.

    ![A conexão é bem-sucedida](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Criar tabelas para os dados de exemplo

Você está pronto para iniciar o processo de carregamento de dados em seu novo data warehouse. Esta parte do tutorial mostra como usar a instrução de cópia para carregar o conjunto de uma Nova York City táxi de um blob de armazenamento do Azure. Para referência futura, para saber como obter seus dados para o armazenamento de BLOBs do Azure ou carregá-los diretamente da sua fonte, consulte a [visão geral de carregamento](design-elt-data-loading.md).

Execute os seguintes scripts SQL e especifique as informações sobre os dados que você deseja carregar. Essas informações incluem o local em que os dados estão localizados, o formato do conteúdo dos dados e a definição da tabela para os dados.

1. Na seção anterior, você fez logon em seu data warehouse como LoaderRC20. No SSMS, clique com o botão direito do mouse em sua conexão LoaderRC20 e selecione **Nova consulta**.  Uma nova janela de consulta é exibida.

    ![Nova janela de consulta de carregamento](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Compare a janela de consulta com a imagem anterior.  Verifique se a nova janela de consulta está em execução como LoaderRC20 e está executando consultas no banco de dados MySampleDataWarehouse. Use essa janela de consulta para executar todas as etapas de carregamento.

7. Execute as seguintes instruções T-SQL para criar as tabelas:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Carregar os dados em seu data warehouse

Esta seção usa a [instrução de cópia para carregar](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) os dados de exemplo de Azure Storage BLOB.  

> [!NOTE]
> Este tutorial carrega os dados diretamente na tabela final. Normalmente, você carregaria em uma tabela de preparo para suas cargas de trabalho de produção. Enquanto os dados estão na tabela de preparo, você pode executar todas as transformações necessárias. 

1. Execute as seguintes instruções para carregar os dados:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Exiba os dados enquanto eles são carregados. Você está carregando vários GBs de dados e compactando-os em índices columnstore clusterizados de alto desempenho. Execute a consulta a seguir que usa DMVs (exibições de gerenciamento dinâmico) para mostrar o status do carregamento.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Exiba todas as consultas do sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Veja os dados carregados sem problemas no seu data warehouse.

    ![Exibir tabelas carregadas](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você está sendo cobrado por recursos de computação e por dados que você carregou em seu data warehouse. Eles são cobrados separadamente.

* Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o data warehouse. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados e poderá retomar a computação quando estiver pronto para trabalhar com os dados.
* Se desejar remover encargos futuros, será possível excluir o data warehouse.

Siga estas etapas para limpar os recursos conforme desejado.

1. Faça logon no [portal do Azure](https://portal.azure.com), selecione seu data warehouse.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o data warehouse para que você não seja cobrado pela computação ou pelo armazenamento, selecione **excluir**.

4. Para remover o servidor que você criou, selecione **mynewserver-20180430.Database.Windows.net** na imagem anterior e, em seguida, selecione **excluir**.  Tenha cuidado com isso, uma vez que a exclusão do servidor excluirá todos os bancos de dados atribuídos a ele.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um data warehouse e um usuário para carregar dados. Você usou a [instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples) simples para carregar dados em seu data warehouse.

Você fez essas coisas:
> [!div class="checklist"]
>
> * Criou um data warehouse no Portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conectou-se ao data warehouse com o SSMS
> * Criou um usuário designado para carregar dados
> * Criou as tabelas para os dados de exemplo
> * Usou a instrução COPY T-SQL para carregar dados em seu data warehouse
> * Exibiu o andamento dos dados enquanto eles estão sendo carregados

Avance para a visão geral de desenvolvimento para saber como migrar um banco de dados existente para o Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Decisões de design para migrar um banco de dados existente para o Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Para obter mais exemplos de carregamento e referências, veja a seguinte documentação:

- [Documentação de referência de instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [COPIAR exemplos para cada método de autenticação](./quickstart-bulk-load-copy-tsql-examples.md)
- [COPIAR guia de início rápido para uma única tabela](./quickstart-bulk-load-copy-tsql.md)
