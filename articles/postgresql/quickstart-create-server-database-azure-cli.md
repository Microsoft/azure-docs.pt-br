---
title: 'Início Rápido: Criar servidor – CLI do Azure – Banco de Dados do Azure para PostgreSQL – servidor único'
description: Neste guia de início rápido, você criará um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a595d677cf0964083526cb7e2c73471148be0fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778403"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Início Rápido: Criar um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure

Este guia de início rápido mostra como usar os comandos da [CLI do Azure](/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um servidor único do Banco de Dados do Azure para PostgreSQL em cinco minutos. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

    > [!TIP]
    >  Considere a possibilidade de usar o comando mais simples da CLI do Azure [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), que atualmente está em versão prévia. Experimente o [início rápido](./quickstart-create-server-up-azure-cli.md).

- Selecione a ID da assinatura específica na sua conta usando o comando [az account set](/cli/azure/account).

    - Anote o valor de **id** da saída **az login** para usá-lo como o valor para o argumento **subscription** no comando. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az_account_list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [az group create](/cli/azure/group#az_group_create) e crie o servidor PostgreSQL dentro desse grupo de recursos. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um [servidor do Banco de Dados do Azure para PostgreSQL](overview.md) usando o comando [az postgres server create](/cli/azure/postgres/server). Um servidor pode conter vários bancos de dados.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Estes são os detalhes dos argumentos anteriores: 

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Nome exclusivo que identifica o servidor do Banco de Dados do Azure para PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa conter de 3 a 63 caracteres. Para obter mais informações, confira [Regras de nomenclatura do Banco de Dados do Azure para PostgreSQL](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql).
resource-group | myresourcegroup | Nome do grupo de recursos do Azure.
local | westus | A localização do Azure para o servidor.
admin-user | myadmin | Nome de usuário para o logon de administrador. Ele não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.
admin-password | *senha de segurança* | Senha do usuário administrador. Ela precisa conter de 8 a 128 caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.
sku-name|GP_Gen5_2| Nome do tipo de preço e a configuração de computação. Segue a convenção {tipo de preço} _{geração da computação}_ {vCores} em formato abreviado. Para obter mais informações, confira [Preços do Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- A versão padrão do PostgreSQL no seu servidor é 9.6. Para ver todas as versões compatíveis, confira [Versões principais compatíveis do PostgreSQL](./concepts-supported-versions.md).
>- Para ver todos os argumentos do comando **az postgres server create**, confira [este documento de referência](/cli/azure/postgres/server#az_postgres_server_create).
>- O SSL é habilitado por padrão no seu servidor. Para obter mais informações sobre o SSL, confira [Configurar a conectividade SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor 
Por padrão, o servidor criado não é publicamente acessível e é protegido com regras de firewall. Você pode configurar as regras de firewall no servidor usando o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) para dar acesso ao seu ambiente local para se conectar ao servidor. 

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua o endereço IP ou o intervalo dos endereços IP que correspondem ao local em que você se conectará. Se você não souber seu endereço IP, acesse [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) para obtê-lo.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Para evitar problemas de conectividade, verifique se o firewall da rede permite a porta 5432. Os servidores do Banco de Dados do Azure para PostgreSQL usam essa porta. 

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, forneça credenciais de acesso e informações do host.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote os valores de **administratorLogin** e **fullyQualifiedDomainName**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Conectar-se ao servidor do Banco de Dados do Azure para PostgreSQL usando o psql
O cliente [psql](https://www.postgresql.org/docs/current/static/app-psql.html) é uma opção popular para se conectar a servidores PostgreSQL. Conecte-se ao seu servidor usando o psql com o [Azure Cloud Shell](../cloud-shell/overview.md). Como alternativa, use também o psql no ambiente local se ele estiver disponível. Um banco de dados vazio, o **postgres**, é criado automaticamente com um novo servidor PostgreSQL. Use esse banco de dados para se conectar ao psql, conforme mostrado no código a seguir. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Se você preferir usar um caminho de URL para se conectar ao Postgres, codifique a URL de entrada @ do nome de usuário com `%40`. Por exemplo, a cadeia de conexão do psql será:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Limpar os recursos
Caso você não precise desses recursos para outro guia de início rápido ou tutorial, exclua-os executando o comando a seguir. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você apenas deseja excluir o servidor recém-criado, execute o comando [az postgres server delete](/cli/azure/postgres/server).

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migrar seu banco de dados usando a exportação e a importação](./howto-migrate-using-export-and-import.md)
