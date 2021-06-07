---
title: Migrar bancos de dados para o SQL Instância Gerenciada usando o serviço de reprodução de log
description: Saiba como migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 1b2a3f018b16258622b817648cb00e230313bf49
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564510"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log (versão prévia)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração de banco de dados do SQL Server 2008-2019 para o SQL Instância Gerenciada do Azure usando o serviço de reprodução de log (LRS), atualmente em visualização pública. LRS é um serviço de nuvem habilitado para o SQL Instância Gerenciada e é baseado em SQL Server tecnologia de envio de logs. 

O [serviço de migração de banco de dados do Azure](../../dms/tutorial-sql-server-to-managed-instance.md) e o LRS usam a mesma tecnologia de migração subjacente e as mesmas APIs. Ao liberar o LRS, estamos permitindo ainda mais migrações personalizadas complexas e arquitetura híbrida entre o SQL Server local e o SQL Instância Gerenciada.

## <a name="when-to-use-log-replay-service"></a>Quando usar o serviço de reprodução de log

Quando você não pode usar o serviço de migração de banco de dados do Azure para migração, você pode usar o LRS diretamente com o PowerShell, CLI do Azure cmdlets ou APIs para criar e orquestrar manualmente as migrações de banco de dados para o SQL Instância Gerenciada. 

Você pode considerar o uso de LRS nos seguintes casos:
- Você precisa de mais controle para seu projeto de migração de banco de dados.
- Há pouca tolerância para tempo de inatividade na transferência de migração.
- O arquivo executável do serviço de migração de banco de dados não pode ser instalado em seu ambiente.
- O arquivo executável do serviço de migração de banco de dados não tem acesso aos backups de banco de dados.
- Nenhum acesso ao sistema operacional do host está disponível ou não há privilégios de administrador.
- Você não pode abrir portas de rede do seu ambiente para o Azure.
- Os backups são armazenados diretamente no armazenamento de BLOBs do Azure por meio da `TO URL` opção.
- Você precisa usar backups diferenciais.

> [!NOTE]
> É recomendável automatizar a migração de bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de migração de banco de dados. Esse serviço usa o mesmo serviço de nuvem LRS no back-end, com o envio de logs no `NORECOVERY` modo. Considere o uso manual de LRS para orquestrar migrações quando o serviço de migração de banco de dados não oferecer suporte completo a seus cenários.

## <a name="how-it-works"></a>Como ele funciona

Criar uma solução personalizada usando o LRS para migrar bancos de dados para a nuvem requer várias etapas de orquestração, conforme mostrado no diagrama e na tabela mais adiante nesta seção.

A migração consiste em fazer backups de banco de dados completos no SQL Server com `CHECKSUM` habilitado e copiar arquivos de backup para o armazenamento de BLOBs do Azure. LRS é usado para restaurar arquivos de backup do armazenamento de BLOBs para o SQL Instância Gerenciada. O armazenamento de BLOBs é o armazenamento intermediário entre SQL Server e Instância Gerenciada SQL.

O LRS monitora o armazenamento de BLOBs para quaisquer novos backups diferenciais ou de log adicionados após a restauração do backup completo. O LRS restaura automaticamente esses novos arquivos. Você pode usar o serviço para monitorar o progresso dos arquivos de backup que estão sendo restaurados no SQL Instância Gerenciada e pode parar o processo, se necessário.

LRS não requer uma Convenção de nomenclatura específica para arquivos de backup. Ele examina todos os arquivos colocados no armazenamento de BLOBs e constrói a cadeia de backup a partir da leitura somente dos cabeçalhos de arquivo. Os bancos de dados estão em um estado de "restauração" durante o processo de migração. Os bancos de dados são restaurados no modo [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) , portanto, eles não podem ser usados para leitura ou gravação até que o processo de migração seja concluído. 

Se você estiver migrando vários bancos de dados, precisará:
 
- Coloque os backups de cada banco de dados em uma pasta separada no armazenamento de BLOBs.
- Inicie o LRS separadamente para cada banco de dados.
- Especifique caminhos diferentes para separar pastas de armazenamento de BLOBs. 

Você pode iniciar o LRS no modo de *preenchimento automático* ou *contínuo* . Quando você o inicia no modo de preenchimento automático, a migração será concluída automaticamente quando o último dos arquivos de backup especificados tiver sido restaurado. Quando você iniciar o LRS no modo contínuo, o serviço irá restaurar continuamente todos os novos arquivos de backup adicionados e a migração será concluída apenas na transferência manual. 

É recomendável que você recorte manualmente depois que o backup final da cauda de log tiver sido feito e exibido como restaurado no SQL Instância Gerenciada. A etapa final de transferência fará com que o banco de dados fique online e disponível para uso de leitura e gravação no SQL Instância Gerenciada.

Depois que o LRS for interrompido, automaticamente por meio de preenchimento automático ou manualmente por meio de transferência, você não poderá retomar o processo de restauração de um banco de dados que foi colocado online no SQL Instância Gerenciada. Para restaurar arquivos de backup adicionais após a conclusão da migração por meio de preenchimento automático ou de transferência, você precisa excluir o banco de dados. Você também precisa restaurar toda a cadeia de backup do zero reiniciando LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagrama que explica as etapas de orquestração do serviço de reprodução de log para o SQL Instância Gerenciada." border="false":::
    
| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. copiar backups de banco de dados de SQL Server para o armazenamento de BLOBs**. | Copie backups completos, diferenciais e de log de SQL Server para um contêiner de armazenamento de BLOBs usando [Azcopy](../../storage/common/storage-use-azcopy-v10.md) ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Use qualquer nome de arquivo. LRS não requer uma Convenção de nomenclatura de arquivo específica.<br /><br />Ao migrar vários bancos de dados, você precisará de uma pasta separada para cada um deles. |
| **2. Inicie o LRS na nuvem**. | Você pode reiniciar o serviço com uma opção de cmdlets: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) ou CLI do Azure ([cmdlets az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Inicie o LRS separadamente para cada banco de dados que aponta para uma pasta de backup no armazenamento de BLOBs. <br /><br /> Depois de iniciar o serviço, ele usará backups do contêiner de armazenamento de BLOBs e começará a restaurá-los no SQL Instância Gerenciada.<br /><br /> Se você iniciou o LRS no modo contínuo, depois que todos os backups inicialmente carregados forem restaurados, o serviço observará os novos arquivos carregados na pasta. O serviço aplicará continuamente logs com base na cadeia LSN (número de sequência de log) até que seja interrompido. |
| **2,1. Monitore o progresso da operação**. | Você pode monitorar o progresso da operação de restauração com uma opção de cmdlets: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) ou CLI do Azure ([cmdlets az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. pare a operação se necessário**. | Se você precisar interromper o processo de migração, terá a opção de cmdlets: PowerShell ([Stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) ou CLI do Azure ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Parar a operação excluirá o banco de dados que você está restaurando no SQL Instância Gerenciada. Depois de parar uma operação, você não pode retomar o LRS para um banco de dados. Você precisa reiniciar o processo de migração do zero. |
| **3. recorte para a nuvem quando você estiver pronto**. | Pare o aplicativo e a carga de trabalho. Faça o último backup da parte final do log e carregue-o no armazenamento de BLOBs do Azure.<br /><br /> Conclua a transferência iniciando uma `complete` operação LRS com uma opção de cmdlets: PowerShell ([Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) ou CLI do Azure [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Esta operação irá parar o LRS e fará com que o banco de dados fique online para uso de leitura e gravação no SQL Instância Gerenciada.<br /><br /> Reaponte a cadeia de conexão do aplicativo de SQL Server para Instância Gerenciada SQL. |

## <a name="requirements-for-getting-started"></a>Requisitos para introdução

### <a name="sql-server-side"></a>Lado SQL Server
- SQL Server 2008-2019
- Backup completo de bancos de dados (um ou vários arquivos)
- Backup diferencial (um ou vários arquivos)
- Backup de log (não dividido para um arquivo de log de transações)
- `CHECKSUM` habilitado para backups (obrigatório)

### <a name="azure-side"></a>Lado do Azure
- Módulo AZ. SQL do PowerShell versão 2.16.0 ou posterior ([instalado](https://www.powershellgallery.com/packages/Az.Sql/) ou acessado por meio de [Azure cloud Shell](/azure/cloud-shell/))
- CLI do Azure versão 2.19.0 ou posterior ([instalada](/cli/azure/install-azure-cli))
- Contêiner de armazenamento de BLOBs do Azure provisionado
- Token de segurança SAS (assinatura de acesso compartilhado) com permissões de leitura e lista geradas para o contêiner de armazenamento de BLOBs

### <a name="migration-of-multiple-databases"></a>Migração de vários bancos de dados
Você deve inserir arquivos de backup para bancos de dados diferentes em pastas separadas no armazenamento de BLOBs.

Inicie o LRS separadamente para cada banco de dados apontando para uma pasta apropriada no armazenamento de BLOBs. O LRS pode dar suporte a até 100 processos de restauração simultâneas por instância gerenciada única.

### <a name="azure-rbac-permissions"></a>Permissões do RBAC do Azure
Executar o LRS por meio dos clientes fornecidos requer uma das seguintes funções do Azure:
- Função de proprietário da assinatura
- Função [colaborador de instância gerenciada](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Função personalizada com a seguinte permissão: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Práticas recomendadas

Sugerimos as seguintes práticas recomendadas:
- Execute [Assistente de migração de dados](/sql/dma/dma-overview) para validar que os bancos de dados estão prontos para serem migrados para o SQL instância gerenciada. 
- Divida os backups completos e diferenciais em vários arquivos, em vez de usar um único arquivo.
- Habilite a compactação de backup.
- Use Cloud Shell para executar scripts, pois eles sempre serão atualizados para os cmdlets mais recentes lançados.
- Planeje concluir a migração dentro de 47 horas depois de iniciar o LRS. Esse é um período de carência que impede a instalação de patches de software gerenciados pelo sistema.

> [!IMPORTANT]
> - Você não pode usar o banco de dados que está sendo restaurado por meio do LRS até que o processo de migração seja concluído. 
> - O LRS não dá suporte a acesso somente leitura a bancos de dados durante a migração.
> - Após a conclusão da migração, o processo de migração é finalizado porque o LRS não dá suporte à retomada do processo de restauração.

## <a name="steps-to-execute"></a>Etapas para executar

### <a name="make-backups-of-sql-server"></a>Fazer backups de SQL Server

Você pode fazer backups de SQL Server usando uma das seguintes opções:

- Faça backup no armazenamento em disco local e, em seguida, carregue arquivos para o armazenamento de BLOBs do Azure, se seu ambiente restringe os backups diretos para o armazenamento de BLOBs.
- Faça backup diretamente no armazenamento de blob com a `TO URL` opção no T-SQL, se o seu ambiente e os procedimentos de segurança permitirem isso. 

Defina os bancos de dados que você deseja migrar para o modo de recuperação completa para permitir backups de log.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Para fazer manualmente backups completos, diferenciais e de log de seu banco de dados no armazenamento local, use os seguintes scripts T-SQL de exemplo. Verifique se a `CHECKSUM` opção está habilitada, pois ela é obrigatória para lRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O armazenamento de BLOBs do Azure é usado como armazenamento intermediário para arquivos de backup entre SQL Server e Instância Gerenciada SQL. Para criar uma nova conta de armazenamento e um contêiner de blob dentro da conta de armazenamento, siga estas etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Criar um contêiner de blob](../../storage/blobs/storage-quickstart-blobs-portal.md) dentro da conta de armazenamento.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Copiar backups de SQL Server para o armazenamento de BLOBs

Na migração de bancos de dados para uma instância gerenciada usando o LRS, você pode usar as seguintes abordagens para carregar backups no armazenamento de BLOBs:
- Usando SQL Server o [backup nativo para a funcionalidade de URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Usando [Azcopy](../../storage/common/storage-use-azcopy-v10.md) ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/en-us/features/storage-explorer) para carregar backups em um contêiner de BLOB
- Usando Gerenciador de Armazenamento no portal do Azure

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Fazer backups de SQL Server diretamente para o armazenamento de BLOBs
Se suas políticas corporativas e de rede permitirem, uma alternativa é fazer backups de SQL Server diretamente para o armazenamento de BLOBs usando a opção SQL Server [backup nativo para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Se você puder buscar essa opção, não será necessário fazer backups no armazenamento local e carregá-los no armazenamento de BLOBs.

Como a primeira etapa, essa operação exige que você gere um token de autenticação SAS para o armazenamento de BLOB e, em seguida, importe o token para SQL Server. A segunda etapa é fazer backups com a `TO URL` opção no T-SQL. Verifique se todos os backups são feitos com a `CHEKSUM` opção habilitado.

Para referência, o código de exemplo a seguir faz backups no armazenamento de BLOBs. Este exemplo não inclui instruções sobre como importar o token SAS. Você pode encontrar instruções detalhadas, incluindo como gerar e importar o token SAS para SQL Server, no tutorial usar o [armazenamento de BLOBs do Azure com SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Gerar um token de autenticação SAS de armazenamento de BLOBs para LRS

O armazenamento de BLOBs do Azure é usado como armazenamento intermediário para arquivos de backup entre SQL Server e Instância Gerenciada SQL. Você precisa gerar um token de autenticação SAS, com apenas permissões de lista e leitura, para LRS. O token permitirá que o LRS acesse o armazenamento de BLOBs e use os arquivos de backup para restaurá-los no SQL Instância Gerenciada. 

Siga estas etapas para gerar o token:

1. Abra Gerenciador de Armazenamento da portal do Azure.
2. Expanda **contêineres de blob**.
3. Clique com o botão direito do mouse no contêiner de BLOB e selecione **obter assinatura de acesso compartilhado**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Captura de tela que mostra seleções para gerar um token de autenticação S A S.":::

4. Selecione o período de expiração do token. Verifique se o token é válido para a duração da sua migração.
5. Selecione o fuso horário para o token: UTC ou sua hora local.
    
   > [!IMPORTANT]
   > O fuso horário do token e sua instância gerenciada podem não ser compatíveis. Verifique se o token SAS tem a validade de tempo apropriada, levando os fusos horários em consideração. Se possível, defina o fuso horário para uma hora anterior e posterior da sua janela de migração planejada.
6. Selecione somente permissões de **leitura** e **lista** .

   > [!IMPORTANT]
   > Não selecione nenhuma outra permissão. Se você fizer isso, o LRS não será iniciado. Esse requisito de segurança é por design.
7. Selecione **Criar**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Captura de tela que mostra seleções para vencimento de token s A S, fuso horário e permissões, juntamente com o botão criar.":::

A autenticação SAS é gerada com a validade de tempo especificada. Você precisa da versão do URI do token, conforme mostrado na captura de tela a seguir.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Captura de tela que mostra um exemplo da versão U R I de um token s A S.":::

### <a name="copy-parameters-from-the-sas-token"></a>Copiar parâmetros do token SAS

Antes de usar o token SAS para iniciar o LRS, você precisa entender sua estrutura. O URI do token SAS gerado consiste em duas partes separadas com um ponto de interrogação ( `?` ), conforme mostrado neste exemplo:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exemplo U R I para um token s de s gerado para o serviço de reprodução de log." border="false":::

A primeira parte, começando com `https://` até o ponto de interrogação ( `?` ), é usada para o `StorageContainerURI` parâmetro que é alimentado como entrada em lRS. Ele fornece informações de LRS sobre a pasta em que os arquivos de backup de banco de dados são armazenados.

A segunda parte, começando após o ponto de interrogação ( `?` ) e indo até o final da cadeia de caracteres, é o `StorageContainerSasToken` parâmetro. Esse é o token de autenticação assinado real, que é válido durante a duração do tempo especificado. Essa parte não precisa, necessariamente, iniciar com `sp=` , conforme mostrado no exemplo. Seu caso pode ser diferente.

Copie os parâmetros da seguinte maneira:

1. Copie a primeira parte do token, começando do `https://` caminho até o ponto de interrogação ( `?` ). Use-o como o `StorageContainerUri` parâmetro no PowerShell ou o CLI do Azure para iniciar o lRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Captura de tela que mostra a cópia da primeira parte do token.":::

2. Copie a segunda parte do token, começando do ponto de interrogação ( `?` ) até o final da cadeia de caracteres. Use-o como o `StorageContainerSasToken` parâmetro no PowerShell ou o CLI do Azure para iniciar o lRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Captura de tela que mostra a cópia da segunda parte do token.":::
   
> [!NOTE]
> Não inclua o ponto de interrogação ao copiar uma parte do token.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Faça logon no Azure e selecione uma assinatura

Use o seguinte cmdlet do PowerShell para fazer logon no Azure:

```powershell
Login-AzAccount
```

Selecione a assinatura apropriada em que a instância gerenciada reside usando o seguinte cmdlet do PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Inicie a migração

Inicie a migração iniciando o LRS. Você pode iniciar o serviço no modo de preenchimento automático ou contínuo. 

Quando você usa o modo de preenchimento automático, a migração será concluída automaticamente quando o último dos arquivos de backup especificados tiver sido restaurado. Essa opção requer que o comando Iniciar especifique o nome do arquivo do último backup. 

Quando você usar o modo contínuo, o serviço irá restaurar continuamente todos os novos arquivos de backup que foram adicionados. A migração será concluída apenas na transferência manual. 

### <a name="start-lrs-in-autocomplete-mode"></a>Iniciar o LRS no modo de preenchimento automático

Para iniciar o LRS no modo de preenchimento automático, use os comandos do PowerShell ou do CLI do Azure a seguir. Especifique o nome do último arquivo de backup usando o `-LastBackupName` parâmetro. Após restaurar o último dos arquivos de backup especificados, o serviço iniciará automaticamente uma transferência.

Aqui está um exemplo de como iniciar o LRS no modo de preenchimento automático usando o PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Aqui está um exemplo de como iniciar o LRS no modo de preenchimento automático usando o CLI do Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS no modo contínuo

Aqui está um exemplo de como iniciar o LRS no modo contínuo usando o PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Aqui está um exemplo de como iniciar o LRS no modo contínuo usando o CLI do Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Os clientes do PowerShell e da CLI para iniciar o LRS no modo contínuo são síncronos. Isso significa que os clientes aguardarão a resposta da API para relatar o êxito ou falha ao iniciar o trabalho. 

Durante essa espera, o comando não retornará o controle para o prompt de comando. Se você estiver criando scripts para a experiência de migração e precisar do comando LRS Start para retornar o controle imediatamente para continuar com o restante do script, você poderá executar o PowerShell como um trabalho em segundo plano com a `-AsJob` opção. Por exemplo:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quando você inicia um trabalho em segundo plano, um objeto de trabalho retorna imediatamente, mesmo se o trabalho levar um tempo estendido para ser concluído. É possível continuar a trabalhar na sessão sem interrupção enquanto o trabalho é executado. Para obter detalhes sobre como executar o PowerShell como um trabalho em segundo plano, consulte a documentação [Iniciar-trabalho do PowerShell](/powershell/module/microsoft.powershell.core/start-job#description) .

Da mesma forma, para iniciar um comando CLI do Azure no Linux como um processo em segundo plano, use o e comercial ( `&` ) no final do comando LRS Start:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Depois de iniciar o LRS, todos os patches de software gerenciado pelo sistema são interrompidos por 47 horas. Após essa janela, o próximo patch de software automatizado irá parar automaticamente o LRS. Se isso acontecer, você não poderá retomar a migração e precisará reiniciá-la do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorar o progresso da migração

Para monitorar o progresso da migração por meio do PowerShell, use o seguinte comando:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para monitorar o progresso da migração por meio do CLI do Azure, use o seguinte comando:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

Se você precisar parar a migração, use os cmdlets a seguir. Parar a migração excluirá o banco de dados de restauração no SQL Instância Gerenciada, portanto, retomar a migração não será possível.

Para interromper o processo de migração por meio do PowerShell, use o seguinte comando:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para interromper o processo de migração por meio do CLI do Azure, use o seguinte comando:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Concluir a migração (modo contínuo)

Se você iniciou o LRS no modo contínuo, depois de ter garantido que todos os backups foram restaurados, iniciar a transferência concluirá a migração. Após a transferência, o banco de dados será migrado e estará pronto para acesso de leitura e gravação.

Para concluir o processo de migração no modo contínuo do LRS por meio do PowerShell, use o seguinte comando:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Para concluir o processo de migração no modo contínuo LRS por meio do CLI do Azure, use o seguinte comando:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitações funcionais

As limitações funcionais do LRS são:
- O banco de dados que você está restaurando não pode ser usado para acesso somente leitura durante o processo de migração.
- Os patches de software gerenciado pelo sistema são bloqueados por 47 horas depois que você inicia o LRS. Depois que essa janela de tempo expirar, a próxima atualização de software irá parar o LRS. Em seguida, você precisa reiniciar o LRS do zero.
- LRS exige que os bancos de dados no SQL Server sejam submetidos a backup com a `CHECKSUM` opção habilitada.
- O token SAS que o LRS usará deve ser gerado para todo o contêiner de armazenamento de BLOBs do Azure e deve ter apenas permissões de leitura e lista.
- Os arquivos de backup para bancos de dados diferentes devem ser colocados em pastas separadas no armazenamento de BLOBs.
- LRS deve ser iniciado separadamente para cada banco de dados que aponta para pastas separadas com arquivos de backup no armazenamento de BLOBs.
- O LRS pode dar suporte a até 100 processos de restauração simultâneas por instância gerenciada única.

## <a name="troubleshooting"></a>Solução de problemas

Depois de iniciar o LRS, use o cmdlet de monitoramento ( `get-azsqlinstancedatabaselogreplay` ou `az_sql_midb_log_replay_show` ) para ver o status da operação. Se LRS falhar ao iniciar após algum tempo e você receber um erro, verifique os problemas mais comuns:

- Um banco de dados existente no SQL Instância Gerenciada tem o mesmo nome que o que você está tentando migrar de SQL Server? Resolva esse conflito renomeando um dos bancos de dados.
- O backup do banco de dados no SQL Server foi feito por meio da `CHECKSUM` opção?
- As permissões no token SAS só são lidas e listadas para LRS?
- Você copiava o token SAS para LRS após o ponto de interrogação ( `?` ), com conteúdo que começa desta forma: `sv=2020-02-10...` ? 
- O tempo de validade do token SAS é aplicável para a janela de tempo de iniciar e concluir a migração? Pode haver incompatibilidades devido aos diferentes fusos horários usados para o SQL Instância Gerenciada e o token SAS. Tente regenerar o token SAS e estender a validade do token da janela de tempo antes e depois da data atual.
- O nome do banco de dados, o nome do grupo de recursos e o nome da instância gerenciada estão escritos corretamente?
- Se você iniciou o LRS no modo de preenchimento automático, um nome de arquivo válido para o último backup foi especificado?

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [migrar SQL Server para a instância gerenciada do SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Saiba mais sobre as [diferenças entre SQL Server e instância gerenciada SQL](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as práticas recomendadas para cargas de trabalho de custo e de tamanho migradas para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).