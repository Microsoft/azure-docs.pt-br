---
title: Concedendo acesso ao Banco de dados SQL Azure | Microsoft Docs
description: Saiba mais sobre como conceder acesso ao Banco de Dados SQL do Microsoft Azure.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/06/2017
ms.author: rickbyh
ms.openlocfilehash: 0ca1ccd273317d67537d31724d566625a4eb2c85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-access-control"></a>Controle de acesso do Banco de Dados SQL do Azure
Para fornecer segurança, o Banco de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP,mecanismos de autenticação que exigem que usuários comprovem suas identidade e mecanismos de autorização que limitam os usuários a ações e dados específicos. 

> [!IMPORTANT]
> Para obter uma visão geral dos recursos de segurança do Banco de Dados SQL, veja [visão geral de segurança do SQL](sql-database-security-overview.md). Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).

## <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall
O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação. Para saber mais, veja [Visão geral de regras de firewall do Banco de Dados SQL](sql-database-firewall-configure.md)

O serviço de Banco de dados SQL do Azure só está disponível por meio da porta TCP 1433. Para acessar um Banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Se não for necessário a outros aplicativos, bloqueie as conexões de entrada na porta TCP 1433. 

Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e uma porta diferentes, exclusivos para cada função de trabalho. O número da porta está no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, consulte [Portas além da 1433 para o ADO.NET 4.5 e o Banco de Dados SQL 2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autenticação

O Banco de Dados SQL dá suporte a dois tipos de autenticação:

* **Autenticação do SQL**, que usa um nome de usuário e senha. Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo". 
* **Autenticação do Azure Active Directory**, que usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode). Se desejar usar a Autenticação do Active Directory do Azure, você deve criar outro administrador de servidor chamado “admin do AD do Azure”, que tenha permissão para administrar usuários e grupos do AD do Azure. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum. Veja [Conectando-se ao Banco de Dados SQL usando a Autenticação do Active Directory do Azure](sql-database-aad-authentication.md) para ver um passo a passo de como criar um administrador do Azure AD para habilitar a Autenticação do Active Directory do Azure.

O Mecanismo do Banco de dados fecha conexões permanecem ociosas por mais de 30 minutos. A conexão deve efetuar logon novamente para que possa ser usada. As conexões ativas continuamente para o banco de dados SQL requerem nova autorização (realizada pelo mecanismo de banco de dados) pelo menos a cada 10 horas. O mecanismo de banco de dados tenta uma nova autorização usando a senha enviada originalmente e não é necessária nenhuma entrada do usuário. Por motivos de desempenho, quando uma senha for redefinida no banco de dados SQL, a conexão não será autenticada novamente, mesmo se a conexão for redefinida devido ao pooling de conexão. Isso é diferente do comportamento do SQL Server no local. Se a senha foi alterada desde que a conexão foi autorizada inicialmente, a conexão deverá ser encerrada e uma nova conexão deverá ser feita usando a nova senha. Um usuário com a permissão `KILL DATABASE CONNECTION` pode terminar explicitamente uma conexão ao Banco de dados SQL usando o comando [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

As contas de usuário podem ser criadas no banco de dados mestre e podem ser concedidas permissões em todos os bancos de dados no servidor, ou eles podem ser criados no próprio banco de dados (chamados de usuários independentes). Para saber mais sobre a criação e o gerenciamento de logons, veja [Gerenciar logons](sql-database-manage-logins.md). Para aumentar a portabilidade e a escalabilidade, use os usuários do banco de dados independentes para melhorar a escalabilidade. Para saber mais sobre usuários independentes, veja [Usuários de bancos de dados independentes – tornando seu banco de dados portátil](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) e [Bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Como uma prática recomendada, seu aplicativo deve usar uma conta dedicada para autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. A abordagem recomendada é criar um [usuário de banco de dados independente](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), o que permite que o seu aplicativo seja autenticado diretamente no banco de dados individual. 

## <a name="authorization"></a>Autorização

A autorização refere-se ao que você pode fazer em um Banco de Dados SQL do Azure, e isso é controlado pelas [associações de função](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) e [permissões no nível do objeto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo. Para saber mais, veja [Gerenciar logons](sql-database-manage-logins.md).

Normalmente, somente os administradores precisam acessar o banco de dados `master`. O acesso de rotina a cada banco de dados do usuário deve ser por meio de usuários de banco de dados independente não administradores criados em cada banco de dados. Quando você usar os usuários de banco de dados independente, não será necessário criar logons no banco de dados `master`. Para obter mais informações, consulte [Usuários do banco de dados independente - Tornando o banco de dados portátil](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Você deve estar familiarizado com os seguintes recursos que podem ser usados para limitar ou elevar as permissões:   
* [Representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser usadas para elevar permissões temporariamente com segurança.
* [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser usado para limitar quais linhas de um usuário pode acessar.
* [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
* [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos recursos de segurança do Banco de Dados SQL, veja [visão geral de segurança do SQL](sql-database-security-overview.md).
- Para saber mais sobre regras de firewall, consulte [Regras de firewall](sql-database-firewall-configure.md).
- Para saber mais sobre logons e usuários, veja [Gerenciar logons](sql-database-manage-logins.md). 
- Para obter uma discussão sobre monitoramento proativo, confira [Auditoria do Banco de Dados](sql-database-auditing.md) e [Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection.md).
- Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).
