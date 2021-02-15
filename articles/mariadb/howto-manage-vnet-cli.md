---
title: Gerenciar pontos de extremidade de VNet-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como criar e gerenciar o Banco de Dados do Azure para MariaDB para pontos de extremidade e regras de serviço do VNet usando a linha de comando da CLI do Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bee4af12b30b64409812d6758521a9ec29b2b61f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665082"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Criar e gerenciar o Banco de Dados do Azure para MariaDB para pontos de extremidade de serviço do MariaDB VNet usando a CLI do Azure

Pontos de extremidade e regras de serviços da Rede Virtual (VNet) estendem o espaço de endereçamento privado de uma Rede Virtual ao seu Banco de Dados do Azure para MariaDB. Usando comandos convenientes da CLI (interface de linha de comando) do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras e os pontos de extremidade de serviço de VNet para gerenciar o servidor. Para obter uma visão geral do Banco de Dados do Azure para endpoints de serviço do MariaDB VNet, incluindo limitações, consulte [Banco de Dados do Azure para endpoints de serviço do VDB do servidor MariaDB](concepts-data-access-security-vnet.md). Pontos de extremidade de serviço de rede virtual estão disponíveis em todas as regiões com suporte para o banco de dados do Azure para MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Você precisa de um [banco de dados do Azure para o servidor MariaDB e o banco de dados](quickstart-create-mariadb-server-database-using-azure-cli.md).

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

## <a name="configure-vnet-service-endpoints"></a>Configurar pontos de extremidade de serviço de rede virtual

Os comandos [az network vnet](/cli/azure/network/vnet) são usados para configurar redes virtuais.

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az-account-set). Substitua a propriedade **id** da saída **logon az** para a sua assinatura no espaço reservado da ID da assinatura.

- A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.

Para proteger os recursos de serviço do Azure em uma VNet, o usuário deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" das sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md).

As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço VNet e Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). As duas assinaturas devem ter o provedor de recursos **Microsoft.Sql** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de configurá-los. **Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marca de serviço também se aplica aos bancos de dados do Banco de Dados SQL do Azure, Banco de Dados do Azure para MariaDB, PostgreSQL e MySQL. É importante observar ao aplicar a marca de serviço **Microsoft.Sql** a um ponto de extremidade do serviço VNet que ela configura o tráfego de ponto de extremidade de serviço para todos os serviços do Banco de Dados do Azure, incluindo o Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL, Banco de Dados do Azure para MariaDB, e Banco de Dados do Azure para servidores MySQL na sub-rede.

### <a name="sample-script"></a>Exemplo de script

Este script de amostra é usado para criar um Banco de Dados do Azure para MariaDB, criar um ponto de extremidade de serviço VNet, VNet e proteger o servidor na sub-rede com uma regra VNet. Neste script de exemplo, altere o nome de usuário administrador e a senha. Substitua a SubscriptionID usada nos comandos `az account set --subscription` pelo identificador de assinatura.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Limpar a implantação
Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md