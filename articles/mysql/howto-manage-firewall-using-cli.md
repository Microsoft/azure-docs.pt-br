---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando a CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar Banco de Dados do Azure para regras de firewall do MySQL usando a linha de comando CLI do Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: bc7763981c27a3d37cc1bd16c0f8efc0b4c01ce0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando a CLI do Azure
As regras de firewall no nível de servidor permitem que os administradores gerenciem o acesso a um Servidor de Banco de Dados do Azure para MySQL de um endereço IP específico ou intervalo de endereços IP. Usando comandos convenientes da CLI do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras de firewall para gerenciar o servidor. Para obter uma visão geral dos firewalls do Banco de Dados do Azure para MySQL, confira [Regras de firewall do servidor de Banco de Dados do Azure para MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Pré-requisitos
* [Instale a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Instalar o SDK do Azure Python para os serviços PostgreSQL e MySQL.
* Instalar o componente de CLI do Azure para os serviços PostgreSQL e MySQL.
* Criar um servidor de Banco de Dados do Azure para MySQL.

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando **az mysql server firewall-rule** é usado na CLI do Azure para criar, excluir, listar, exibir e atualizar regras de firewall.

Comandos:
- **create**: crie uma regra de firewall de servidor MySQL do Azure.
- **delete**: exclua uma regra de firewall de servidor MySQL do Azure.
- **list: liste as regras de firewall de servidor MySQL do Azure.
- **show: mostre os detalhes de uma regra de firewall de servidor MySQL do Azure.
- **update**: atualize uma regra de firewall de servidor MySQL do Azure.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Fazer logon no Azure e listar os servidores de Banco de Dados do Azure para MySQL
Conecte com segurança a CLI do Azure à sua conta do Azure usando o comando **az login**.

1. Execute o comando a seguir na linha de comando:
```azurecli
az login
```
Esse comando gera um código para usar na próxima etapa.

2. Use um navegador da Web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e, em seguida, insira o código.

3. No prompt, entre utilizando suas credenciais do Azure.

4. Após a autorização do logon, uma lista de assinaturas será impressa no console. Copie a ID da assinatura desejada para definir a assinatura atual a ser usada.
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. Liste os servidores de Bancos de Dados do Azure para MySQL para sua assinatura e grupo de recursos, se você não tiver certeza dos nomes.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Anote o atributo de nome na lista, que será necessário para especificar em qual servidor MySQL trabalhar. Se for necessário, confirme os detalhes desse servidor e usando o atributo de nome para confirmar se está correto:

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Liste regras de firewall no servidor de Banco de Dados do Azure para MySQL 
Usando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Observe que o atributo de nome do servidor é especificado na opção **--server** e não na opção **--name**.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
A saída listará as regras, se houver, no formato JSON (por padrão). É possível usar a opção **--output table** para gerar os resultados em um formato de tabela mais legível.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Criar uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Nomeie a regra, bem como os IPs inicial e final da regra (para dar acesso a um intervalo de endereços IP).
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Para permitir o acesso de um endereço IP único, forneça o mesmo endereço IP como IP Inicial e IP Final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você criou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Atualizar uma regra de firewall no servidor de Banco de Dados do Azure para MySQL 
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada, bem como os atributos de IP inicial e IP final a serem atualizados.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall atualizada, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Mostrar detalhes de uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, mostre os detalhes da regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Após o êxito, a saída do comando listará os detalhes da regra de firewall especificado, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Excluir uma regra de firewall no servidor de Banco de Dados do Azure para MySQL
Usando o nome de servidor MySQL do Azure e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro será exibido.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Regras de firewall do Servidor de Banco de Dados do Azure para MySQL](./concepts-firewall-rules.md).
- [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure](./howto-manage-firewall-using-portal.md).