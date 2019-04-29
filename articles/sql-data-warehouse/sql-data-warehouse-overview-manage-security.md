---
title: Proteger um banco de dados no SQL Data Warehouse | Microsoft Docs
description: Dicas para proteger um banco de dados no SQL Data Warehouse do Azure para desenvolvimento de soluções.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 179925fc7411a1ccf3de02d7b6298cc66f93bc66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126933"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger um banco de dados no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão Geral da Segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo apresenta os conceitos básicos da proteção do banco de dados do SQL Data Warehouse do Azure. Especificamente, este artigo apresenta a você recursos para limitar acesso, proteger dados e monitorar atividades em um banco de dados.

## <a name="connection-security"></a>Segurança da conexão
A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram incluídos explicitamente na lista de permissões. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o portal do Azure, a API REST ou o PowerShell. Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor.  Para acessar o SQL Data Warehouse do Azure de seu computador local, verifique se o firewall em sua rede e computador local permite a comunicação de saída na porta TCP 1433.  

SQL Data Warehouse usa regras de firewall de nível de servidor. Não oferece suporte a regras de firewall de nível de banco de dados. Para saber mais, confira [Firewall do Banco de Dados SQL do Azure][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

As conexões com o SQL Data Warehouse são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Authentication
A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. Atualmente, o SQL Data Warehouse dá suporte à Autenticação do SQL Server com um nome de usuário e senha, e com Azure Active Directory. 

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo", por meio da Autenticação do SQL Server.

No entanto, como uma melhor prática, os usuários de sua organização devem usar uma conta diferente para a autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. 

Para criar um usuário Autenticado do SQL Server, conecte o banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon do servidor.  Além disso, é uma boa ideia criar um usuário no banco de dados mestre para os usuários do Azure SQL Data Warehouse. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ela também permite que o usuário utilize o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte o **banco de dados do SQL Data Warehouse** com seu logon de administrador do servidor e crie um usuário do banco de dados com base no logon do servidor que você criou.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder um permissão de usuário para realizar operações adicionais, como criar logons ou novos bancos de dados, atribua o usuário às funções `Loginmanager` e `dbmanager` no banco de dados mestre. Para obter mais informações sobre essas funções adicionais e como fazer a autenticação em um Banco de Dados SQL, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure][Managing databases and logins in Azure SQL Database].  Para obter mais informações, consulte [Conectar-se ao SQL Data Warehouse usando a autenticação do Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorização
Autorização indica o que você pode fazer em um banco de dados do Azure SQL Data Warehouse. Privilégios de autorização são determinados pelas permissões e associações de função. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Para gerenciar funções, você pode usar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer com o SQL Data Warehouse do Azure:

* [Permissões][Permissions] granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, esquemas, procedimentos e outros objetos individuais no banco de dados. Use permissões granulares para ter maior controle e conceder as permissões mínimas necessárias. 
* [Funções de banco de dados][Database roles] diferentes de db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes. As funções internas de banco de dados fixo fornecem uma maneira fácil para conceder permissões, mas podem resultar na concessão de mais permissões do que o necessário.
* [Procedimentos armazenados][Stored procedures] podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo usuário.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

O gerenciamento de bancos de dados e de servidores lógicos pelo portal do Azure ou usando a API do Azure Resource Manager é controlado pelas atribuições de função da sua conta de usuário. Para saber mais, confira [Role-based access control in the Azure portal][Role-based access control in Azure portal](Controle de acesso baseado em função no portal do Azure).

## <a name="encryption"></a>Criptografia
A TDE (Transparent Data Encryption) do SQL Data Warehouse do Azure ajuda a proteger contra a ameaça de atividades mal-intencionadas por meio da execução de criptografia e descriptografia de seus dados em repouso.  Quando você criptografa seus banco de dados, os arquivos de log de transações e backups associados são criptografados sem exigir nenhuma alteração em seus aplicativos. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. 

No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Banco de Dados SQL. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é o AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption][Transparent Data Encryption].

Você pode criptografar o banco de dados usando o [portal do Azure][Encryption with Portal] ou o [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes e exemplos sobre como se conectar ao SQL Data Warehouse com protocolos diferentes, consulte [Conectar-se ao SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
