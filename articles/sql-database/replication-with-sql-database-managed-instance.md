---
title: Configurar a replicação em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
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
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646751"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure

A replicação transacional permite que você replique dados em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure de um banco de dados do SQL Server ou de outro banco de dados de instância. Também é possível usar a replicação transacional para efetuar push de alterações feitas em um banco de dados de instância na instância gerenciada do Banco de Dados SQL do Azure para um banco de dados do SQL Server, para um banco de dados individual no Banco de Dados SQL do Azure, para um banco de dados em pool em um pool elástico do Banco de Dados SQL do Azure. A replicação transacional está em versão prévia pública na [instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Uma instância gerenciada pode hospedar bancos de dados publicadores, distribuidores e assinantes. Confira [configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para obter as configurações disponíveis.

## <a name="requirements"></a>Requisitos

A configuração de uma instância gerenciada para funcionar como publicador ou distribuidor requer:

- que, no momento, a instância gerenciada não esteja participando de uma relação de replicação geográfica.

   >[!NOTE]
   >Banco de dados individuais e em pool no Banco de Dados SQL do Azure só podem ser assinantes.

- Todas as instâncias gerenciadas devem estar na mesma vNet.

- A conectividade usa Autenticação SQL entre os participantes da replicação.

- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho de replicação.

- A porta 445 (TCP de saída) precisa ser aberta nas regras de segurança de sub-rede da instância gerenciada para acessar o compartilhamento de arquivo do Azure

## <a name="features"></a>Recursos

Suporta:

- Combinação de replicação transacional e de instantâneo das instâncias gerenciadas e locais do SQL Server no Banco de Dados SQL do Azure.
- Os assinantes podem estar em bancos de dados do SQL Server local, as instâncias de bancos de dados único/gerenciados no banco de dados SQL do Azure ou bancos de dados em pool em pools Elásticos do banco de dados SQL.
- Replicação unidirecional ou bidirecional.

Não há suporte para os seguintes recursos em uma instância gerenciada no Banco de Dados SQL do Azure:

- Assinaturas atualizáveis.
- [Replicação geográfica ativa](sql-database-active-geo-replication.md) e [grupos de failover automático](sql-database-auto-failover-group.md) não deve ser usado se a replicação transacional estiver configurada.

## <a name="configure-publishing-and-distribution-example"></a>Exemplo de configuração de publicação e distribuição

1. [Crie uma instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-tutorial-portal.md) no portal.
2. [Crie uma Conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho.

   Certifique-se de copiar as chaves de armazenamento. Consulte [Exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys
).
3. Crie um banco de dados de instância para o publicador.

   Nos scripts de exemplo abaixo, substitua `<Publishing_DB>` pelo nome do banco de dados de instância.

4. Crie um usuário do banco de dados com Autenticação SQL para o distribuidor. Use uma senha segura.

   Nos scripts de exemplo abaixo, use `<SQL_USER>` e `<PASSWORD>` com o usuário e a senha do banco de dados dessa Conta do SQL Server.

5. [Conecte-se à Instância Gerenciada do Banco de Dados SQL](sql-database-connect-query-ssms.md).

6. Execute a seguinte consulta para adicionar o distribuidor e o banco de dados de distribuição.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Para configurar um publicador para usar um banco de dados de distribuição especificado, atualize e execute a consulta a seguir.

   Substitua `<SQL_USER>` e `<PASSWORD>` pela Conta do SQL Server e pela senha.

   Substitua `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` pelo valor de sua conta de armazenamento.  

   Substitua `<STORAGE_CONNECTION_STRING>` pela cadeia de conexão na guia **Chaves de acesso** de sua conta de Armazenamento do Microsoft Azure.

   Após atualizar a consulta a seguir, execute-a.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Configure o publicador para replicação.

    Na consulta a seguir, substitua `<Publishing_DB>` pelo nome do banco de dados publicador.

    Substitua `<Publication_Name>` pelo nome de sua publicação.

    Substitua `<SQL_USER>` e `<PASSWORD>` pela Conta do SQL Server e pela senha.

    Após atualizar a consulta, execute-a para criar a publicação.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Adicione o artigo, a assinatura e o agente da assinatura push.

   Para adicionar esses objetos, atualize o script a seguir.

   - Substitua `<Object_Name>` pelo nome do objeto da publicação.
   - Substitua `<Object_Schema>` pelo nome do esquema de origem.
   - Substitua os outros parâmetros entre colchetes angulares `<>` para corresponder aos valores nos scripts anteriores.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>Veja também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [O que é uma Instância Gerenciada?](sql-database-managed-instance.md)
