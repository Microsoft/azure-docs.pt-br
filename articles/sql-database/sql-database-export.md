---
title: Exportar um banco de dados SQL do Azure para um arquivo BACPAC | Microsoft Docs
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o Portal do Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 4427b74f205dcd2eabf825bbfc345279dc303f96
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC

Quando for preciso exportar um banco de dados para arquivamento ou para mover para outra plataforma, você pode exportar os dados e o esquema do banco de dados para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Um arquivo BACPAC é um arquivo ZIP com uma extensão BACPAC que contém os metadados e dados de um banco de dados do SQL Server. Um arquivo BACPAC pode ser colocado no Armazenamento de Blobs do Azure ou em uma determinada localização no armazenamento local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server. 

> [!IMPORTANT] 
> A exportação automatizada do Banco de Dados SQL do Azure foi desativada em 1° de março de 2017. Você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md
) ou a [Automação do Azure](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell de acordo com um agendamento de sua escolha. Para obter um exemplo, baixe o [script PowerShell de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) do Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerações ao exportar um banco de dados SQL do Azure

* Para que uma exportação seja transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados SQL do Azure.
* Se você estiver exportando para o armazenamento de blobs, o tamanho máximo de um arquivo BACPAC é de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
* Não há suporte para a exportação de um arquivo BACPAC no armazenamento Premium do Azure usando os métodos abordados neste artigo.
* Se a operação de exportação do Banco de Dados SQL do Azure exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
  * Aumentar temporariamente seu nível de serviço.
  * Interromper toda a atividade de leitura e gravação durante a exportação.
  * Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md) e [Backups do Banco de Dados SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportar para um arquivo BACPAC usando o portal do Azure

Para exportar um banco de dados usando o [Portal do Azure](https://portal.azure.com), abra a página do banco de dados e clique em **Exportar** na barra de ferramentas. Especifique o nome do arquivo BACPAC, forneça a conta de armazenamento e o contêiner do Azure para a exportação e forneça as credenciais para conectar-se ao banco de dados de origem.  

![Exportação de banco de dados](./media/sql-database-export/database-export.png)

Para monitorar o progresso da operação de exportação, abra a página para o servidor lógico que contém o banco de dados que está sendo exportado. Role para baixo até **Operações** e, em seguida, clique em **Importar/Exportar histórico**.

![exportar histórico](./media/sql-database-export/export-history.png)
![exportar status de histórico](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportar para um arquivo BACPAC usando o utilitário SQLPackage

Para exportar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consulte [Exportar parâmetros e propriedades](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). O utilitário SQLPackage acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Recomendamos o uso do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados do Azure SQL usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar um banco de dados usando SqlPackage.exe com Autenticação Universal do Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportar para um arquivo BACPAC usando o SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio também fornecem um assistente para exportar um Banco de Dados SQL do Azure para um arquivo BACPAC. Consulte [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportar para um arquivo BACPAC usando o PowerShell

Use o cmdlet [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) para enviar uma solicitação de exportação de banco de dados para o serviço de Banco de Dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Para verificar o status da solicitação de exportação, use o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Executar isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: Êxito**, a exportação estará concluída.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre retenção de backup de longo prazo de um backup do Banco de Dados SQL do Azure como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados do Azure SQL usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para saber mais sobre como importar um BACPAC para um Banco de Dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Para saber mais sobre como exportar um BACPAC de um banco de dados do SQL Server, consulte [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) e [Migrar seu primeiro banco de dados](sql-database-migrate-your-sql-server-database.md).
* Se você estiver exportando do SQL Server como um prelúdio para a migração para o Banco de Dados SQL do Azure, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
