---
title: 'Tutorial: Criar um Banco de Dados do Azure para MariaDB usando a CLI do Azure'
description: Este tutorial explica como criar e gerenciar o servidor e banco de dados do Banco de Dados do Azure para MariaDB usando a CLI do Azure na linha de comando.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/10/2018
ms.custom: mvc
ms.openlocfilehash: 548f4f10758b2d69bf4fda00f8bf52d33d20306c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999154"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Tutorial: Criar um Banco de Dados do Azure para MariaDB usando a CLI do Azure

O Banco de Dados do Azure para MariaDB é um serviço de banco de dados relacional na nuvem da Microsoft com base no mecanismo de banco de dados edição da comunidade MariaDB. Neste tutorial, você usará a CLI (interface de linha de comando) do Azure e outros utilitários para aprender a:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para MariaDB
> * Configurar o firewall do servidor
> * Use a [ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

Você pode usar o Azure Cloud Shell no navegador ou [Instalar o Azure CLI]( /cli/azure/install-azure-cli) em seu próprio computador para executar os blocos de código neste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias assinaturas, escolha a que for adequada na qual existe o recurso ou onde ele é cobrado. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) com o comando [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um servidor do Banco de Dados do Azure para MariaDB
Crie um servidor do Banco de Dados do Azure para MariaDB com o comando `az mariadb server create`. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

O exemplo a seguir cria um servidor do Banco de Dados do Azure para MariaDB localizado em `westus` no grupo de recursos `myresourcegroup` com o nome `mydemoserver`. O servidor tem um logon de administrador chamado `myadmin`. É um servidor de Geração 5 de Uso Geral com 2 vCores. Substitua o `<server_admin_password>` com seu próprio valor.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
O valor do parâmetro sku-name segue a convenção {camada de preços}\_{geração de cálculo}\_{vCores} como nestes exemplos:
+ `--sku-name B_Gen5_4` mapeia para Básico, Gen 5 e 4 vCores.
+ `--sku-name GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Veja a documentação das [camadas de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por camada.

> [!IMPORTANT]
> O logon de administrador do servidor e a senha que você especificar aqui são necessários para fazer logon no servidor e em seus bancos de dados mais tarde neste Guia de início rápido. Lembre-se ou registre essas informações para o uso posterior.


## <a name="configure-firewall-rule"></a>Configurar regra de firewall
Criar uma regra de firewall no nível do servidor do Banco de Dados do Azure para MariaDB com o comando `az mariadb server firewall-rule create`. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando **mysql** ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço Azure MariaDB.

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substituir no endereço IP ou intervalo dos endereços IP que correspondem a onde você vai se conectar.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Conectar-se ao servidor usando mysql
Use a [ferramenta de linha de comando do MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma conexão com seu servidor do Banco de Dados do Azure para MariaDB. Neste exemplo, o comando é:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Criar um banco de dados vazio
Quando você estiver conectado ao servidor, crie um banco de dados em branco.
```sql
mysql> CREATE DATABASE mysampledb;
```

No prompt, execute o seguinte comando para mudar a conexão para esse banco de dados recém-criado:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados
Agora que você sabe como se conectar ao Banco de Dados do Azure para MariaDB, conclua algumas tarefas básicas.

Primeiro, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas
Agora que você tem uma tabela, insira alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Agora, você tem duas linhas de dados de exemplo na tabela criada anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para recuperar as informações da tabela do banco de dados.
```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada à medida que você recupera os dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo
Imagine que você excluiu acidentalmente essa tabela. Isso é algo que você não pode se recuperar facilmente. O Banco de Dados do Azure para MariaDB permite que você volte até qualquer ponto no tempo, até os últimos 35 dias, e restaure esse ponto no tempo em um novo servidor. Use esse novo servidor para recuperar seus dados excluídos. As etapas a seguir restauram o servidor de exemplo para um ponto anterior à adição da tabela.

Para a restauração, você precisa das seguintes informações:

- Ponto de restauração: Selecione um ponto no tempo anterior à alteração do servidor. Deve ser maior ou igual ao valor de backup mais antigo do banco de dados de origem.
- Servidor de destino: Forneça o novo nome do servidor para o qual deseja fazer a restauração
- Servidor de origem: Forneça o nome do servidor do qual deseja fazer a restauração
- Localização: Não é possível selecionar a região; por padrão, ela é a mesma do servidor de origem

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

O comando `az mariadb server restore` precisa dos seguintes parâmetros:

| Configuração | Valor sugerido | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos no qual o servidor de origem existe.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando de restauração. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selecione um point-in-time para restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu próprio fuso horário local, como `2017-04-13T05:59:00-08:00`, ou usar o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID para restaurar a partir do servidor de origem. |

Restaurar um servidor para um ponto no tempo cria um novo servidor, copiado como o servidor original do ponto no tempo que você especificar. O local e os valores de tipo de preço para o servidor restaurado são o mesmo que o servidor de origem.

O comando é síncrono e retornará depois que o servidor for restaurado. Depois que a restauração é concluída, localize o novo servidor que foi criado. Verifique se os dados foram restaurados conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:
> [!div class="checklist"]
> * Criar um servidor do Banco de Dados do Azure para MariaDB
> * Configurar o firewall do servidor
> * Use a [ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados
