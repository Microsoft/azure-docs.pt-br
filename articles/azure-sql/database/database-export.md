---
title: Exportar um banco de dados SQL do Azure para um arquivo BACPAC (o portal do Azure)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Exporte um banco de dados para um arquivo BACPAC usando o portal do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: f874803e0ae361255754477ca68184255f35b91f
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107371"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exportar para um arquivo BACPAC – banco de dados SQL do Azure e Azure SQL Instância Gerenciada

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando for preciso exportar um banco de dados para arquivamento ou para mover para outra plataforma, você pode exportar os dados e o esquema do banco de dados para um arquivo [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4). Um arquivo BACPAC é um arquivo ZIP com uma extensão de BACPAC que contém os metadados e os dados do Database. Um arquivo BACPAC pode ser armazenado no armazenamento de BLOBs do Azure ou no armazenamento local em um local e posteriormente importado de volta para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou uma instância de SQL Server.

## <a name="considerations"></a>Considerações

- Para que uma exportação seja transacionalmente consistente, você deve garantir que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](database-copy.md) de seu banco de dados.
- Se você estiver exportando para o armazenamento de blobs, o tamanho máximo de um arquivo BACPAC é de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
- Não há suporte para a exportação de um arquivo BACPAC no armazenamento Premium do Azure usando os métodos abordados neste artigo.
- No momento, não há suporte para o armazenamento por trás de um firewall.
- O nome do arquivo de armazenamento ou o valor de entrada para StorageURI deve ter menos de 128 caracteres e não pode terminar com '. ' e não pode conter caracteres especiais, como um caractere de espaço ou ' <, >, *,%, &,:, \, /,? '. 
- Se a operação de exportação exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:

  - Aumente temporariamente o tamanho da computação.
  - Interromper toda a atividade de leitura e gravação durante a exportação.
  - Use um [índice clusterizado](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Azure cria backups automaticamente para cada banco de dados de usuário. Para obter detalhes, consulte [visão geral de continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md) e [backups de banco de dados SQL](automated-backups-overview.md)

## <a name="the-azure-portal"></a>O portal do Azure

Atualmente, não há suporte para a exportação de um BACPAC de um banco de dados do [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) usando o portal do Azure. Em vez disso, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como os arquivos temporários gerados pelo DACFx (Data-Tier Application Framework). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir até três vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar sqlpackage.exe em um computador com espaço em disco local suficiente. Incentivamos o uso do [SqlPackage](#sqlpackage-utility) para importar/exportar bancos de dados maiores que 150 GB para evitar esse problema.

1. Para exportar um banco de dados usando o [Portal do Azure](https://portal.azure.com), abra a página do banco de dados e clique em **Exportar** na barra de ferramentas.

   ![Captura de tela que realça o botão Exportar.](./media/database-export/database-export1.png)

2. Especifique o nome do arquivo BACPAC, selecione uma conta de armazenamento do Azure existente e um contêiner para a exportação, em seguida, forneça as credenciais apropriadas para acessar o banco de dados de origem. Um **logon de administrador** do SQL Server é necessário aqui mesmo se você for o administrador do Azure, pois ser um administrador do Azure não equivale a ter permissões de administrador no banco de dados SQL do Azure ou no azure SQL instância gerenciada.

    ![Exportação de banco de dados](./media/database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorar o progresso da operação de exportação, abra a página do servidor que contém o banco de dados que está sendo exportado. Em **Configurações**, clique em **Histórico de importação/exportação**.

   ![histórico de exportação](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Utilitário SQLPackage

Para exportar um banco de dados no banco de dados SQL usando o utilitário de linha de comando [SqlPackage](/sql/tools/sqlpackage) , consulte [Exportar parâmetros e propriedades](/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SQLPackage acompanha as últimas versões do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Recomendamos o uso do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Este exemplo mostra como exportar um banco de dados usando SqlPackage.exe com Autenticação Universal do Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornecem um assistente para exportar um banco de dados no banco de dados SQL do Azure ou um banco de dados SQL Instância Gerenciada para um arquivo BACPAC. Consulte [Exportar um aplicativo da camada de dados](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> O [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) atualmente não dá suporte à exportação de um banco de dados para um arquivo BACPAC usando Azure PowerShell. Para exportar uma instância gerenciada para um arquivo BACPAC, use o SQL Server Management Studio ou o SQLPackage.

Use o cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) para enviar uma solicitação de exportação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o status da solicitação de exportação, use o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Executar isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: Êxito**, a exportação estará concluída.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a retenção de backup de longo prazo de um banco de dados individual e bancos de dado em pool como uma alternativa para exportar um banco de dados para fins de arquivamento, consulte [retenção de backup de longo prazo](long-term-retention-overview.md). Você pode usar os trabalhos do SQL Agent para agendar [backups do banco de dados de somente cópia](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa à retenção do backup de longo prazo.
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Para saber mais sobre como exportar um BACPAC de um banco de dados do SQL Server, veja [Exportar um aplicativo da camada de dados](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para saber mais sobre como usar o serviço de migração de dados para migrar um banco de dado, confira [migrar de SQL Server para o Azure SQL Database offline usando DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Se você estiver exportando do SQL Server como um prelúdio para a migração para o Banco de Dados SQL do Azure, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](migrate-to-database-from-sql-server.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](../../storage/blobs/security-recommendations.md).
