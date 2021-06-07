---
title: Gerenciar pontos de extremidade de VNet-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como criar e gerenciar regras e pontos de extremidade de serviços VNET do Banco de Dados do Azure para MySQL usando a linha de comando CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 237b9bc4d7ac6366a67accb31fdf3c80c778b5d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94636751"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Criar e gerenciar pontos de extremidade de serviços VNet do Banco de Dados do Azure para MySQL usando a CLI do Azure
As regras e pontos de extremidade de serviços de VNet (rede virtual) estendem o espaço de endereço privado de uma rede virtual para seu servidor do Banco de Dados do Azure para MySQL. Usando comandos convenientes da CLI (interface de linha de comando) do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras e os pontos de extremidade de serviço de VNet para gerenciar o servidor. Para obter uma visão geral dos pontos de extremidade de serviço de VNet do Banco de Dados do Azure para MySQL, confira [Pontos de extremidade de serviço de VNet do servidor do Banco de Dados do Azure para MySQL](concepts-data-access-and-security-vnet.md). Os terminais de serviço da VNet estão disponíveis em todas as regiões suportadas para o Banco de Dados do Azure para MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Você precisa de um banco de dados do [Azure para servidor MySQL e banco de dados](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.
> No caso de emparelhamento VNet, se o tráfego estiver fluindo através de um Gateway VNet comum com pontos de extremidade de serviço e deve fluir para o par, crie uma regra ACL/VNet para permitir que Máquinas Virtuais do Azure acessem o Banco de Dados do Azure para Servidor MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Configurar pontos de extremidade de serviço de VNet para Banco de Dados do Azure para MySQL
Os comandos [az network vnet](/cli/azure/network/vnet) são usados para configurar redes virtuais.

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az-account-set). Substitua a propriedade **ID** da saída de **logon AZ** para sua assinatura no espaço reservado da ID de assinatura.

- A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.

Para proteger os recursos de serviço do Azure em uma VNet, o usuário deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" das sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md).

As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço VNet e Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). As duas assinaturas devem ter o provedor de recursos **Microsoft.Sql** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de executar o script de exemplo abaixo ou configurar pontos de extremidade de serviço. **Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marcação de serviço também é aplicável aos serviços Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e MySQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configura o tráfego do ponto de extremidade de serviço para todos os serviços de Banco de Dados do Azure, incluindo servidores do Banco de Dados SQL do Azure, do Banco de Dados do Azure para PostgreSQL e do Banco de Dados do Azure para MySQL na sub-rede. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exemplo de script para criar um Banco de Dados do Azure para MySQL, uma VNet, um ponto de extremidade de serviço de VNet e para proteger o servidor da sub-rede com uma regra de VNet
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha. Substitua a SubscriptionID usada nos comandos `az account set --subscription` pelo identificador de assinatura.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
