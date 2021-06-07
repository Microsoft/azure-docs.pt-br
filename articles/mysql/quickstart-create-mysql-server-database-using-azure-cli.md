---
title: 'Início Rápido: Criar um servidor – CLI do Azure – Banco de Dados do Azure para MySQL'
description: Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de dados do Azure para MySQL no grupo de recursos do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3dd971ff36bde02072d14c465dbafec2823b256d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780309"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Início Rápido: Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure

> [!TIP]
> Considere a possibilidade de usar o comando mais simples da CLI do Azure [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) (atualmente em versão prévia). Experimente o [início rápido](./quickstart-create-server-up-azure-cli.md).

Este início rápido mostra como usar os comandos da [CLI do Azure](/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um servidor de Banco de Dados do Azure para MySQL em cinco minutos. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este guia de início rápido exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

 - Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az_account_list).

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL
Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [az group create](/cli/azure/group) e crie seu servidor MySQL dentro desse grupo. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um servidor de Banco de Dados do Azure para MySQL com o comando [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create). Um servidor pode conter vários bancos de dados.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Estes são os detalhes dos argumentos acima: 

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome exclusivo que identifique seu servidor do Banco de Dados do Azure para MySQL. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
local | westus | O local do Azure para o servidor.
admin-user | myadmin | O nome de usuário para o logon de administrador. Não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.
admin-password | *senha de segurança* | A senha do usuário administrador. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.
sku-name|GP_Gen5_2|Insira o nome do tipo de preço e a configuração de computação. Segue a convenção {tipo de preço} _{geração de computação}_ {vCores} em formato abreviado. Confira os [tipos de preço](./concepts-pricing-tiers.md) para obter mais informações.

>[!IMPORTANT] 
>- A versão padrão do MySQL no seu servidor é a 5.7. No momento, as versões 5.6 e 8.0 também estão disponíveis.
>- Para exibir todos os argumentos do comando **az mysql server create**, confira este [documento de referência](/cli/azure/mysql/server#az_mysql_server_create).
>- O SSL é habilitado por padrão no seu servidor. Para obter mais informações sobre o SSL, confira [Configurar a conectividade SSL](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor 
Por padrão, o novo servidor criado é protegido com regras de firewall e não é acessível publicamente. Você pode configurar a regra de firewall no servidor usando o comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule). Isso permitirá que você se conecte ao servidor localmente.

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua o endereço IP do qual você se conectará. Use um intervalo de endereços IP, se necessário. Se você não sabe como procurar seu endereço IP, acesse [https://whatismyipaddress.com/](https://whatismyipaddress.com/) para obtê-lo.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Se esse for o caso, você não poderá se conectar ao seu servidor enquanto o departamento de TI não abrir a porta 3306.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Conectar-se ao servidor do Banco de Dados do Azure para MySQL usando o cliente de linha de comando mysql
Você pode se conectar ao seu servidor usando uma ferramenta de cliente conhecida, a ferramenta de linha de comando **[mysql.exe](https://dev.mysql.com/downloads/)** com o [Azure Cloud Shell](../cloud-shell/overview.md). Como alternativa, use a linha de comando do mysql em seu ambiente local.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Limpar os recursos
Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Criar um aplicativo PHP no Windows com o MySQL](../app-service/tutorial-php-mysql-app.md)