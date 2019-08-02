---
title: 'Tutorial: Criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a CLI do Azure'
description: Este tutorial mostra como criar, configurar e consultar seu primeiro servidor de Banco de Dados do Azure para PostgreSQL - Servidor único usando a CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: db0ff9facbd8609955c5ef1918b0f8a6aa53ea65
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447233"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tutorial: Criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a CLI do Azure 
Neste tutorial, você usará a CLI (interface de linha de comando) do Azure e outros utilitários para aprender a:
> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para o servidor PostgreSQL
> * Configurar o firewall do servidor
> * Useo utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Você pode usar o Azure Cloud Shell no navegador ou [instalar a CLI do Azure]( /cli/azure/install-azure-cli) em seu computador para executar os comandos neste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias assinaturas, escolha a que for adequada na qual existe o recurso ou onde ele é cobrado. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL
Crie um [Banco de Dados do Azure para PostgreSQL](overview.md) usando o comando [az postgres server create](/cli/azure/postgres/server). Um servidor contém um grupo de bancos de dados gerenciados conjuntamente. 

O exemplo a seguir cria um servidor chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `myadmin`. O nome de um servidor é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo no Azure. Substitua o `<server_admin_password>` com seu próprio valor. É um servidor de Geração 5 de Uso Geral com 2 vCores.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
O valor do parâmetro sku-name segue a convenção {camada de preços}\_{geração de cálculo}\_{vCores} como nestes exemplos:
+ `--sku-name B_Gen5_2` mapeia para Básico, Gen 5 e 2 vCores.
+ `--sku-name GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Veja a documentação das [camadas de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por camada.

> [!IMPORTANT]
> O logon de administrador do servidor e a senha que você especificar aqui são necessários para fazer logon no servidor e em seus bancos de dados mais tarde neste Guia de início rápido. Lembre-se ou registre essas informações para o uso posterior.

Por padrão, o banco de dados **postgres** é criado em seu servidor. O [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado a uso por usuários, utilitários e aplicativos de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Crie uma regra de firewall no nível de servidor do PostgreSQL do Azure com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). Uma regra de firewall de nível de servidor permite que um aplicativo externo, tal como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), conecte-se ao servidor por meio do firewall do serviço Azure PostgreSQL. 

Você pode definir uma regra de firewall que abranja um intervalo de IP aos quais você possa se conectar de sua rede. O exemplo a seguir usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) para criar uma regra de firewall `AllowMyIP` que permite a conexão de um único endereço IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Para restringir o acesso ao seu servidor do Azure PostgreSQL para sua rede apenas, você pode definir a regra de firewall para abranger apenas o intervalo de endereços IP de sua rede corporativa.

> [!NOTE]
> O servidor PostgreSQL do Azure se comunica pela porta 5432. Ao se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Faça seu departamento de TI abrir a porta 5432 para se conectar ao seu servidor do Banco de Dados SQL do Azure.
>

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **administratorLogin** e o **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Conectar-se ao Banco de Dados do Azure para o banco de dados PostgreSQL usando psql
Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), ou o Azure Cloud Console para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao Banco de Dados do Azure para o servidor PostgreSQL.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para PostgreSQL:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se você preferir usar um caminho de URL para se conectar ao Postgres, codifique a URL de entrada @ do nome de usuário com `%40`. Por exemplo, a cadeia de conexão para psql seria,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Quando já estiver conectado ao servidor, crie um banco de dados em branco no prompt:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados
Agora que você sabe como se conectar ao Banco de Dados do Azure para PostgreSQL, pode concluir algumas tarefas básicas:

Primeiro, crie uma tabela e carregue-a com alguns dados. Por exemplo, crie uma tabela que rastreia informações de inventário:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Você agora pode ver a tabela recém-criada na lista de tabelas digitando:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Carregar dados na tabela
Agora que uma tabela foi criada, insira alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Agora, você adicionou duas linhas de dados de exemplo na tabela criada anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para recuperar as informações da tabela do inventário: 
```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados na tabela do inventário:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Você pode ver os valores atualizados quando recuperar os dados:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo
Imagine que você excluiu acidentalmente uma tabela. Isso é algo que você não pode se recuperar facilmente. O Banco de Dados do Azure para PostgreSQL permite que você volte para qualquer ponto anterior no qual o servidor tenha backups (determinados pelo período de retenção de backup que você configurou) e restaure esse ponto como um novo servidor. Use esse novo servidor para recuperar seus dados excluídos. 

O comando a seguir restaura o servidor de exemplo para um ponto anterior à adição da tabela:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

O comando `az postgres server restore` precisa dos seguintes parâmetros:

| Configuração | Valor sugerido | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos no qual o servidor de origem existe.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando de restauração. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selecione um point-in-time para restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu próprio fuso horário local, como `2017-04-13T05:59:00-08:00`, ou usar o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID para restaurar a partir do servidor de origem. |

Restaurar um servidor para um ponto no tempo cria um novo servidor, copiado como o servidor original do ponto no tempo que você especificar. O local e os valores de tipo de preço para o servidor restaurado são o mesmo que o servidor de origem.

O comando é síncrono e retornará depois que o servidor for restaurado. Depois que a restauração é concluída, localize o novo servidor que foi criado. Verifique se os dados foram restaurados conforme o esperado.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como usar a CLI (interface de linha de comando) do Azure e outros utilitários para aprender a:
> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para o servidor PostgreSQL
> * Configurar o firewall do servidor
> * Useo utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Em seguida, para saber como usar o portal do Azure para executar tarefas semelhantes, examine este tutorial: [Criar seu primeiro Banco de Dados do Azure para PostgreSQL usando o portal do Azure](tutorial-design-database-using-azure-portal.md)
