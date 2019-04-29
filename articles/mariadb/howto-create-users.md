---
title: Criar usuários no servidor de Banco de Dados do Azure para MariaDB
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um servidor de Banco de Dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ed373cfa0ac755d56e7bc2601c65e0e6482ff6d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038864"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Criar usuários no Banco de Dados do Azure para MariaDB 
Este artigo descreve como você pode criar usuários em um servidor de Banco de Dados do Azure para MariaDB.

Quando foi criado o Banco de Dados do Azure para MariaDB, você forneceu um nome de usuário de logon de administrador de servidor e uma senha. Para saber mais, você pode seguir o [Início Rápido](quickstart-create-mariadb-server-database-using-azure-portal.md). Você pode localizar seu nome de usuário de logon de administrador do servidor do portal do Azure.

O usuário administrador do servidor obtém determinados privilégios para o servidor, conforme listado: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Depois de criar o servidor de Banco de Dados do Azure para MariaDB, você poderá usar a primeira conta de usuário administrador de servidor para criar usuários adicionais e conceder acesso de administrador a eles. Além disso, a conta de administrador do servidor pode ser usada para criar usuários com menos privilégios que têm acesso aos esquemas de banco de dados individuais.

## <a name="create-additional-admin-users"></a>Criar usuários administradores adicionais
1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras. 
   Se você não tiver certeza de como se conectar, veja [Usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o seu novo nome de usuário para o valor de espaço reservado `new_master_user`. Esta sintaxe concede os privilégios listados em todos os esquemas de banco de dados (*.*) para o nome de usuário (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verificar as concessões 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Criar usuários de banco de dados

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras. 
   Se você não tiver certeza de como se conectar, veja [Usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o valor de espaço reservado `db_user` com seu novo nome de usuário pretendido e o valor de espaço reservado `testdb` com seu próprio nome de banco de dados.

   Essa sintaxe de código sql cria um novo banco de dados nomeado testdb para fins de exemplo. Em seguida, ele cria um novo usuário no serviço de Banco de Dados do Azure para MariaDB e concede a todos os privilégios para o novo esquema de banco de dados (testdb.\*) a esse usuário. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifique as concessões no banco de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do mysql. Com este comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome de servidor, o nome do banco de dados e o nome de usuário.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Para obter mais informações sobre gerenciamento de contas de usuário, veja a documentação do MariaDB para [Gerenciamento de contas de usuário](https://mariadb.com/kb/en/library/user-account-management/), [Sintaxe de GRANT](https://mariadb.com/kb/en/library/grant/) e [Privilégios](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Próximas etapas
Abra o firewall para os endereços IP das máquinas dos novos usuários para que eles possam se conectar: [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Microsoft Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
