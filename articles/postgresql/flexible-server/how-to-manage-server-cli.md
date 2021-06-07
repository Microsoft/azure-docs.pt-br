---
title: Gerenciar servidor-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Saiba como gerenciar um servidor de banco de dados do Azure para PostgreSQL-flexível do CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493671"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Gerenciar um banco de dados do Azure para PostgreSQL – servidor flexível usando o CLI do Azure

> [!IMPORTANT]
> Banco de dados do Azure para PostgreSQL-o servidor flexível está em versão prévia.

Este artigo mostra como gerenciar seu servidor flexível implantado no Azure. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. 

Você precisará executar a CLI do Azure versão 2,0 ou posterior, localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Entre em sua conta usando o comando [AZ login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Selecione sua assinatura usando o comando [AZ Account Set](/cli/azure/account) . Anote o valor de **ID** da saída de **logon AZ** para usar como o valor para o argumento **Subscription** no comando a seguir. Se você tiver várias assinaturas, escolha a assinatura para a qual o recurso deve ser cobrado. Para identificar todas as suas assinaturas, use o comando [AZ Account List](/cli/azure/account#az-account-list) .

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se você ainda não criou um servidor flexível, precisará fazer isso para seguir este guia de instruções.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Você pode dimensionar facilmente sua camada de computação, vCores e armazenamento usando o comando a seguir. Para obter uma lista de todas as operações de servidor que você pode executar, consulte a visão geral [AZ postgres flexível-Server](/cli/azure/postgres/flexible-server) .

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

A seguir estão os detalhes dos argumentos no código anterior:

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome exclusivo para o servidor. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v3|Insira o nome da camada e do tamanho da computação. O valor segue a Convenção *Standard_ {tamanho da VM}* em abreviação. Confira os [tipos de preço](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | Insira a capacidade de armazenamento do servidor em megabytes. O mínimo é 5120, aumentando em incrementos de 1024.

> [!IMPORTANT]
> Você não pode reduzir o armazenamento horizontalmente. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gerenciar bancos de dados PostgreSQL em um servidor

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Agora, vamos usar a ferramenta de linha de comando psql para se conectar ao banco de dados do Azure para o servidor PostgreSQL.

1. Execute o seguinte comando **psql** :

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** em seu servidor PostgreSQL **mydemoserver.Postgres.Database.Azure.com** por meio de suas credenciais de acesso. Quando for solicitado, insira o `<server_admin_password>` que você escolheu.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Depois de se conectar, a ferramenta psql exibe um prompt de **postgres** onde você pode inserir comandos SQL. Um aviso será exibido na saída de conexão inicial se a versão do psql que você está usando for diferente da versão no banco de dados do Azure para o servidor PostgreSQL.

   Exemplo de saída psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se o firewall não está configurado para permitir o endereço IP do seu cliente, ocorre o seguinte erro:
   >
   > "psql: FATAL: nenhuma entrada pg_hba.conf para o host `<IP address>`, usuário "myadmin", banco de dados "postgres", SSL em FATAL: Uma conexão SSL é necessária. Especifique as opções de SSL e tente novamente. "
   >
   > Confirme se o endereço IP do seu cliente é permitido nas regras de firewall.

2. Crie um banco de dados em branco chamado **postgresdb** digitando o seguinte comando no prompt:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. No prompt, execute o seguinte comando para alternar as conexões para o banco de dados **postgresdb** recém-criado:

    ```bash
    \c postgresdb
    ```

4. Digite  `\q` e selecione Enter para sair do psql.

Nesta seção, você se conectou ao banco de dados do Azure para o servidor PostgreSQL via psql e criou um banco de dados de usuário em branco.

## <a name="reset-the-admin-password"></a>Redefinir a senha do administrador

Você pode alterar a senha da função de administrador com o seguinte comando:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Escolha uma senha que tenha um mínimo de 8 caracteres e um máximo de 128 caracteres. A senha deve conter caracteres de três das seguintes categorias: 
> - Letras maiúsculas do alfabeto inglês
> - Letras minúsculas do alfabeto inglês
> - Números
> - Caracteres não alfanuméricos

## <a name="delete-a-server"></a>Excluir um servidor

Para excluir o servidor flexível do banco de dados do Azure para PostgreSQL, execute o comando [AZ postgres flexível-Server Delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

- [Entender os conceitos de backup e restauração](concepts-backup-restore.md)
- [Ajustar e monitorar o servidor](concepts-monitoring.md)