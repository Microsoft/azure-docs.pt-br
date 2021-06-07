---
title: 'Início Rápido: Criar um servidor – CLI do Azure – Banco de Dados do Azure para MariaDB'
description: Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de Dados do Azure para MariaDB no grupo de recursos do Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042c70fdd08a6de2b97c4560eb2b6a24eec0bb34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789961"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Início Rápido: Criar um servidor de Banco de Dados do Azure para MariaDB usando a CLI do Azure

É possível usar a CLI do Azure para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de Dados do Azure para MariaDB no grupo de recursos do Azure em aproximadamente cinco minutos.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

Se você tiver várias assinaturas, escolha aquela que contém o recurso ou aquela na qual você recebe a cobrança. Para selecionar uma ID da assinatura específica em sua conta, use o comando [az account set](/cli/azure/account#az_account_set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um servidor do Banco de Dados do Azure para MariaDB

Crie um servidor de Banco de Dados do Azure para MariaDB usando o comando [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create). Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

Configuração | Valor de exemplo | Descrição
---|---|---
name | **mydemoserver** | Insira um nome exclusivo que identifique o servidor de Banco de Dados do Azure para MariaDB. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter entre 3 e 63 caracteres.
resource-group | **myresourcegroup** | Insira o nome do grupo de recursos do Azure.
sku-name | **GP_Gen5_2** | O nome da SKU. Segue a convenção *tipo de preço*\_*geração de computação*\_*vCores* em resumo. Para obter mais informações sobre o parâmetro **sku-name**, consulte a seção após a tabela.
backup-retention | **7** | Quanto tempo um backup deve ser retido. A unidade é dias. Intervalo: 7 a 35. 
geo-redundant-backup | **Desabilitado** | Indica se os backups com redundância geográfica devem ser habilitados para este servidor. Valores permitidos: **Habilitado**, **Desabilitado**.
local | **westus** | O local do Azure para o servidor.
ssl-enforcement | **Enabled** | Se o SSL deve ser habilitado para este servidor. Valores permitidos: **Habilitado**, **Desabilitado**.
storage-size | **51200** | A capacidade de armazenamento do servidor (a unidade é megabytes). Os tamanhos de armazenamento válidos são 5.120 MB (mínimo) com aumentos em incrementos de 1.024 MB. Para obter mais informações sobre tamanhos de armazenamento, consulte o documento [tipos de preço](./concepts-pricing-tiers.md). 
version | **10.2** | A versão do mecanismo principal do MariaDB.
admin-user | **myadmin** | O nome de usuário para o logon de administrador. O parâmetro **admin-user** não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
admin-password | *sua senha* | A senha do usuário administrador. Sua senha deve conter entre 8 e 128 caracteres. Ela precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.

O valor do parâmetro sku-name segue a convenção {camada de preços}\_{geração de cálculo}\_{vCores} como nestes exemplos:
+ `--sku-name B_Gen5_1` é mapeado para Básico, Gen 5 e 1 vCore. Essa opção é o menor SKU disponível.
+ `--sku-name GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Para obter informações sobre valores válidos por região e camadas, consulte [Tipos de preço](./concepts-pricing-tiers.md).

O exemplo a seguir cria um servidor chamado **mydemoserver** na região Oeste dos EUA. O servidor está no grupo de recursos **myresourcegroup** e tem o logon de administrador do servidor **myadmin**. Trata-se de um servidor Gen 5 com o tipo de preço Uso Geral e com 2 vCores. Um nome de servidor mapeia para um nome DNS e deve ser globalmente exclusivo no Azure. Substitua `<server_admin_password>` com sua própria senha de administrador do servidor.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que servidores criados no tipo de preço Básico não podem ser dimensionados mais tarde para Uso Geral ou Otimizado para Memória. Veja a [página de preço](https://azure.microsoft.com/pricing/details/mariadb/) para obter mais informações.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para MariaDB usando o comando [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando do mysql ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço do Banco de Dados do Azure para MariaDB.

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou intervalo de endereços IP que corresponda ao local do qual você se conecta.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MariaDB se comunicam pela porta 3306. Se está tentando se conectar de uma rede corporativa, é possível que o tráfego de saída pela porta 3306 não seja permitido. Nesse caso, você só poderá se conectar ao seu servidor se o departamento de TI abrir a porta 3306.

## <a name="configure-ssl-settings"></a>Configurar definições de SSL

Por padrão, as conexões SSL entre o servidor e os aplicativos cliente são impostas. Essa configuração padrão garante a segurança de dados “em movimento” ao criptografar o fluxo de dados pela Internet. Para esse início rápido, desabilite as conexões SSL para o servidor. Desabilitar SSL não é recomendável para servidores de produção. Para saber mais, confira [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md) (Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MariaDB).

O exemplo a seguir desabilitará o SSL, impondo seu servidor de Banco de Dados do Azure para MariaDB:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host. Para obter as informações de conexão, execute o seguinte comando:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote os valores de **fullyQualifiedDomainName** e o **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
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

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando do mysql

Conecte-se ao seu servidor usando a ferramenta de linha de comando do mysql. É possível [baixar](https://dev.mysql.com/downloads/) a ferramenta de linha de comando aqui e instalá-la em seu computador. Você também pode acessar a ferramenta de linha de comando, selecionando o botão **Experimente** em um exemplo de código neste artigo. Outra maneira de acessar a ferramenta de linha de comando é selecionando o botão **> _** na barra de ferramentas superior direita no portal do Azure para abrir o **Azure Cloud Shell**.

Para se conectar ao servidor usando a ferramenta de linha de comando do mysql:

1. Conectar-se ao servidor:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Exiba o status do servidor no prompt `mysql>`:

   ```sql
   status
   ```

   Você deverá ver algo semelhante ao texto a seguir:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Conectar-se ao servidor usando MySQL Workbench

1. Abra o MySQL Workbench no computador cliente. Se ainda não estiver instalado, [baixe](https://dev.mysql.com/downloads/workbench/) e instale o aplicativo.

2. Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   ![Configurar uma nova conexão](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Configuração | Valor sugerido | Descrição |
   |---|---|---|
   | Nome da Conexão | **Conexão de demonstração** | Insira um rótulo para essa conexão (pode ser qualquer nome de conexão) |
   | Método de Conexão | **Padrão (TCP/IP)** | Usar o protocolo TCP/IP para se conectar ao Banco de Dados do Azure para MariaDB |
   | Nome do host | **mydemoserver.mariadb.database.azure.com** | O nome do servidor que você anotou anteriormente. |
   | Porta | **3306** | A porta padrão para Banco de Dados do Azure para MariaDB. |
   | Nome de Usuário | **myadmin\@mydemoserver** | O logon de administrador do servidor que você anotou anteriormente. |
   | Senha | *sua senha* | Use a senha da conta de administrador que você configurou anteriormente. |

3. Para verificar se todos os parâmetros estão configurados corretamente, selecione **Testar conexão**.

4. Selecione a conexão para se conectar com sucesso ao servidor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar desses recursos usados no início rápido para outro início rápido ou tutorial, você poderá excluí-los executando o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser excluir apenas o servidor que você criou neste início rápido, execute o comando [az mariadb server delete](/cli/azure/mariadb/server#az_mariadb_server_delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Projetar um banco de dados MariaDB com a CLI do Azure](tutorial-design-database-cli.md)
