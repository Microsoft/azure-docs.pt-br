---
title: Configurar a replicação em um banco de dados de instância gerenciada do banco de dados SQL | Microsoft Docs
description: Aprenda a configurar a replicação transacional em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: e4d056aacf8f3969b645747e2303574f3fea3bda
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357113"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure

A replicação transacional permite que você replique dados em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure de um banco de dados do SQL Server ou de outro banco de dados de instância. 

Você também pode usar a replicação transacional para enviar por push as alterações feitas em uma instância de banco de dados na instância gerenciada do banco de dados SQL para:

- Um banco de dados do SQL Server.
- Um único banco de dados SQL do Azure.
- Um banco de dados em pool em um pool Elástico do banco de dados SQL.
 
Replicação transacional está em visualização pública no [instância gerenciada do banco de dados SQL](sql-database-managed-instance.md). Uma instância gerenciada pode hospedar bancos de dados publicadores, distribuidores e assinantes. Confira [configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para obter as configurações disponíveis.

  > [!NOTE]
  > Este artigo destina-se para orientar um usuário em Configurando a replicação com um banco de dados do Azure a instância gerenciada de ponta a ponta, começando com a criação de grupo de recursos. Se você já gerenciou instâncias implantadas, pule para [etapa 4](#4---create-a-publisher-database) para criar seu banco de dados do publicador, ou [etapa 6](#6---configure-distribution) se você já tiver um banco de dados do publicador e assinante e está pronto para começar Configurando a replicação.  

## <a name="requirements"></a>Requisitos

Configurar uma instância gerenciada para funcionar como um publicador e/ou um distribuidor exige:

- que, no momento, a instância gerenciada não esteja participando de uma relação de replicação geográfica.
- Que a instância gerenciada publicador está na mesma rede virtual como o distribuidor e o assinante, ou [emparelhamento vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) foi estabelecida entre as redes virtuais de todas as três entidades. 
- A conectividade usa Autenticação SQL entre os participantes da replicação.
- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho de replicação.
- Porta 445 (TCP de saída) esteja aberta nas regras de segurança de NSG para as instâncias gerenciadas para acessar o compartilhamento de arquivos do Azure. 


 > [!NOTE]
 > Banco de dados individuais e em pool no Banco de Dados SQL do Azure só podem ser assinantes. 


## <a name="features"></a>Recursos

Suporta:

- Combinação de replicação transacional e de instantâneo das instâncias gerenciadas e locais do SQL Server no Banco de Dados SQL do Azure.
- Os assinantes podem estar em bancos de dados do SQL Server local, as instâncias de bancos de dados único/gerenciados no banco de dados SQL do Azure ou bancos de dados em pool em pools Elásticos do banco de dados SQL.
- Replicação unidirecional ou bidirecional.

Não há suporte para os seguintes recursos em uma instância gerenciada no Banco de Dados SQL do Azure:

- [Assinaturas atualizáveis](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Replicação geográfica ativa](sql-database-active-geo-replication.md) e [grupos de failover automático](sql-database-auto-failover-group.md) não deve ser usado se a replicação transacional estiver configurada.
 
## <a name="1---create-a-resource-group"></a>1 - criar um grupo de recursos

Use o [portal do Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - criar instâncias gerenciadas

Use o [portal do Azure](https://portal.azure.com) para criar dois [instâncias gerenciadas](sql-database-managed-instance-create-tutorial-portal.md) na mesma rede virtual e sub-rede. As duas instâncias gerenciadas devem ser nomeadas:

- `sql-mi-pub`
- `sql-mi-sub`

Você também precisará [configurar uma VM do Azure para conectar-se](sql-database-managed-instance-configure-vm.md) instâncias gerenciadas do seu banco de dados do SQL Azure. 

## <a name="3---create-azure-storage-account"></a>3 - Criar conta de armazenamento do Azure

[Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copie o caminho de compartilhamento de arquivos no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Copie as chaves de acesso de armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Para saber mais, confira [Exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 - criar um banco de dados do publicador

Conectar-se ao seu `sql-mi-pub` gerenciado instância usando o SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar seu banco de dados publicador:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - criar um banco de dados do assinante

Conectar-se ao seu `sql-mi-sub` gerenciado instância usando o SQL Server Management Studio e execute o seguinte código T-SQL para criar seu banco de dados do assinante vazio:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - configurar a distribuição

Conectar-se ao seu `sql-mi-pub` gerenciado instância usando o SQL Server Management Studio e execute o seguinte código T-SQL para configurar seu banco de dados de distribuição. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - configurar o Editor para usar o distribuidor 

No Editor de instância gerenciada `sql-mi-pub`, altere a execução de consultas [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) modo e execute o código a seguir para registrar o distribuidor nova com o publicador. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

Esse script configura um publicador local na instância gerenciada, adiciona um servidor vinculado e cria um conjunto de trabalhos do SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - criar a publicação e o assinante

Usando o [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) modo, execute o seguinte script T-SQL para habilitar a replicação do banco de dados e configurar a replicação entre o publicador, distribuidor e assinante. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - modificar parâmetros de agente

Instância gerenciada de banco de dados SQL do Azure atualmente está apresentando alguns problemas de back-end com conectividade com os agentes de replicação. Embora esse problema é que está sendo abordado resolvido, a solução alternativa para aumentar o valor de tempo limite de logon para os agentes de replicação. 

Execute o seguinte comando T-SQL no Editor para aumentar o tempo limite de logon: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute o seguinte comando T-SQL novamente para definir o tempo limite de logon para o valor padrão, deve você precisa fazer isso:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie todos os três agentes para aplicar essas alterações. 

## <a name="10---test-replication"></a>10 - testar a replicação

Depois que a replicação tiver sido configurada, você pode testá-lo inserindo novos itens no publicador e observando as alterações propagadas ao assinante. 

Execute o seguinte trecho de T-SQL para exibir as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Execute o seguinte trecho de T-SQL para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no assinante. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

Para descartar a publicação, execute o seguinte comando T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação do banco de dados, execute o seguinte comando T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desabilitar publicação e distribuição, execute o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Você pode limpar todos os recursos do Azure por [excluindo os recursos de instância gerenciada do grupo de recursos](../azure-resource-manager/manage-resources-portal.md#delete-resources) e, em seguida, excluir o grupo de recursos `SQLMI-Repl`. 

   
## <a name="see-also"></a>Veja também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [O que é uma Instância Gerenciada?](sql-database-managed-instance.md)
