---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando a CLI do Azure
description: Este artigo descreve como criar e gerenciar as regras de firewall do Banco de Dados do Azure para MariaDB usando a linha de comando CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038613"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Regras de firewall de nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados do Azure para servidor MariaDB de um endereço IP específico ou um intervalo de endereços IP. Usando comandos convenientes da CLI do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras de firewall para gerenciar o servidor. Para uma visão geral do banco de dados do Azure para MariaDB firewalls, consulte [banco de dados do Azure para regras de firewall de servidor MariaDB](./concepts-firewall-rules.md).

Regras de VNet (rede) virtuais também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre [criando e gerenciando a rede Virtual regras usando a CLI do Azure e pontos de extremidade de serviço](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Um [banco de dados e um servidor do Banco de Dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando **az mariadb server firewall-rule** é usado na CLI do Azure para criar, excluir, listar, exibir e atualizar regras de firewall.

Comandos:
- **criar**: Crie uma regra de firewall do servidor do MariaDB do Azure.
- **excluir**: Exclua uma regra de firewall do servidor do MariaDB do Azure.
- **lista**: Liste as regras de firewall de servidor do MariaDB do Azure.
- **show**: Mostre os detalhes de uma regra de firewall de servidor do MariaDB do Azure.
- **atualizar**: Atualize uma regra de firewall do servidor do MariaDB do Azure.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Entrar no Azure e listar seu banco de dados do Azure para servidores MariaDB
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

5. Liste os servidores de Banco de Dados do Azure para MariaDB para sua assinatura e grupo de recursos, se você não tiver certeza dos nomes. Use o comando [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list).

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Anote o atributo de nome na lista, que será necessário para especificar em qual servidor do MariaDB trabalhar. Se for necessário, confirme os detalhes desse servidor e usando o atributo de nome para confirmar se está correto. Use o comando [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show).

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Listar regras de firewall no servidor de Banco de Dados do Azure para MariaDB 
Usando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Use o comando [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list).  Observe que o atributo de nome do servidor é especificado na opção **--server** e não na opção **--name**. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída listará as regras, se houver, no formato JSON (por padrão). É possível usar a opção **--output table** para gerar os resultados em um formato de tabela mais legível.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Criar uma regra de firewall no Banco de Dados do Azure para MariaDB
Usando o nome de servidor do MariaDB do Azure e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Use o comando [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Nomeie a regra, bem como os IPs inicial e final da regra (para dar acesso a um intervalo de endereços IP).
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso de um endereço IP único, forneça o mesmo endereço IP como IP Inicial e IP Final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que aplicativos dos endereços IP do Azure se conectem ao seu Banco de Dados do Azure para MariaDB, forneça o endereço IP 0.0.0.0 como IP Inicial e IP Final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

Após o êxito, cada saída de comando listará os detalhes da regra de firewall que você criou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Atualizar uma regra de firewall no servidor de Banco de Dados do Azure para MariaDB 
Usando o nome de servidor do MariaDB do Azure e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Use o comando [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update). Forneça o nome da regra de firewall existente como entrada, bem como os atributos de IP inicial e IP final a serem atualizados.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall atualizada, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Mostrar detalhes de uma regra de firewall no servidor de Banco de Dados do Azure para MariaDB
Usando o nome de servidor do MariaDB do Azure e o nome do grupo de recursos, mostre os detalhes da regra de firewall existente no servidor. Use o comando [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show). Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, a saída do comando listará os detalhes da regra de firewall especificado, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Excluir uma regra de firewall no Banco de Dados do Azure para MariaDB
Usando o nome de servidor do MariaDB do Azure e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Use o comando [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete). Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro será exibido.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Regras de firewall do Servidor de Banco de Dados do Azure para MariaDB](./concepts-firewall-rules.md).
- [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md).
- Proteger ainda mais o acesso ao seu servidor pelo [criando e gerenciando a rede Virtual regras usando a CLI do Azure e pontos de extremidade de serviço](howto-manage-vnet-cli.md).
