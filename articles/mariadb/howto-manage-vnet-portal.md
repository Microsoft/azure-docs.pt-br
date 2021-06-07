---
title: Gerenciar pontos de extremidade de VNet-portal do Azure-banco de dados do Azure para MariaDB
description: Crie e gerencie o Banco de Dados do Azure para endpoints e regras de serviço do MariaDB VNet usando o portal do Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665014"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Crie e gerencie o Banco de Dados do Azure para endpoints de serviço do VDB da MariaDB e regras de VNet usando o portal do Azure

Pontos de extremidade e regras de serviços da Rede Virtual (VNet) estendem o espaço de endereçamento privado de uma Rede Virtual ao seu Banco de Dados do Azure para MariaDB. Para obter uma visão geral do Banco de Dados do Azure para endpoints de serviço do MariaDB VNet, incluindo limitações, consulte [Banco de Dados do Azure para endpoints de serviço do VDB do servidor MariaDB](concepts-data-access-security-vnet.md). Pontos de extremidade de serviço de rede virtual estão disponíveis em todas as regiões com suporte para o banco de dados do Azure para MariaDB.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Crie uma regra VNet e ative os pontos de extremidade do serviço

1. Na página do servidor MariaDB, no cabeçalho Configurações, clique em **Segurança de conexão** para abrir o painel Segurança de conexão do Banco de Dados do Azure para MariaDB.

2. Verifique se o controle permitir acesso aos serviços do Azure está definido como **desativado**.

> [!Important]
> Se você defini-lo como ON, o servidor de banco de dados MariaDB do Azure aceitará a comunicação de qualquer sub-rede. Deixar o controle definido como ON pode ocasionar acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço Rede Virtual do Microsoft Azure, em coordenação com o recurso de regra de rede virtual do banco de dados do Azure para MariaDB, em conjunto pode reduzir sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se você não tiver uma VNet existente, clique em **+ Criar nova rede virtual** para criar uma. Confira [Início Rápido: criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Insira um nome de regra de VNet, selecione a assinatura, a rede virtual e o nome da sub-rede. Depois, clique em **Habilitar**. Dessa forma, os pontos de extremidade de serviço da VNet serão habilitados na sub-rede usando a marcação de serviço **Microsoft.SQL**.

   ![Portal do Azure – configurar a VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

   Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.
    
   Para proteger os recursos de serviço do Azure em uma VNet, o usuário deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" das sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.
    
   Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md).
    
   As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço VNet e Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). As duas assinaturas devem ter o provedor de recursos **Microsoft.Sql** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de configurá-los. **Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marca de serviço também se aplica aos bancos de dados do Banco de Dados SQL do Azure, Banco de Dados do Azure para MariaDB, PostgreSQL e MySQL. É importante observar ao aplicar a marca de serviço **Microsoft.Sql** a um ponto de extremidade do serviço VNet que ela configura o tráfego de ponto de extremidade de serviço para todos os serviços do Banco de Dados do Azure, incluindo o Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL, Banco de Dados do Azure para MariaDB, e Banco de Dados do Azure para servidores MySQL na sub-rede.
   > 

5. Depois de habilitá-lo, clique em **OK**. Você verá que os pontos de extremidade de serviço da VNet estão habilitados junto com uma regra de VNet.

   ![Pontos de extremidade de serviço de VNet habilitados e a regra de VNet criada](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Configurando o SSL no banco de dados do Azure para MariaDB](howto-configure-ssl.md)
- Da mesma forma, você pode gerar um script para [VNet Habilitar pontos de extremidade de serviço e criar uma regra de rede virtual para o banco de dados do Azure para MariaDB usando a CLI do Azure](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md