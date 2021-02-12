---
title: Fazer backup e restaurar bancos de dados-Azure SQL Edge
description: Saiba mais sobre os recursos de backup e restauração no Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395233"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Fazer backup e restaurar bancos de dados no Azure SQL Edge 

O Azure SQL Edge foi criado com base nas versões mais recentes do Microsoft SQL Mecanismo de Banco de Dados. Ele fornece recursos de banco de dados de backup e restauração semelhantes aos disponíveis em SQL Server em Linux e SQL Server em execução em contêineres. O componente de backup e restauração fornece uma proteção essencial para proteger os dados armazenados nos bancos de dado do Azure SQL Edge. 

Para minimizar o risco de perda de dados catastrófica, você deve fazer backup de seus bancos de dados periodicamente para preservar as modificações em seus dados regularmente. Uma estratégia de backup e restauração bem planejada ajuda a proteger os bancos de dados contra perda de dados causada por uma variedade de falhas. Teste sua estratégia restaurando um conjunto de backups e, em seguida, recuperando seu banco de dados para prepará-lo a responder com eficiência a um desastre.

Para saber mais sobre por que os backups são importantes, consulte [backup e restauração de bancos de dados SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

O Azure SQL Edge permite que você faça backup e restauração de armazenamento local e BLOBs do Azure. Para obter mais informações, consulte [SQL Server Backup e restauração com o armazenamento de BLOBs do Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e [SQL Server Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Fazer backup de um banco de dados no Azure SQL Edge

O Azure SQL Edge dá suporte aos mesmos tipos de backup que SQL Server. Para obter uma lista completa, consulte [visão geral do backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Os bancos de dados criados no Azure SQL Edge usam o modelo de recuperação simples por padrão. Dessa forma, não é possível executar backups de log nesses bancos de dados. Se precisar fazer isso, você precisará de um administrador para alterar o modelo de recuperação de banco de dados para o modelo de recuperação completa. Para obter uma lista completa dos modelos de recuperação com suporte pelo SQL Server, consulte [visão geral do modelo de recuperação](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Fazer backup no disco local

No exemplo a seguir, você usa o `BACKUP DATABASE` comando Transact-SQL para criar um backup de banco de dados no contêiner. Para fins deste exemplo, você cria uma nova pasta chamada *backup* para armazenar os arquivos de backup.

1. Crie uma pasta para os backups. Execute este comando no host onde o contêiner do Azure SQL Edge está em execução. No comando a seguir, substitua **<AzureSQLEdge_Container_Name>** pelo nome do contêiner do Azure SQL Edge em sua implantação.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Conecte-se à instância do Azure SQL Edge usando SQL Server Management Studio (SSMS) ou usando Azure Data Studio. Execute o `BACKUP DATABASE` comando para fazer o backup do seu banco de dados de usuário. No exemplo a seguir, você está fazendo o backup do banco de dados *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Depois de executar o comando, se o backup do banco de dados for bem-sucedido, você verá mensagens semelhantes às seguintes na seção de resultados do SSMS ou do Azure Data Studio.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Fazer backup na URL

O SQL do Azure no Edge dá suporte a backups para blobs de página e blobs de blocos. Para obter mais informações, consulte [back up to Block blob vs Page blob](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). No exemplo a seguir, o backup do banco de dados *IronOreSilicaPrediction* está sendo feito em um blob de blocos. 

1. Para configurar backups para bloquear BLOBs, primeiro gere um token SAS (assinatura de acesso compartilhado) que você possa usar para criar uma credencial de SQL Server no Azure SQL Edge. O script cria uma SAS associada a uma política de acesso armazenada. Para obter mais informações, consulte [assinaturas de acesso compartilhado, parte 1: Noções básicas sobre o modelo SAS](../storage/common/storage-sas-overview.md). O script também grava o comando T-SQL necessário para criar a credencial no SQL Server. O script a seguir pressupõe que você já tenha uma assinatura do Azure com uma conta de armazenamento e um contêiner de armazenamento para os backups.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Depois de executar o script com êxito, copie o `CREATE CREDENTIAL` comando para uma ferramenta de consulta. Em seguida, conecte-se a uma instância do SQL Server e execute o comando para criar a credencial com a SAS.

2. Conecte-se à instância do Azure SQL Edge usando o SSMS ou Azure Data Studio e crie a credencial usando o comando da etapa anterior. Certifique-se de substituir o `CREATE CREDENTIAL` comando pela saída real da etapa anterior.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. O comando a seguir faz um backup do *IronOreSilicaPrediction* para o contêiner de armazenamento do Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Restaurar um banco de dados no Azure SQL Edge

No Azure SQL Edge, você pode restaurar de um disco local, um local de rede ou uma conta de armazenamento de BLOBs do Azure. Para obter mais informações sobre restauração e recuperação no SQL Server, consulte [visão geral de restauração e recuperação](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). Para obter uma visão geral do modelo de recuperação simples no SQL Server, consulte [restaurações completas de banco de dados (modelo de recuperação simples)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Os bancos de dados criados no Azure SQL Edge não podem ser restaurados em uma instância de Microsoft SQL Server ou SQL do Azure. Além disso, um banco de dados criado no Microsoft SQL Server ou no Azure SQL pode ser restaurado no Azure SQL Edge, desde que o banco de dados não contenha nenhum dos recursos não suportados pelo Azure SQL Edge. 

### <a name="restore-from-a-local-disk"></a>Restaurar de um disco local

Este exemplo usa o backup *IronOreSilicaPrediction* que você fez no exemplo anterior. Agora, você irá restaurá-lo como um novo banco de dados com um nome diferente.

1. Se o arquivo de backup do banco de dados ainda não estiver presente no contêiner, você poderá usar o comando a seguir para copiar o arquivo para o contêiner. O exemplo a seguir pressupõe que o arquivo de backup está presente no host local e está sendo copiado para a pasta/var/opt/MSSQL/backup em um contêiner do Azure SQL Edge chamado *sql1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Conecte-se à instância do Azure SQL Edge usando o SSMS ou Azure Data Studio para executar o comando Restore. No exemplo a seguir, **IronOrePredictDB. bak** é restaurado para criar um novo banco de dados, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Depois de executar o comando Restore, se a operação de restauração tiver sido bem-sucedida, você verá mensagens semelhantes às seguintes na janela saída. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Restauração a partir da URL

O SQL do Azure no Edge também dá suporte à restauração de um banco de dados de uma conta de armazenamento do Azure. Você pode restaurar a partir dos BLOBs de blocos ou backups de blob de páginas. No exemplo a seguir, o arquivo de backup do banco de dados *IronOreSilicaPrediction_2020_04_16. bak* em um blob de blocos é restaurado para criar o banco de dados, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```