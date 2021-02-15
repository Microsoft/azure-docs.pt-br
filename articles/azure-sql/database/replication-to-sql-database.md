---
title: Replicação do Azure SQL Server para o banco de dados SQL do Azure
description: Você pode configurar um banco de dados no banco de dados SQL do Azure como o Assinante de push em uma topologia de replicação transacional ou de instantâneo unidirecional.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780947"
---
# <a name="replication-to-azure-sql-database"></a>Replicação no banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Você pode configurar um banco de dados SQL do Azure como o Assinante de push em uma topologia de replicação transacional ou de instantâneo unidirecional.

> [!NOTE]
> Este artigo descreve o uso da [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) no banco de dados SQL do Azure. Ele não está relacionado à [replicação geográfica ativa](./active-geo-replication-overview.md), um recurso de banco de dados SQL do Azure que permite que você crie réplicas legíveis completas de bancos de dados individuais.

## <a name="supported-configurations"></a>Configurações com suporte
  
- O banco de dados SQL do Azure só pode ser o Assinante de push de um SQL Server Publicador e distribuidor.  
- A instância de SQL Server atuando como Publicador e/ou distribuidor pode ser uma instância do [SQL Server em execução local](https://www.microsoft.com/sql-server/sql-server-downloads), um [instância gerenciada SQL do Azure](../managed-instance/instance-create-quickstart.md)ou uma instância do [SQL Server em execução em uma máquina virtual do Azure na nuvem](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- O banco de dados de distribuição e os agentes de replicação não podem ser colocados em um banco de dados no banco de dados SQL do Azure.  
- Há suporte para o [instantâneo](/sql/relational-databases/replication/snapshot-replication) e [a replicação transacional unidirecional](/sql/relational-databases/replication/transactional/transactional-replication) . Não há suporte para replicação transacional ponto a ponto nem e replicação de mesclagem.

### <a name="versions"></a>Versões  

Para replicar com êxito para um banco de dados no banco de dados SQL do Azure, SQL Server editores e distribuidores devem estar usando (pelo menos) uma das seguintes versões:

A publicação em qualquer banco de dados SQL do Azure de um banco de dados SQL Server tem suporte nas seguintes versões do SQL Server:

- SQL Server 2016 e superior
- SQL Server 2014 [RTM cu10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> A tentativa de configurar a replicação usando uma versão sem suporte pode resultar em um número de erro MSSQL_REPL20084 (o processo não pôde se conectar ao Assinante.) e MSSQL_REPL40532 (não é possível abrir o servidor \<name> solicitado pelo logon. Houve falha no logon.).  

Para usar todos os recursos do Banco de Dados SQL do Azure, você deve estar usando as versões mais recentes do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e do [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Tipos de replicação

Existem diferentes [tipos de replicação](/sql/relational-databases/replication/types-of-replication):

| Replicação | Banco de Dados SQL do Azure | Instância Gerenciada do Azure SQL |
| :----| :------------- | :--------------- |
| [**Transacional padrão**](/sql/relational-databases/replication/transactional/transactional-replication) | Sim (somente como assinante) | Sim | 
| [**Instantâneo**](/sql/relational-databases/replication/snapshot-replication) | Sim (somente como assinante) | Sim|
| [**Replicação de mesclagem**](/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Assinaturas atualizáveis**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Comentários

- Há suporte apenas para assinaturas push no Banco de Dados SQL do Azure.  
- Replicação pode ser configurada usando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou executando instruções Transact-SQL no editor. Você não pode configurar a replicação usando o portal do Azure.  
- A replicação só pode usar SQL Server logons de autenticação para se conectar ao banco de dados SQL do Azure.
- Tabelas replicadas devem ter uma chave primária.  
- Você deve ter uma assinatura do Azure existente.  
- O assinante do banco de dados SQL do Azure pode estar em qualquer região.  
- Uma única publicação no SQL Server pode dar suporte a assinantes do Banco de Dados SQL do Azure e do SQL Server (local e SQL Server em uma máquina virtual do Azure).  
- O gerenciamento de replicação, o monitoramento e a solução de problemas devem ser realizados a partir de SQL Server em vez do banco de dados SQL do Azure.  
- Somente `@subscriber_type = 0` tem suporte no **sp_addsubscription** para Banco de Dados SQL.  
- O banco de dados SQL do Azure não oferece suporte a replicação bidirecional, imediata, atualizável ou ponto a ponto.

## <a name="replication-architecture"></a>Arquitetura de replicação  

![O diagrama mostra a arquitetura de replicação com o banco de dados SQL do Azure, que contém vários clusters de assinantes em regiões diferentes e máquinas virtuais do Azure locais, que contêm um Publicador, um executável logread e executáveis do distribuidor que se conectam a clusters remotos.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário típico de replicação  

1. Crie uma publicação de replicação transacional em um banco de dados SQL Server.  
2. Em SQL Server use o **Assistente para nova assinatura** ou instruções TRANSACT-SQL para criar um push para assinatura no banco de dados SQL do Azure.  
3. Com bancos de dados individuais e em pool no Banco de Dados SQL do Azure, o conjunto de dados inicial é um instantâneo criado pelo Agente de Instantâneo e distribuído e aplicado pelo Agente de Distribuição. Com um Publicador do SQL Instância Gerenciada, você também pode usar um backup de banco de dados para propagar o assinante do banco de dados SQL do Azure.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Use a replicação transacional para replicar dados de um SQL Server um banco de dado para o Azure SQL Database.  
2. Redirecione os aplicativos cliente ou de camada intermediária para atualizar a cópia do banco de dados.  
3. Interrompa a atualização da versão do SQL Server da tabela e remova a publicação.  

## <a name="limitations"></a>Limitações

As opções a seguir não têm suporte para assinaturas de Banco de Dados SQL do Azure:

- Copiar associação de grupos de arquivos  
- Copiar esquemas de particionamento de tabela  
- Copiar esquemas de particionamento de índice  
- Copiar estatísticas definidas pelo usuário  
- Copiar associações padrão  
- Copiar associações de regra  
- Copiar índices de texto completo  
- Copiar XML XSD  
- Copiar índices XML  
- Permissões de cópia  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Copiar atributo de compactação de dados  
- Copiar atributo de coluna esparsa  
- Converter fluxo de arquivos em tipos de dados MAX  
- Converter hierarchyid em tipos de dados MAX  
- Converter espacial em tipos de dados MAX  
- Copiar propriedades estendidas  

### <a name="limitations-to-be-determined"></a>Limitações a serem determinadas

- Copiar ordenação  
- Execução em uma transação serializável do SP  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma assinatura push. Para obter mais informações, consulte:
  
- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma assinatura push](/sql/relational-databases/replication/create-a-push-subscription/) usando o nome do servidor como o assinante (por exemplo **n' azuresqldbdns. Database. Windows. net '** ) e o nome do banco de dados SQL do Azure como o banco de dados de destino (por exemplo, **AdventureWorks** ).  

## <a name="see-also"></a>Consulte Também  

- [Replicação transacional](../managed-instance/replication-transactional-overview.md)
- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma Assinatura Push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de Replicação](/sql/relational-databases/replication/types-of-replication)
- [Monitoramento (Replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Assinatura](/sql/relational-databases/replication/initialize-a-subscription)