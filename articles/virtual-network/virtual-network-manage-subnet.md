---
title: Adicionar, alterar ou excluir uma sub-rede de rede virtual do Azure | Microsoft Docs
description: Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou excluir uma sub-rede da rede virtual

Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual. 

Se você não estiver familiarizado com as redes virtuais, antes de adicionar, alterar ou excluir uma sub-rede recomendamos que você leia [Visão geral da rede virtual do Azure](virtual-networks-overview.md) e [Criar, alterar ou excluir uma rede virtual](virtual-network-manage-network.md). Todos os recursos do Azure implantados em uma rede virtual são implantados em uma sub-rede dentro de uma rede virtual. Normalmente, várias sub-redes são criadas dentro de uma rede virtual para:
- **Filtra o tráfego entre sub-redes**. É possível aplicar grupos de segurança de rede às sub-redes para filtrar tráfego de rede de entrada e saída para todos os recursos (como máquinas virtuais) que estão na rede virtual. Para saber mais sobre como criar um grupo de segurança de rede, consulte [Criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- **Controlar o roteamento entre sub-redes**. O Azure cria rotas padrão para que o tráfego seja roteado automaticamente entre sub-redes. Você pode substituir as rotas padrão do Azure, criando rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, consulte [Criar rotas definidas pelo usuário](virtual-network-create-udr-arm-ps.md). 

Este artigo explica como adicionar, alterar e excluir uma sub-rede de redes virtuais que foram criadas utilizando o modelo de implantação do Azure Resource Manager.
 
## <a name="before"></a>Antes de começar

Antes de iniciar as tarefas descritas neste artigo, complete os seguintes pré-requisitos:

- Caso não tenha experiência em trabalhar com redes virtuais, recomendamos que você examine o exercício em [Criar sua primeira rede virtual do Azure](virtual-network-get-started-vnet-subnet.md). Esse exercício pode ajudá-lo a se familiarizar melhor com as redes virtuais.
- Para saber mais sobre os limites para redes virtuais, examine [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Entre no portal do Azure, na ferramenta de linha de comando do Azure (CLI do Azure) ou no Azure PowerShell utilizando sua conta do Azure. Caso você não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se você pretende utilizar os comandos do PowerShell para completar as tarefas descritas, primeiro deverá [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de ter a versão mais recente dos cmdlets do Azure PowerShell instalada. Para obter ajuda sobre os comandos do PowerShell nos exemplos, digite `get-help <command> -full`.
- Se você pretende utilizar os comandos CLI do Azure para completar as tarefas descritas neste artigo, primeiro deverá:
    - [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de ter a versão mais recente da CLI do Azure instalada.
    - Use o Azure Cloud Shell. Em vez de instalar a CLI e suas dependências, você pode usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no ícone (**>_**) do Cloud Shell na parte superior do Portal do Azure. 

  Para obter ajuda com os comandos CLI do Azure, digite `az <command> --help`.

## <a name="create-subnet"></a>Adicionar uma sub-rede

Para adicionar uma sub-rede:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, clique em **Redes virtuais**.
3. Na folha **Redes virtuais** clique na rede virtual à qual você deseja adicionar uma sub-rede.
4. Na folha da rede virtual, clique em **Sub-redes**.
5. Clique em **+Sub-rede**.
6. Na folha **Adicionar sub-rede** insira os valores para os seguintes parâmetros:
    - **Nome**: o nome deve ser exclusivo na rede virtual.
    - **Intervalo de endereços**: o intervalo deve ser exclusivo dentro do espaço de endereços para a rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede dentro da rede virtual. O espaço de endereçamento deve ser especificado utilizando a notação CIDR (Roteamento Entre Domínios sem Classes). Por exemplo, em uma rede virtual com espaço de endereçamento 10.0.0.0/16, você pode definir um espaço de endereçamento de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, definir uma sub-rede com um intervalo de endereços /29 resulta em três endereços IP utilizáveis na sub-rede. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é adicionada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet) neste artigo.
    - **Grupo de segurança de rede**: opcionalmente, é possível associar um grupo de segurança de rede existente à sub-rede para controlar a filtragem de tráfego de rede de entrada e saída para a sub-rede. O grupo de segurança da rede deve existir na mesma assinatura e local da rede virtual. Além disso, deve ser criado utilizando o modelo de implantação Resource Manager. Para saber mais sobre como criar um grupo de segurança de rede, consulte [Grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabela de rotas**: opcionalmente, é possível associar uma tabela de rota existente à sub-rede para controlar o roteamento de tráfego da rede para outras redes. A tabela de rotas deve existir na mesma assinatura e localização da rede virtual. Além disso, deve ser criado utilizando o modelo de implantação Resource Manager. Para saber mais sobre como criar tabelas de rotas, consulte [Rotas definidas pelo usuário](virtual-network-create-udr-arm-ps.md).
    - **Usuários**: é possível controlar o acesso à sub-rede utilizando funções internas ou suas próprias funções personalizadas. Para saber mais sobre como atribuir funções e usuários para acessar a sub-rede, consulte [Utilizar atribuição de função para gerenciar o acesso aos recursos do Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Para adicionar a sub-rede à rede virtual que você selecionou, clique em **OK**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Alterar as configurações de sub-rede

É possível alterar grupos de segurança de rede, tabelas de rotas e acesso de usuário de uma sub-rede gerenciando recursos que estão em uma sub-rede. Para saber mais sobre essas configurações, em [Adicionar uma sub-rede](#create-subnet), veja a etapa 6. Caso queira alterar o espaço de endereçamento de uma sub-rede, primeiro você deverá excluir todos os recursos que estão na sub-rede. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja excluir. Para alterar o intervalo de endereços de uma sub-rede:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, clique em **Redes virtuais**.
3. Na folha **Redes virtuais** clique na rede virtual para a qual deseja alterar um intervalo de endereços de sub-rede.
4. Clique na sub-rede para a qual deseja alterar o intervalo de endereços.
5. Na folha de sub-rede, na caixa **Intervalo de endereços** digite o novo intervalo de endereços. O intervalo deve ser exclusivo dentro do espaço de endereçamento para a rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede dentro da rede virtual. O espaço de endereçamento deve ser especificado utilizando a notação CIDR. Por exemplo, em uma rede virtual com espaço de endereçamento 10.0.0.0/16, você pode definir um espaço de endereçamento de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, uma sub-rede com um intervalo de endereços /29 tem três endereços IP utilizáveis. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet) neste artigo.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Excluir uma sub-rede

Uma sub-rede somente pode ser excluída se não houver recursos na sub-rede. Se houver recursos na sub-rede, será necessário excluir os recursos na sub-rede antes de excluí-la. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja excluir. Para excluir uma sub-rede:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, clique em **Redes virtuais**.
3. Na folha **Redes virtuais**, clique na rede virtual da qual você deseja excluir uma sub-rede.
4. Na folha da rede virtual, em **SETTINGS**, clique em **Sub-redes**.
5. Na lista de sub-redes que aparece na folha de sub-redes, clique com o botão direito do mouse na sub-rede que deseja excluir, clique em **Excluir** e, em seguida, clique em **Sim** para excluir a sub-rede.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Próximas etapas

Para criar uma máquina virtual em uma sub-rede, veja [Criar uma rede virtual e implantar VMs na sub-rede](virtual-network-get-started-vnet-subnet.md#create-vms).
