---
title: Proteger um pool SQL dedicado (anteriormente conhecido como SQL DW)
description: Dicas para proteger um pool SQL dedicado (anteriormente conhecido como SQL DW) e desenvolver soluções no Azure Synapse Analytics.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 7e2d4b47f8f37a8e6d69a7846b8b0f92247121da
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685184"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Proteger um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo explicará as noções básicas de como proteger seu pool SQL dedicado (anteriormente conhecido como SQL DW). Em particular, este artigo apresenta recursos para limitar o acesso, proteger dados e monitorar atividades usando o pool dedicado do SQL (anteriormente conhecido como SQL DW).

## <a name="connection-security"></a>Segurança da conexão

A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo [SQL Server lógico](../../azure-sql/database/logical-servers.md) e seus bancos de dados para rejeitar tentativas de conexão de endereços IP que não foram explicitamente aprovados. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o portal do Azure, a API REST ou o PowerShell.

Como prática recomendada, você deve restringir os intervalos de endereços IP permitidos por meio do firewall de nível de servidor o máximo possível.  Para acessar seu pool SQL dedicado (anteriormente conhecido como SQL DW) do seu computador local, verifique se o firewall na rede e no computador local permite a comunicação de saída na porta TCP 1433.  

O pool de SQL dedicado (anteriormente conhecido como SQL DW) usa regras de firewall de IP no nível de servidor. Ele não dá suporte a regras de firewall de IP no nível de banco de dados. Para obter mais informações, consulte [regras de firewall do banco de dados SQL do Azure](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

As conexões com seu pool SQL dedicado (anteriormente conhecido como SQL DW) são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Autenticação

A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O pool de SQL dedicado (anteriormente conhecido como SQL DW) atualmente dá suporte à autenticação SQL Server com um nome de usuário e senha e com Azure Active Directory.

Quando você criou o servidor para seu banco de dados, você especificou um logon de "administrador do servidor" com um nome de usuário e senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo", por meio da Autenticação do SQL Server.

No entanto, como prática recomendada, os usuários da sua organização devem usar uma conta diferente para se autenticar. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL.

Para criar um usuário Autenticado do SQL Server, conecte o banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon do servidor.  É uma boa ideia também criar um usuário no banco de dados mestre. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ele também permite que eles usem o pesquisador de objetos para exibir todos os bancos de dados em um servidor.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte-se ao seu **pool SQL dedicado (anteriormente conhecido como SQL DW)** com o logon de administrador do servidor e crie um usuário de banco de dados com base no logon do servidor que você criou.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder um permissão de usuário para realizar operações adicionais, como criar logons ou novos bancos de dados, atribua o usuário às funções `Loginmanager` e `dbmanager` no banco de dados mestre.

Para obter mais informações sobre essas funções adicionais e como fazer a autenticação em um Banco de Dados SQL, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Para obter mais informações sobre como se conectar usando Azure Active Directory, consulte [conectando usando a autenticação do Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorização

A autorização refere-se ao que você pode fazer em um banco de dados quando você está autenticado e conectado. Privilégios de autorização são determinados pelas permissões e associações de função. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Para gerenciar funções, você pode usar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Há maneiras de limitar ainda mais o que um usuário pode fazer no banco de dados:

* [Permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, esquemas, procedimentos e outros objetos individuais no banco de dados. Use permissões granulares para ter maior controle e conceder as permissões mínimas necessárias.
* As [funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) que não sejam db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais poderosas ou contas de gerenciamento menos poderosas. As funções internas de banco de dados fixo fornecem uma maneira fácil para conceder permissões, mas podem resultar na concessão de mais permissões do que o necessário.
* [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo usuário.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

O gerenciamento de bancos de dados e servidores do portal do Azure ou do uso da API Azure Resource Manager é controlado pelas atribuições de função da sua conta de usuário do Portal. Para obter mais informações, confira [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Criptografia

A Transparent Data Encryption (TDE) ajuda a proteger contra a ameaça de atividades mal-intencionadas Criptografando e descriptografando seus dados em repouso. Quando você criptografa seus banco de dados, os arquivos de log de transações e backups associados são criptografados sem exigir nenhuma alteração em seus aplicativos. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados.

No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado é o AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Você pode criptografar o banco de dados usando o [portal do Azure](sql-data-warehouse-encryption-tde.md) ou o [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes e exemplos sobre como se conectar ao seu warehouse com protocolos diferentes, consulte [conectar-se ao pool dedicado do SQL (anteriormente conhecido como SQL DW)](sql-data-warehouse-connect-overview.md).
