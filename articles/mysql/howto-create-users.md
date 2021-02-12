---
title: Criar bancos de dados e usuários-Azure para MySQL
description: Este artigo descreve como criar novas contas de usuário para interagir com um servidor de banco de dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251243"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Criar bancos de dados e usuários no Azure Database para MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo descreve como criar usuários no banco de dados do Azure para MySQL.

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

Ao criar pela primeira vez o banco de dados do Azure para o servidor MySQL, você forneceu um nome de usuário e uma senha de administrador do servidor. Para obter mais informações, consulte este guia de [início rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Você pode determinar o nome de usuário do administrador do servidor no portal do Azure.

O usuário administrador do servidor tem estes privilégios:

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Depois de criar um banco de dados do Azure para o servidor MySQL, você pode usar a primeira conta do administrador do servidor para criar mais usuários e conceder acesso de administrador a eles. Você também pode usar a conta de administrador do servidor para criar usuários com menos privilégios que tenham acesso a esquemas de banco de dados individuais.

> [!NOTE]
> Não há suporte para a função SUPER Privilege e DBA. Examine os [privilégios](concepts-limits.md#privileges--data-manipulation-support) no artigo limitações para entender o que não tem suporte no serviço.
>
> Plug-ins `validate_password` de senha como e `caching_sha2_password` não têm suporte do serviço.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Para criar um banco de dados com um usuário não administrador no banco de dados do Azure para MySQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** do portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como MySQL Workbench, mysql.exe ou HeidiSQL.

   Se você não tiver certeza de como se conectar, consulte [conectar e consultar dados para um único servidor](./connect-workbench.md) ou [conectar e consultar dados para um servidor flexível](./flexible-server/connect-workbench.md).

3. Edite e execute o código SQL a seguir. Substitua o valor do espaço reservado `db_user` pelo seu novo nome de usuário pretendido. Substitua o valor do espaço reservado `testdb` pelo nome do banco de dados.

   Esse código SQL cria um novo banco de dados chamado TestDB. Em seguida, ele cria um novo usuário no serviço MySQL e concede a todos os privilégios para o novo esquema de banco de dados (TestDB \* ) para esse usuário.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifique as concessões no banco de dados:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Entre no servidor, especificando o banco de dados designado e usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do mysql. Ao usar esse comando, a senha do usuário será solicitada. Use seu próprio nome de servidor, nome de banco de dados e nome de usuário.

   ### <a name="single-server"></a>[Servidor único](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[Servidor Flexível](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Para criar mais usuários administradores no banco de dados do Azure para MySQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** do portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como MySQL Workbench, mysql.exe ou HeidiSQL.

   Se você não tiver certeza de como se conectar, consulte [usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md).

3. Edite e execute o código SQL a seguir. Substitua o valor do espaço reservado `new_master_user` pelo novo nome de usuário. Essa sintaxe concede os privilégios listados em todos os esquemas de banco de dados (*.*) ao usuário ( `new_master_user` neste exemplo).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifique as concessões:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Todos os servidores do banco de dados do Azure para MySQL são criados com um usuário chamado "azure_superuser". Essa é uma conta de sistema criada pela Microsoft para gerenciar o servidor a fim de realizar monitoramento, backups e outras manutenções regulares. Os engenheiros de chamada também podem usar essa conta para acessar o servidor durante um incidente com autenticação de certificado e devem solicitar acesso usando processos JIT (just-in-time).

## <a name="next-steps"></a>Próximas etapas

Abra o firewall dos endereços IP dos computadores dos novos usuários para permitir que conectem-se:

* [Criar e gerenciar regras de firewall em um único servidor](howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall no servidor flexível](flexible-server/how-to-connect-tls-ssl.md)

Para obter mais informações sobre o gerenciamento de contas de usuário, consulte a documentação do produto MySQL para [Gerenciamento de contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), sintaxe de [concessão](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e [privilégios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
