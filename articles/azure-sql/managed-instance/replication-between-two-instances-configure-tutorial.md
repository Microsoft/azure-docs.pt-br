---
title: Configurar a replicação entre instâncias gerenciadas
titleSuffix: Azure SQL Managed Instance
description: Este tutorial ensina você a configurar a replicação transacional entre um publicador/distribuidor da Instância Gerenciada de SQL do Azure e um assinante da Instância Gerenciada de SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690678"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Tutorial: Configurar a replicação entre duas instâncias gerenciadas

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite replicar dados de um banco de dados para outro hospedado no SQL Server ou na [Instância Gerenciada de SQL do Azure](sql-managed-instance-paas-overview.md). A Instância Gerenciada de SQL pode ser um publicador, um distribuidor ou um assinante na topologia de replicação. Confira [configurações de replicação transacional](replication-transactional-overview.md#common-configurations) para obter as configurações disponíveis. 

A replicação transacional está atualmente em versão prévia pública para a Instância Gerenciada de SQL. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Configurar uma instância gerenciada como um publicador e um distribuidor de replicação.
> - Configurar uma instância gerenciada como um assistente de replicação.

![Fazer a replicação entre duas instâncias gerenciadas](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Este tutorial destina-se a um público-alvo experiente e pressupõe que o usuário esteja familiarizado com a implantação e a conexão com as instâncias gerenciadas e as VMs do SQL Server no Azure. 


> [!NOTE]
> - Este artigo descreve o uso de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) na Instância Gerenciada de SQL do Azure. Ele não está relacionado a [grupos de failover](../database/auto-failover-group-overview.md), um recurso de Instância Gerenciada de SQL do Azure que permite criar réplicas legíveis completas de instâncias individuais. Há considerações adicionais ao configurar [replicação transacional com grupos de failover](replication-transactional-overview.md#with-failover-groups).



## <a name="requirements"></a>Requisitos

A configuração da Instância Gerenciada de SQL para funcionar como um publicador e/ou um distribuidor exige:

- A instância gerenciada pelo publicador deverá estar na mesma rede virtual do distribuidor e do assinante ou os [gateways de VPN](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) foram configurados entre as redes virtuais das três entidades. 
- A conectividade usa Autenticação SQL entre os participantes da replicação.
- Um compartilhamento da conta de armazenamento do Azure para o diretório de trabalho de replicação.
- A porta 445 (TCP de saída) está aberta nas regras de segurança do NSG para que as instâncias gerenciadas acessem o compartilhamento de arquivo do Azure.  Se você encontrar o erro `failed to connect to azure storage <storage account name> with os error 53`, precisará adicionar uma regra de saída ao NSG da sub-rede apropriada da Instância Gerenciada de SQL.

## <a name="1---create-a-resource-group"></a>1– Criar um grupo de recursos

Use o [portal do Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 – Criar instâncias gerenciadas

Use o [portal do Azure](https://portal.azure.com) para criar duas [Instâncias Gerenciadas de SQL](instance-create-quickstart.md) na mesma rede virtual e sub-rede. Por exemplo, nomeie as duas instâncias gerenciadas:

- `sql-mi-pub` (junto com alguns caracteres para aleatoriedade)
- `sql-mi-sub` (junto com alguns caracteres para aleatoriedade)

Você também precisará [configurar uma VM do Azure para se conectar](connect-vm-instance-configure.md) às instâncias gerenciadas. 

## <a name="3---create-an-azure-storage-account"></a>3 – Criar uma conta de armazenamento do Azure

[Crie uma conta de armazenamento do Azure](../../storage/common/storage-account-create.md#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivo](../../storage/files/storage-how-to-create-file-share.md) na conta de armazenamento. 

Copie o caminho do compartilhamento de arquivo no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie as chaves de acesso de armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 – Criar um banco de dados publicador

Conecte-se à instância gerenciada `sql-mi-pub` usando o SQL Server Management Studio e execute o seguinte código T-SQL (Transact-SQL) para criar o banco de dados publicador:

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

## <a name="5---create-a-subscriber-database"></a>5 – Criar um banco de dados do assinante

Conecte-se à instância gerenciada `sql-mi-sub` usando o SQL Server Management Studio e execute o seguinte código T-SQL para criar o banco de dados do assinante vazio:

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

## <a name="6---configure-distribution"></a>6 – Configurar a distribuição

Conecte-se à instância gerenciada `sql-mi-pub` usando o SQL Server Management Studio e execute o código T-SQL a seguir para configurar o banco de dados de distribuição.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – Configurar o publicador para usar o distribuidor

Na Instância Gerenciada de SQL do publicador `sql-mi-pub`, altere a execução da consulta para o modo [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o código a seguir para registrar o novo distribuidor no publicador.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

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

   > [!NOTE]
   > Lembre-se de usar apenas barras invertidas (`\`) para o parâmetro file_storage. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivo.

Esse script configura um publicador local na instância gerenciada, adiciona um servidor vinculado e cria um conjunto de trabalhos para o agente do SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8 – Criar a publicação e o assinante

Usando o modo [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor), execute o script T-SQL a seguir para habilitar a replicação no banco de dados e configurar a replicação entre o publicador, o distribuidor e o assinante.

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


-- Configure your log reader agent
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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – Modificar parâmetros do agente

No momento, a Instância Gerenciada de SQL do Azure apresenta alguns problemas de back-end na conectividade com os agentes de replicação. Enquanto esse problema está sendo resolvido, a solução alternativa é aumentar o valor de tempo limite de logon dos agentes de replicação.

Execute o seguinte comando T-SQL no publicador para aumentar o tempo limite do logon:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute o seguinte comando T-SQL novamente para definir o tempo limite de logon novamente para o valor padrão, caso você precise fazer isso:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie todos os três agentes para aplicar essas alterações.

## <a name="10---test-replication"></a>10 – Testar a replicação

Depois que a replicação for configurada, teste-a inserindo novos itens no publicador e observando as alterações se propagarem para o assinante.

Execute o seguinte snippet T-SQL para ver as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Execute o snippet T-SQL a seguir para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no assinante.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a publicação, execute o seguinte comando T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação por meio do banco de dados, execute o seguinte comando T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desabilitar a publicação e a distribuição, execute o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Limpe os recursos do Azure [excluindo os recursos da Instância Gerenciada de SQL do grupo de recursos](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e excluindo o grupo de recursos `SQLMI-Repl`. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais também sobre a [replicação transacional com a Instância Gerenciada de SQL do Azure](replication-transactional-overview.md) ou aprenda a configurar a replicação entre uma [Instância Gerenciada de SQL do publicador/distribuidor e um SQL no assinante da VM do Azure](replication-two-instances-and-sql-server-configure-tutorial.md).