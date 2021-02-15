---
title: Replicação transacional
titleSuffix: Azure SQL Managed Instance
description: Saiba como usar SQL Server replicação transacional com o SQL Instância Gerenciada do Azure (versão prévia).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225336"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Replicação transacional com o Azure SQL Instância Gerenciada (versão prévia)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional é um recurso do Azure SQL Instância Gerenciada e SQL Server que permite replicar dados de uma tabela no Instância Gerenciada SQL do Azure ou uma instância de SQL Server para tabelas colocadas em bancos de dados remotos. Esse recurso permite que você sincronize várias tabelas em bancos de dados diferentes. 

A replicação transacional está atualmente em versão prévia pública para a Instância Gerenciada de SQL. 

## <a name="overview"></a>Visão geral

Você pode usar a replicação transacional para enviar por push as alterações feitas em um Instância Gerenciada do SQL do Azure para:

- Um banco de dados SQL Server-local ou na VM do Azure
- Um banco de dados no banco de dados SQL do Azure
- Um banco de dados de instância no Azure SQL Instância Gerenciada

  > [!NOTE]
  > Para usar todos os recursos do Azure SQL Instância Gerenciada, você deve usar as versões mais recentes do [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Componentes

Os principais componentes na replicação transacional são o **Publicador**, o **distribuidor** e o **assinante**, conforme mostrado na figura a seguir:  

![replicação com um Banco de Dados SQL](./media/replication-transactional-overview/replication-to-sql-database.png)

| Função | Banco de Dados SQL do Azure | Instância Gerenciada de SQL do Azure |
| :----| :------------- | :--------------- |
| **Publicador** | Não | Sim |
| **Distribuidor** | Não | Sim|
| **Assinante de pull** | Não | Sim|
| **Assinante push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

O **Publicador** publica as alterações feitas em algumas tabelas (artigos) enviando as atualizações para o distribuidor. O Publicador pode ser um Instância Gerenciada de SQL do Azure ou uma instância de SQL Server.

O **distribuidor** coleta alterações nos artigos de um Publicador e os distribui aos assinantes. O distribuidor pode ser um Instância Gerenciada do Azure SQL ou uma instância de SQL Server (qualquer versão, desde que seja igual ou superior à versão do editor).

O **assinante** recebe as alterações feitas no Publicador. Uma instância SQL Server e Azure SQL Instância Gerenciada podem ser assinantes push e pull, embora não haja suporte para uma assinatura pull quando o distribuidor é um Instância Gerenciada SQL do Azure e o Assinante não é. Um banco de dados no banco de dados SQL do Azure só pode ser um assinante Push.

O Azure SQL Instância Gerenciada pode dar suporte a um assinante das seguintes versões do SQL Server:

- SQL Server 2016 e posterior
- SQL Server 2014 [RTM cu10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Para outras versões do SQL Server que não dão suporte à publicação para objetos no Azure, é possível utilizar o método de [republicação de dados](/sql/relational-databases/replication/republish-data) para mover dados para versões mais recentes do SQL Server.
   > - Tentativa de configurar a replicação usando uma versão mais antiga pode resultar em erro número MSSQL_REPL20084 (o processo não pôde se conectar ao Assinante.) e MSSQ_REPL40532 (não é possível abrir o servidor \<name> solicitado pelo logon. Houve falha no logon).

### <a name="types-of-replication"></a>Tipos de replicação

Existem diferentes [tipos de replicação](/sql/relational-databases/replication/types-of-replication):

| Replicação | Banco de Dados SQL do Azure | Instância Gerenciada de SQL do Azure |
| :----| :------------- | :--------------- |
| [**Transacional padrão**](/sql/relational-databases/replication/transactional/transactional-replication) | Sim (somente como assinante) | Sim |
| [**Instantâneo**](/sql/relational-databases/replication/snapshot-replication) | Sim (somente como assinante) | Sim|
| [**Replicação de mesclagem**](/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Assinaturas atualizáveis**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Matriz de suporte

  A matriz de suporte de replicação transacional para o Azure SQL Instância Gerenciada é a mesma para SQL Server.
  
| **Publicador**   | **Distribuidor** | **Assinante** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| Microsoft SQL Server 2017 | SQL Server 2019 <br/>Microsoft SQL Server 2017 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>Microsoft SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Quando usar

A replicação transacional é útil nos seguintes cenários:

- Publique alterações feitas em uma ou mais tabelas em um banco de dados e distribua-as para um ou vários bancos de dados em uma instância SQL Server ou banco de dados SQL do Azure que assinou as alterações.
- Mantenha vários bancos de dados distribuídos em estado sincronizado.
- Migre bancos de dados de uma instância do SQL Server ou do SQL do Azure Instância Gerenciada para outro, publicando as alterações continuamente.

### <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| Categoria | Sincronização de Dados | Replicação transacional |
|---|---|---|
| Vantagens | – Suporte ativo-ativo<br/>– Bidirecional entre o Banco de Dados SQL do Azure e o local | – Menor latência<br/>– Consistência transacional<br/>– Reutilização da topologia existente após a migração |
| Desvantagens | – Não há consistência transacional<br/>– Maior impacto do desempenho | -Não é possível publicar do banco de dados SQL do Azure <br/>– Alto custo de manutenção |

## <a name="common-configurations"></a>Configurações comuns

De modo geral, o publicador e o distribuidor devem ambos estar na nuvem ou ser locais. Há suporte para as seguintes configurações:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Publicador com distribuidor local no SQL Instância Gerenciada

![Instância única como publicador e distribuidor](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

O Publicador e o distribuidor são configurados em um único Instância Gerenciada SQL e distribuim alterações para outro SQL Instância Gerenciada, banco de dados SQL ou instância de SQL Server.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Publicador com distribuidor remoto no SQL Instância Gerenciada

Nessa configuração, uma instância gerenciada publica as alterações em um distribuidor colocado em outro Instância Gerenciada do SQL que pode atender a muitas instâncias gerenciadas do SQL de origem e distribuir as alterações para um ou vários destinos no banco de dados SQL do Azure, SQL Instância Gerenciada do Azure ou SQL Server.

![Instâncias separadas para publicador e distribuidor](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

O publicador e o distribuidor são configurados em duas instâncias gerenciadas. Há algumas restrições com essa configuração:

- Ambas as instâncias gerenciadas estão na mesma vNet.
- As duas instâncias gerenciadas estão no mesmo local.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Publicador/distribuidor local com assinante remoto

![Banco de dados SQL do Azure como assinante](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Nessa configuração, um banco de dados no banco de dados SQL do Azure ou Azure SQL Instância Gerenciada é um assinante. Essa configuração dá suporte à migração do local para o Azure. Se um assinante for um banco de dados no banco de dados SQL do Azure, ele deverá estar no modo de push.  

## <a name="requirements"></a>Requisitos

- Use a autenticação do SQL para conectividade entre os participantes da replicação.
- Use um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho usado pela replicação.
- Abra a porta de saída TCP 445 nas regras de segurança de sub-rede para acessar o compartilhamento de arquivos do Azure.
- Abra a porta de saída TCP 1433 quando o Instância Gerenciada do SQL for o Publicador/distribuidor e o Assinante não for. Talvez você também precise alterar a regra de segurança de saída do SQL Instância Gerenciada NSG para `allow_linkedserver_outbound` a **marca de serviço de destino** da porta 1433 de `virtualnetwork` para `internet` .
- Coloque o Publicador e o distribuidor na nuvem ou ambos no local.
- Configure o emparelhamento VPN entre as redes virtuais dos participantes da replicação se as redes virtuais forem diferentes.

> [!NOTE]
> Você poderá encontrar o erro 53 ao se conectar a um arquivo de armazenamento do Azure se a porta 445 de saída do grupo de segurança de rede (NSG) for bloqueada quando o distribuidor for um banco de dados SQL Instância Gerenciada do Azure e o assinante for local. [Atualize a VNET NSG](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) para resolver esse problema.

## <a name="with-failover-groups"></a>Com grupos de failover

Não há suporte para a [replicação geográfica ativa](../database/active-geo-replication-overview.md) com um instância gerenciada SQL usando a replicação transacional. Em vez de replicação geográfica ativa, use [grupos de failover automático](../database/auto-failover-group-overview.md), mas observe que a publicação precisa ser [excluída manualmente](transact-sql-tsql-differences-sql-server.md#replication) da instância gerenciada primária e recriada na instância gerenciada do SQL secundário após o failover.

Se a replicação geográfica estiver habilitada em um **Publicador** ou SQL instância gerenciada de **distribuidor** em um [grupo de failover](../database/auto-failover-group-overview.md), o administrador do SQL instância gerenciada deverá limpar todas as publicações no antigo primário e reconfigurá-los no novo primário após a ocorrência de um failover. As seguintes atividades são necessárias neste cenário:

1. Pare todos os trabalhos de replicação em execução no banco de dados, se houver algum.
1. Remova os metadados da assinatura do Publicador executando o seguinte script no banco de dados do Publicador:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Remova os metadados da assinatura do Assinante. Execute o seguinte script no banco de dados de assinatura no SQL Instância Gerenciada do Assinante:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Force a remoção de todos os objetos de replicação do Publicador executando o seguinte script no banco de dados publicado:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Force a remoção do distribuidor antigo do Instância Gerenciada de SQL primário original (se estiver fazendo failover novamente para um antigo primário que costumava ter um distribuidor). Execute o seguinte script no banco de dados mestre em Instância Gerenciada do SQL do distribuidor antigo:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se a replicação geográfica estiver habilitada em uma instância do **assinante** em um grupo de failover, a publicação deverá ser configurada para se conectar ao ponto de extremidade do ouvinte do grupo de failover para a instância gerenciada do Assinante. No caso de um failover, a ação subsequente pelo administrador da instância gerenciada depende do tipo de failover ocorrido:

- Para um failover sem perda de dados, a replicação continuará funcionando após o failover.
- Para um failover com perda de dados, a replicação também funcionará. Ele replicará as alterações perdidas novamente.
- Para um failover com perda de dados, mas a perda de dados está fora do período de retenção do banco de dado de distribuição, o administrador do SQL Instância Gerenciada precisará reinicializar o banco de dados de assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como configurar a replicação transacional, consulte os seguintes tutoriais:

- [Configurar a replicação entre um Publicador do SQL Instância Gerenciada e um assinante](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Configurar a replicação entre um Publicador do SQL Instância Gerenciada, o distribuidor do SQL Instância Gerenciada e o assinante do SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Crie uma publicação](/sql/relational-databases/replication/publish/create-a-publication).
- [Crie uma assinatura push](/sql/relational-databases/replication/create-a-push-subscription) usando o nome do servidor como o assinante (por exemplo `N'azuresqldbdns.database.windows.net` , e o banco de dados no nome do banco de dados SQL do Azure como o banco de dados de destino (por exemplo, **AdventureWorks**. )

## <a name="see-also"></a>Confira também  

- [Replicação com um Instância Gerenciada SQL e um grupo de failover](transact-sql-tsql-differences-sql-server.md#replication)
- [Replicação para um Banco de Dados SQL](../database/replication-to-sql-database.md)
- [Replicação para instância gerenciada](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma Assinatura Push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de Replicação](/sql/relational-databases/replication/types-of-replication)
- [Monitoramento (Replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Assinatura](/sql/relational-databases/replication/initialize-a-subscription)
