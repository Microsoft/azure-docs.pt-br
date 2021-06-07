---
title: Criar usuários-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: c2d0cfc15457d45701f129ae329295064d773a09
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604100"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Criar usuários no banco de dados do Azure para PostgreSQL-servidor único

Este artigo descreve como você pode criar usuários em um servidor de banco de dados do Azure para PostgreSQL.

Se você quiser saber mais sobre como criar e gerenciar usuários de assinatura do Azure e seus privilégios, você pode visitar o [artigo Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/built-in-roles.md) do Azure ou examinar [como personalizar funções](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>A conta do administrador do servidor

Quando foi criado o Banco de Dados do Azure para PostgreSQL, você forneceu um nome de usuário de administrador de servidor e uma senha. Para saber mais, você pode seguir o [Início Rápido](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Como o nome de usuário administrador do servidor é um nome personalizado, você pode localizar o nome de usuário administrador do servidor escolhido no portal do Azure.

O banco de dados do Azure para o servidor PostgreSQL é criado com as 3 funções padrão definidas. Você pode ver essas funções executando o comando: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- seu usuário administrador do servidor

O usuário administrador do servidor é um membro da função azure_pg_admin. No entanto, a conta do administrador do servidor não faz parte da função azure_superuser. Como esse serviço é um serviço gerenciado de PaaS, somente a Microsoft faz parte da função de superusuário.

O mecanismo PostgreSQL usa privilégios para controlar o acesso a objetos de banco de dados, conforme discutido na [documentação do produto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). No Banco de Dados do Azure para PostgreSQL, o usuário administrador do servidor tem estes privilégios: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

A conta de usuário administrador do servidor pode ser usada para criar usuários adicionais e conceder a esses usuários acesso à função azure_pg_admin. Além disso, a conta de administrador do servidor pode ser usada para criar usuários e funções com menos privilégios que têm acesso aos banco de dados e esquemas individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar usuários administradores adicionais no Banco de Dados do Azure para PostgreSQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta preferencial do cliente, como pgAdmin ou psql.
   Se você não tem certeza de como se conectar, veja o [início rápido](./quickstart-create-server-database-portal.md)

3. Edite e execute o código SQL a seguir. Substitua seu novo nome de usuário pelo valor de espaço reservado < new_user > e a senha de espaço reservado com sua própria senha forte. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar usuários de banco de dados no Banco de Dados do Azure para PostgreSQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta preferencial do cliente, como pgAdmin ou psql.

3. Edite e execute o código SQL a seguir. Substitua o valor de espaço reservado `<db_user>` com seu novo nome de usuário pretendido e o valor de espaço reservado `<newdb>` com seu próprio nome de banco de dados. Substitua a senha de espaço reservado pela sua própria senha forte.

   Essa sintaxe de código sql cria um novo banco de dados nomeado testdb para fins de exemplo. Em seguida, ele cria um novo usuário no serviço do PostgreSQL e concede privilégios de conexão para o novo banco de dados para o usuário.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Usando uma conta de administrador, você precisará conceder privilégios adicionais para proteger os objetos no banco de dados. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para obter mais detalhes sobre as funções de banco de dados e os privilégios. Por exemplo:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do psql. Com este comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome de servidor, o nome do banco de dados e o nome de usuário.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Próximas etapas

Abra o firewall para os endereços IP das máquinas os novos usuários para que ele possa se conectar: [Criar e gerenciar regras de firewall para o Banco de Dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para saber mais sobre gerenciamento de contas de usuário, consulte a documentação do produto do PostgreSQL para [Funções e Privilégios do Banco de Dados](https://www.postgresql.org/docs/current/static/user-manag.html), [Sintaxe de GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) e [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html).
