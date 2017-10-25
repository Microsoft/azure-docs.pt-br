---
title: Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure | Microsoft Docs
description: Crie um novo banco de dados SQL do Azure importando um arquivo BACPAC existente.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/26/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 5ff4184a6147c367c64b5c90c090e1abe59edb90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importar um arquivo BACPAC para um novo Banco de Dados SQL do Azure

Quando você precisa importar um banco de dados de um arquivo ou ao migrar de outra plataforma, você pode importar o esquema de banco de dados e os dados de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Um arquivo BACPAC é um arquivo ZIP com uma extensão BACPAC que contém os metadados e dados de um banco de dados do SQL Server. Um arquivo BACPAC pode ser importado do armazenamento de Blobs do Azure (apenas no armazenamento padrão) ou de uma determinada localização do armazenamento local. Para aumentar a velocidade de importação, recomendamos que você especifique um serviço desempenho e da camada de nível superior tal como um P6 e, em seguida, reduza verticalmente conforme apropriado após a importação ser bem-sucedida. Além disso, o nível de compatibilidade do banco de dados após a importação baseia-se no nível de compatibilidade do banco de dados de origem. 

> [!IMPORTANT] 
> Depois de migrar seu banco de dados para o Banco de Dados SQL do Azure, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.   >

> [!NOTE]
> Para importar um BACPAC para um novo banco de dados, você deve primeiro criar um servidor lógico do Banco de Dados SQL do Azure. Para um tutorial que mostra como migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure usando o SQLPackage, consulte [Migrar um banco de dados do SQL Server](sql-database-migrate-your-sql-server-database.md)
>

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Exportar de um arquivo BACPAC usando o portal do Azure

Este artigo fornece instruções para criar um Banco de Dados SQL Azure de um arquivo BACPAC armazenado no Armazenamento de Blobs do Azure usando o [Portal do Azure](https://portal.azure.com). Importar usando o Portal do Azure dá suporte somente à importação de um arquivo BACPAC do Armazenamento de Blobs do Azure.

Para importar um banco de dados usando o Portal do Azure, abra a página do servidor ao qual associar o banco de dados e clique em **Importar** na barra de ferramentas. Especifique a conta de armazenamento e o contêiner e selecione o arquivo BACPAC que você deseja importar. Selecione o tamanho do novo banco de dados (normalmente, o mesmo que o de origem) e forneça credenciais do SQL Server de destino.  

   ![Importação de banco de dados](./media/sql-database-import/import.png)

Para monitorar o progresso da operação de importação, abra a página para o servidor lógico que contém o banco de dados que está sendo importado. Role para baixo até **Operações** e, em seguida, clique em **Importar/Exportar histórico**.

### <a name="monitor-the-progress-of-an-import-operation"></a>Monitorar o progresso de uma operação de importação

Para monitorar o progresso da operação de importação, abra a página para o servidor lógico que contém o banco de dados que está sendo importado. Role para baixo até **Operações** e, em seguida, clique em **Importar/Exportar histórico**.
   
   ![importar](./media/sql-database-import/import-history.png) ![importar status](./media/sql-database-import/import-status.png)

Para verificar se o banco de dados está ativo no servidor, clique em **Bancos de Dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importação de um arquivo BACPAC usando o SQLPackage

Para importar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consulte [Importar parâmetros e propriedades](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). O utilitário SQLPackage acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Recomendamos o uso do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Confira o comando SQLPackage a seguir para um script de exemplo sobre como importar o banco de dados **AdventureWorks2008R2** do armazenamento local para um servidor lógico do Banco de Dados SQL do Azure, chamado **mynewserver20170403** neste exemplo. Esse script mostra a criação de um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de Serviço **P6**. Altere esses valores conforme apropriado para o ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Um servidor lógico do Banco de Dados SQL do Azure escuta na porta 1433. Se você estiver tentando conectar um servidor lógico do Banco de Dados SQL do Azure de dentro de um firewall corporativo, essa porta deverá estar aberta no firewall corporativo para que você possa conectar-se com êxito.
>

Este exemplo mostra como importar um banco de dados usando SqlPackage.exe com Autenticação Universal do Active Directory:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importação de um arquivo BACPAC usando o PowerShell

Use o cmdlet [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) para enviar uma solicitação de importação de banco de dados para o serviço de Banco de Dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de importação pode demorar para ser concluída.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Para verificar o status da solicitação de importação, use o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Executar isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: Êxito**, a importação estará concluída.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Para outro exemplo de script, confira [Importar um banco de dados de um arquivo BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como conectar e consultar um Banco de Dados SQL importado, confira [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md).
* Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC).
* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).



