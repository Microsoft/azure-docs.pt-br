---
title: Importar um arquivo BACPAC para criar um banco de dados no Banco de Dados SQL do Azure
description: Crie um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure com base em um arquivo BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: eddcab2c0a34ef437e4f2f1e2203fee9065133a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781875"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Guia de início rápido: Importar um arquivo BACPAC para um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Importe um banco de dados do SQL Server para o Banco de Dados SQL do Azure ou a Instância Gerenciada de SQL usando um arquivo [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). Você pode importar dados de um arquivo BACPAC armazenado no Armazenamento de Blobs do Azure (apenas no Armazenamento Standard) ou de uma determinada localização do armazenamento local. Para maximizar a velocidade de importação fornecendo mais recursos e mais rápidos, dimensione seu banco de dados para uma camada de serviço superior e o tamanho da computação durante o processo de importação. Em seguida, você poderá reduzir verticalmente após a importação ser realizada.

> [!NOTE]
> O nível de compatibilidade do banco de dados se baseia no nível de compatibilidade do banco de dados de origem.

> [!IMPORTANT]
> Após a importação dos dados, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.

## <a name="using-azure-portal"></a>Usando o Portal do Azure

Assista a este vídeo para ver como importar um arquivo BACPAC no portal do Azure ou continue lendo abaixo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal do Azure](https://portal.azure.com) *somente* oferece suporte à criação de um banco de dados individual no Banco de Dados SQL do Azure e *somente* de um arquivo BACPAC armazenado no Armazenamento de Blobs do Azure.

Para migrar um banco de dados para uma [Instância Gerenciada de SQL do Azure](../managed-instance/sql-managed-instance-paas-overview.md) por meio de um arquivo BACPAC, use o SQL Server Management Studio ou o SqlPackage. No momento, não há suporte para o portal do Azure nem para o Azure PowerShell.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como os arquivos temporários gerados pelo DACFx (Data-Tier Application Framework). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir até três vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450 GB de espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar sqlpackage.exe em um computador com espaço em disco local suficiente. Incentivamos o uso do SqlPackage para importar/exportar bancos de dados maiores que 150 GB a fim de evitar esse problema.

1. Para fazer a importação por meio de um arquivo BACPAC para um novo banco de dados individual usando o portal do Azure, abra a página do servidor apropriada e, na barra de ferramentas, selecione **Importar banco de dados**.  

   ![Banco de dados import1](./media/database-import/sql-server-import-database.png)

1. Selecione a conta de armazenamento e o contêiner para o arquivo BACPAC e, em seguida, selecione o arquivo BACPAC do qual importar.

1. Especifique o tamanho do novo banco de dados (normalmente, o mesmo que o original) e forneça as credenciais do SQL Server de destino. Para obter uma lista de valores possíveis para um novo banco de dados do Banco de Dados SQL do Azure, confira [Criar Banco de Dados](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).

   ![Banco de dados import2](./media/database-import/sql-server-import-database-settings.png)

1. Clique em **OK**.

1. Para monitorar o progresso de uma importação, abra a página do servidor do banco de dados e, em **Configurações**, selecione **Histórico de importação/exportação**. Quando obtiver êxito, a importação terá o status **Concluído**.

   ![Status de importação do banco de dados](./media/database-import/sql-server-import-database-history.png)

1. Para verificar se o banco de dados está ativo no servidor, selecione **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="using-sqlpackage"></a>Como usar o SqlPackage

Para importar um Banco de Dados do SQL Server usando o utilitário de linha de comando [SqlPackage](/sql/tools/sqlpackage), confira [Importar parâmetros e propriedades](/sql/tools/sqlpackage#import-parameters-and-properties). O [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e o [SQL Server Data Tools para Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) incluem o SqlPackage. Você também pode fazer o download da versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do Centro de Download da Microsoft. 

Para escala e desempenho, recomendamos usar o SqlPackage na maioria dos ambientes de produção em vez de usar no portal do Azure. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos `BACPAC`, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

O modelo de provisionamento baseado em DTU é compatível com valores de tamanho máximo de banco de dados selecionados para cada camada. Ao importar um banco de dados [use um desses valores compatíveis](/sql/t-sql/statements/create-database-transact-sql). 

O comando do SqlPackage a seguir importa o banco de dados **AdventureWorks2008R2** do armazenamento local para um servidor SQL lógico chamado **mynewserver20170403**. Ele cria um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de serviço **P6**. Altere esses valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar ao Banco de Dados SQL do Azure por trás de um firewall corporativo, o firewall precisa ter a porta 1433 aberta. Para se conectar à Instância Gerenciada de SQL, você precisa ter uma [conexão ponto a site](../managed-instance/point-to-site-p2s-configure.md) ou uma conexão do ExpressRoute.

Este exemplo mostra como importar um banco de dados usando o SqlPackage com a Autenticação Universal do Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Usando o PowerShell

> [!NOTE]
> Atualmente, uma [Instância Gerenciada de SQL](../managed-instance/sql-managed-instance-paas-overview.md) não dá suporte à migração de um banco de dados para um banco de dados de instância por meio de um arquivo BACPAC usando o Azure PowerShell. Para fazer a importação para uma Instância Gerenciada de SQL, use o SQL Server Management Studio ou o SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal ou do PowerShell precisam armazenar o arquivo BACPAC, bem como os arquivos temporários gerados pelo DACFx (Data-Tier Application Framework). O espaço em disco necessário varia significativamente entre os BDs com o mesmo tamanho e pode exigir até três vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450 GB de espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro “Não há espaço suficiente no disco”. Nesse caso, a solução alternativa é executar sqlpackage.exe em um computador com espaço em disco local suficiente. Ao importar/exportar bancos de dados maiores que 150 GB, use o SqlPackage para evitar esse problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Ainda há suporte para o módulo do ARM (Azure Resource Manager) do PowerShell, mas todo o desenvolvimento futuro destina-se ao módulo Az.Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

Use o cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) para enviar uma solicitação de importação de banco de dados para o Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída. O modelo de provisionamento baseado em DTU é compatível com valores de tamanho máximo de banco de dados selecionados para cada camada. Ao importar um banco de dados [use um desses valores compatíveis](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Use o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. A execução do cmdlet imediatamente após a solicitação geralmente retorna `Status: InProgress`. A importação estará concluída quando `Status: Succeeded` for exibido.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az-sql-db-import](/cli/azure/sql/db#az_sql_db_import) para enviar uma solicitação de importação de banco de dados para o Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída. O modelo de provisionamento baseado em DTU é compatível com valores de tamanho máximo de banco de dados selecionados para cada camada. Ao importar um banco de dados [use um desses valores compatíveis](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Para outro exemplo de script, confira [Importar um banco de dados de um arquivo BACPAC](scripts/import-from-bacpac-powershell.md).

## <a name="cancel-the-import-request"></a>Cancelar a solicitação de importação

Use a [API de Cancelamento – Operações de Banco de Dados](/rest/api/sql/databaseoperations/cancel) ou o [comando Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity) do PowerShell, no caso um exemplo de comando do PowerShell.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Limitações

- Não há suporte para a importação de um banco de dados no pool elástico. Você pode importar dados para um banco de dados individual e, em seguida, mover o banco de dados para um pool elástico.
- O Serviço de Importação/Exportação não funciona quando a opção Permitir acesso aos serviços do Azure está definida como DESATIVADO. No entanto, resolva o problema executando manualmente sqlpackage.exe em uma VM do Azure ou executando a exportação diretamente no código por meio da API do DACFx.
- A importação não dá suporte à especificação de uma redundância de armazenamento de backup durante a criação de um banco de dados e cria com a redundância do armazenamento de backup com redundância geográfica padrão. Para resolver isso, primeiro, crie um banco de dados vazio com a redundância de armazenamento de backup desejada usando o portal do Azure ou o PowerShell e importe o BACPAC para esse banco de dados vazio. 

> [!NOTE]
> Atualmente, a redundância do armazenamento de backup configurável do Banco de Dados SQL do Azure só está disponível na versão prévia pública na região Sudeste da Ásia do Azure.

## <a name="import-using-wizards"></a>Importar usando assistentes

Você também pode usar esses assistentes.

- [Importar o Assistente de Aplicativo da Camada de Dados no SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistente de Importação e Exportação do SQL Server](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar a um banco de dados do Banco de Dados SQL do Azure e consultá-lo, confira [Guia de início rápido: Banco de Dados SQL do Azure: Usar o SQL Server Management Studio para se conectar aos dados e consultá-los](connect-query-ssms.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](migrate-to-database-from-sql-server.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](../../storage/blobs/security-recommendations.md).