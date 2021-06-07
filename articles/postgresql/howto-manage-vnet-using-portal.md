---
title: Usar regras de rede virtual-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Criar e gerenciar pontos de extremidade do serviço VNet e regras do banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489822"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
As regras e pontos de extremidade de serviços de VNet (rede virtual) estendem o espaço de endereço privado de uma rede virtual para seu servidor do Banco de Dados do Azure para PostgreSQL. Para obter uma visão geral dos pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL, confira [Pontos de extremidade de serviço de VNet do servidor do Banco de Dados do Azure para PostgreSQL](concepts-data-access-and-security-vnet.md). Os terminais de serviços da VNet estão disponíveis em todas as regiões suportadas para o Banco de Dados do Azure para PostgreSQL.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.
> No caso de emparelhamento de VNet, se o tráfego estiver fluindo através de um Gateway VNet comum com pontos de extremidade de serviço e deve fluir para o par, crie uma regra ACL/VNet para permitir que Máquinas Virtuais do Azure acessem o Banco de Dados do Azure para servidor PostgreMySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra de VNet e habilitar pontos de extremidade de serviço no portal do Azure

1. Na página do servidor PostgreSQL, no título Configurações, clique em **Segurança de Conexão** para abrir o painel Segurança de Conexão para o Banco de Dados do Azure para PostgreSQL. 

2. Verifique se o controle permitir acesso aos serviços do Azure está definido como **desativado**.

> [!Important]
> Se você deixar o controle definido como ON, seu servidor de banco de dados PostgreSQL do Azure aceitará a comunicação de qualquer sub-rede. Deixar o controle definido como ON pode ocasionar acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço Rede Virtual do Microsoft Azure, em coordenação com o recurso de regra de rede virtual do banco de dados do Azure para PostgreSQL, pode reduzir sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se você não tiver uma VNet existente, clique em **+ Criar nova rede virtual** para criar uma. Confira [Início Rápido: criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="portal do Azure clique em segurança de conexão":::

4. Insira um nome de regra de VNet, selecione a assinatura, a rede virtual e o nome da sub-rede. Depois, clique em **Habilitar**. Dessa forma, os pontos de extremidade de serviço da VNet serão habilitados na sub-rede usando a marcação de serviço **Microsoft.SQL**.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Portal do Azure – configurar a VNet":::

    A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

    Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.
    
    Para proteger os recursos de serviço do Azure em uma VNet, o usuário deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" das sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.
    
    Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md).
    
    As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço VNet e Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). As duas assinaturas devem ter o provedor de recursos **Microsoft.Sql** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de configurá-los. **Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marcação de serviço também é aplicável aos serviços Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e MySQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configura o tráfego do ponto de extremidade de serviço para todos os serviços de Banco de Dados do Azure, incluindo servidores do Banco de Dados SQL do Azure, do Banco de Dados do Azure para PostgreSQL e do Banco de Dados do Azure para MySQL na sub-rede. 
   > 

5. Depois de habilitá-lo, clique em **OK**. Você verá que os pontos de extremidade de serviço da VNet estão habilitados junto com uma regra de VNet.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="Pontos de extremidade de serviço de VNet habilitados e a regra de VNet criada":::

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível criar scripts para [Habilitar pontos de extremidade de serviço VNet e criar um regra VNET para o Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para PostgreSQL, consulte [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md