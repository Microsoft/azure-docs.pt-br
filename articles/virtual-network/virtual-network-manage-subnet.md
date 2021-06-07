---
title: Adicionar, alterar ou excluir uma sub-rede de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre redes virtuais e como adicionar, alterar ou excluir uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: d591194f09e85c3e34ed4b904249df994a14bff5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558594"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou excluir uma sub-rede da rede virtual

Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual. Todos os recursos do Azure implantados em uma rede virtual são implantados em uma sub-rede dentro de uma rede virtual. Se você for novo em redes virtuais, poderá aprender mais sobre elas na [visão geral da rede virtual](virtual-networks-overview.md) ou concluindo um guia de [início rápido](quick-create-portal.md). Para saber mais sobre como gerenciar uma rede virtual, confira [criar, alterar ou excluir uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Em seguida, conclua uma dessas tarefas antes de iniciar as etapas em qualquer seção deste artigo: 

- **Usuários do portal**: entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia Azure Cloud Shell navegador, localize a lista suspensa **selecionar ambiente** e escolha **PowerShell** se ela ainda não estiver selecionada.

    Se você estiver executando o PowerShell localmente, use Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Também execute `Connect-AzAccount` para criar uma conexão com o Azure.

- **Usuários da CLI (interface de linha de comando) do Azure**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou execute a CLI do seu computador. Use CLI do Azure versão 2.0.31 ou posterior se você estiver executando o CLI do Azure localmente. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Também execute `az login` para criar uma conexão com o Azure.

A conta na qual você entra ou se conecta ao Azure com, deve ser atribuída à função de [função de colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir suas redes virtuais. Pesquise por **Redes virtuais** e selecione essa opção.

2. Selecione o nome da rede virtual à qual você deseja adicionar uma sub-rede.

3. Em **configurações**, selecione sub- **redes**  >  **sub-rede**.

4. Na caixa de diálogo **Adicionar sub-rede** , insira valores para as seguintes configurações:

    | Configuração | Descrição |
    | --- | --- |
    | **Nome** | O nome deve ser exclusivo na rede virtual. Para obter máxima compatibilidade com outros serviços do Azure, é recomendável usar uma letra como o primeiro caractere do nome. Por exemplo, o Gateway de Aplicativo do Azure não implantará em uma sub-rede cujo nome começa com um número. |
    | **Intervalo de endereços** | <p>O intervalo deve ser exclusivo dentro do espaço de endereçamento para a rede virtual. O intervalo não pode se sobrepor a outros intervalos de endereços de sub-rede na rede virtual. O espaço de endereçamento deve ser especificado utilizando a notação CIDR (Roteamento Entre Domínios sem Classes).</p><p>Por exemplo, em uma rede virtual com o espaço de endereço *10.0.0.0/16*, você pode definir um espaço de endereço de sub-rede de *10.0.0.0/22*. O menor intervalo que você pode especificar é */29*, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, a definição de uma sub-rede com um intervalo de endereços */29* resulta em três endereços IP utilizáveis na sub-rede.</p><p>Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é adicionada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet-settings).</p> |
    | **Grupo de segurança de rede** | Para filtrar o tráfego de rede de entrada e saída para a sub-rede, você pode associar um grupo de segurança de rede existente a uma sub-rede. O grupo de segurança da rede deve existir na mesma assinatura e local da rede virtual. Saiba mais sobre [grupos de segurança de rede](./network-security-groups-overview.md) e [como criar um grupo de segurança de rede](tutorial-filter-network-traffic.md). |
    | **Tabela de rotas** | Para controlar o roteamento de tráfego de rede para outras redes, você pode opcionalmente associar uma tabela de rotas existente a uma sub-rede. A tabela de rotas deve existir na mesma assinatura e localização da rede virtual. Saiba mais sobre o [Roteamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas](tutorial-create-route-table-portal.md). |
    | **Pontos de extremidade de serviço** | <p>Uma sub-rede pode, opcionalmente, ter um ou mais pontos de extremidade de serviço habilitados para ele. Para habilitar um ponto de extremidade de serviço para um serviço, selecione o serviço ou serviços para os quais deseja habilitar os pontos de extremidade de serviço na lista **Serviços**. O Azure configura o local automaticamente para um ponto de extremidade. Por padrão, o Azure configura os pontos de extremidade de serviço para a região da rede virtual. Para dar suporte a cenários de failover regional, o Azure configura automaticamente os pontos de extremidade para as [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) para o armazenamento do Azure.</p><p>Para remover um ponto de extremidade de serviço, desmarque o serviço para o qual deseja remover o ponto de extremidade de serviço. Para saber mais sobre os pontos de extremidade de serviço e os serviços para os quais eles podem ser habilitados, consulte [pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md). Depois de habilitar um ponto de extremidade de serviço para um serviço, você também precisa habilitar o acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se habilitar o ponto de extremidade de serviço para **Microsoft.Storage**, você também deverá habilitar o acesso à rede para todas as contas de Armazenamento do Azure a que deseja conceder acesso à rede. Para habilitar o acesso à rede para sub-redes para as quais um ponto de extremidade de serviço está habilitado, consulte a documentação do serviço individual para o qual você habilitou o ponto de extremidade de serviço.</p><p>Para validar que um ponto de extremidade de serviço está habilitado para uma sub-rede, visualize as [rotas efetivas](diagnose-network-routing-problem.md) para qualquer interface de rede na sub-rede. Ao configurar um ponto de extremidade, você verá uma rota *padrão* com os prefixos de endereço do serviço e um tipo de próximo salto de **VirtualNetworkServiceEndpoint**. Para saber mais sobre roteamento, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).</p> |
    | **Delegação de sub-rede** | Uma sub-rede pode, opcionalmente, ter uma ou mais delegações habilitadas para ela. A delegação de sub-rede fornece permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede usando um identificador exclusivo durante a implantação do serviço. Para delegar para um serviço, selecione o serviço que você deseja delegar na lista **Serviços**. |

5. Para adicionar a sub-rede à rede virtual que você selecionou, selecione **OK**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alterar as configurações de sub-rede

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir suas redes virtuais. Pesquise por **Redes virtuais** e selecione essa opção.

2. Selecione o nome da rede virtual que contém a sub-rede que você deseja alterar.

3. Em **configurações**, selecione **sub-redes**.

4. Na lista de sub-redes, selecione a sub-rede para a qual deseja alterar as configurações.

5. Na página sub-rede, altere qualquer uma das seguintes configurações:

    | Configuração | Descrição |
    | --- | --- |
    | **Intervalo de endereços** | Se nenhum recurso for implantado dentro da sub-rede, você poderá alterar o intervalo de endereços. Se houver recursos na sub-rede, você deverá mover os recursos para outra sub-rede ou excluí-los da sub-rede primeiro. As etapas necessárias para mover ou excluir um recurso variam de acordo com o recurso. Para saber como mover ou excluir recursos que estão em sub-redes, leia a documentação de cada um desses tipos de recursos. Consulte as restrições para o **intervalo de endereços** na etapa 4 de [Adicionar uma sub-rede](#add-a-subnet). |
    | **Usuários** | É possível controlar o acesso à sub-rede utilizando funções internas ou suas próprias funções personalizadas. Para saber mais sobre a atribuição de funções e usuários para acessar a sub-rede, consulte [atribuir funções do Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). |
    | **Grupo de segurança de rede** e **Tabela de rotas** | Consulte a etapa 4 de [Adicionar uma sub-rede](#add-a-subnet). |
    | **Pontos de extremidade de serviço** | <p>Consulte pontos de extremidade de serviço na etapa 4 de [Adicionar uma sub-rede](#add-a-subnet). Ao habilitar um ponto de extremidade de serviço para uma sub-rede existente, certifique-se de que nenhuma tarefa crítica está executando qualquer recurso na sub-rede. Pontos de extremidade de serviço alternam rotas em cada interface de rede na sub-rede. Os pontos de extremidade de serviço vão de usar a rota padrão com o prefixo de endereço *0.0.0.0/0* e o tipo do próximo salto da *Internet*, para usar uma nova rota com os prefixos de endereço do serviço e um tipo de próximo salto de *VirtualNetworkServiceEndpoint*.</p><p>Durante a troca, todas as conexões TCP abertas podem ser finalizadas. O ponto de extremidade de serviço não está habilitado até que o tráfego flua para o serviço para todas as interfaces de rede sejam atualizados com a nova rota. Para saber mais sobre roteamento, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).</p> |
    | **Delegação de sub-rede** | Consulte pontos de extremidade de serviço na etapa 4 de [Adicionar uma sub-rede](#add-a-subnet). A delegação de sub-rede pode ser modificada para ter nenhuma ou várias delegações habilitadas para ela. Se um recurso para um serviço já estiver implantado na sub-rede, a delegação de sub-rede não poderá ser adicionada ou removida até que todos os recursos do serviço sejam removidos. Para delegar para um serviço diferente, selecione o serviço que você deseja delegar na lista **Serviços**. |

6. Clique em **Salvar**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Uma sub-rede somente pode ser excluída se não houver recursos na sub-rede. Se os recursos estiverem na sub-rede, você deverá excluir esses recursos antes de excluir a sub-rede. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos que estão em sub-redes, leia a documentação de cada um desses tipos de recursos.

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir suas redes virtuais. Pesquise por **Redes virtuais** e selecione essa opção.

2. Selecione o nome da rede virtual que contém a sub-rede que você deseja excluir.

3. Em **configurações**, selecione **sub-redes**.

4. Na lista de sub-redes, selecione a sub-rede que você deseja excluir.

5. Selecione **excluir** e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network vnet sub-exclusão de sub-rede](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em sub-redes, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas na tabela a seguir:

|Ação                                                                   |   Nome                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Ler uma sub-rede de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma sub-rede da rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Excluir uma sub-rede da rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Unir uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Habilitar um ponto de extremidade de serviço para uma sub-rede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obter as máquinas virtuais em uma sub-rede       |

## <a name="next-steps"></a>Próximas etapas

- Criar uma rede virtual e sub-redes usando [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para redes virtuais