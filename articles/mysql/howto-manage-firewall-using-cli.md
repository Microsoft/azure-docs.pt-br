---
title: Gerenciar regras de firewall-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como criar e gerenciar as regras de firewall do Banco de Dados do Azure para MySQL usando a linha de comando CLI do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaddd8b2979b30251301ad041ea4b872c23d680b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541327"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando a CLI do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um servidor de banco de dados do Azure para MySQL de um endereço IP específico ou de um intervalo de endereços IP. Usando comandos convenientes da CLI do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras de firewall para gerenciar o servidor. Para obter uma visão geral do banco de dados do Azure para firewalls MySQL, consulte [regras de firewall do banco de dados do Azure para MySQL](./concepts-firewall-rules.md).

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instale o CLI do Azure](/cli/azure/install-azure-cli).
* Um [banco de dados e servidor do Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando **az mysql server firewall-rule** é usado na CLI do Azure para criar, excluir, listar, exibir e atualizar regras de firewall.

Comandos:
- **create** : crie uma regra de firewall de servidor MySQL do Azure.
- **delete** : exclua uma regra de firewall de servidor MySQL do Azure.
- **lista** : listar as regras de firewall do servidor MySQL do Azure.
- **Mostrar** : Mostre os detalhes de uma regra de firewall do servidor MySQL do Azure.
- **update** : atualize uma regra de firewall de servidor MySQL do Azure.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Entre no Azure e liste seu banco de dados do Azure para servidores MySQL
Conecte com segurança a CLI do Azure à sua conta do Azure usando o comando **az login**.

1. Na linha de comando, execute o seguinte comando:
    ```azurecli
    az login
    ```
   Esse comando gera um código para usar na próxima etapa.

2. Use um navegador da Web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e, em seguida, insira o código.

3. No prompt, entre usando suas credenciais do Azure.

4. Após a autorização do logon, uma lista de assinaturas será impressa no console. Copie a ID da assinatura desejada para definir a assinatura atual a ser usada. Use o comando [az account set](/cli/azure/account#az-account-set).
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Liste os servidores de Bancos de Dados do Azure para MySQL para sua assinatura e grupo de recursos, se você não tiver certeza dos nomes. Use o comando [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list).

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Anote o atributo de nome na lista, que será necessário para especificar em qual servidor MySQL trabalhar. Se for necessário, confirme os detalhes desse servidor e usando o atributo de nome para confirmar se está correto. Use o comando [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show).

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Liste regras de firewall no servidor de Banco de Dados do Azure para MySQL 
Usando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Use o comando [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list).  Observe que o atributo de nome do servidor é especificado na opção **--server** e não na opção **--name**. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída listará as regras, se houver, no formato JSON (por padrão). É possível usar a opção **--output table** para gerar os resultados em um formato de tabela mais legível.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Criar uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Use o comando [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create). Nomeie a regra, bem como os IPs inicial e final da regra (para dar acesso a um intervalo de endereços IP).
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso de um endereço IP único, forneça o mesmo endereço IP como IP Inicial e IP Final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que aplicativos dos endereços IP do Azure se conectem ao seu Banco de Dados do Azure para servidor MySQL, forneça o endereço IP 0.0.0.0 como IP Inicial e IP Final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

Após o êxito, cada saída de comando listará os detalhes da regra de firewall que você criou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Atualizar uma regra de firewall no servidor de Banco de Dados do Azure para MySQL 
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Use o comando [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update). Forneça o nome da regra de firewall existente como entrada, bem como os atributos de IP inicial e IP final a serem atualizados.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall atualizada, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Mostrar detalhes de uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, mostre os detalhes da regra de firewall existente no servidor. Use o comando [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show). Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, a saída do comando listará os detalhes da regra de firewall especificado, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Excluir uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Use o comando [az mysql server firewall delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete). Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro será exibido.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Regras de firewall do Servidor de Banco de Dados do Azure para MySQL](./concepts-firewall-rules.md).
- [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure](./howto-manage-firewall-using-portal.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).