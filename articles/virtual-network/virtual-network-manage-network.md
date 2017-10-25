---
title: Criar, alterar, ou excluir uma rede virtual do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir uma rede virtual no Azure.
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
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou excluir uma rede virtual

Saiba como criar e excluir uma rede virtual e alterar as configurações, como servidores DNS e espaços de endereço IP para uma rede virtual existente.

Uma rede virtual é uma representação da sua própria rede na nuvem. Uma rede virtual é um isolamento lógico da nuvem do Azure que é dedicada à sua assinatura do Azure. Para cada rede virtual que você cria, você pode:
- Escolha um espaço de endereço para atribuir. Um espaço de endereços consiste em um ou mais intervalos de endereços que são definidos usando a notação de roteamento entre domínios (CIDR), como 10.0.0.0/16.
- Escolha usar o servidor DNS fornecido pelo Azure ou usar seu próprio servidor DNS. Todos os recursos que estão conectados à rede virtual são atribuídos a esse servidor DNS para resolver nomes dentro da rede virtual.
- Segmente a rede virtual em sub-redes, cada uma com seu próprio intervalo de endereços, dentro do espaço de endereços da rede virtual. Para saber como criar, alterar e excluir sub-redes, leia consulte [Adicionar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md).

Esse artigo explica como criar, alterar e excluir redes virtuais usando o modelo de implantação do Azure Resource Manager.

## <a name="before"></a>Antes de começar

Antes de iniciar as tarefas descritas neste artigo, complete os seguintes pré-requisitos:

- Caso não tenha experiência em trabalhar com redes virtuais, recomendamos que você examine o exercício em [Criar sua primeira rede virtual do Azure](virtual-network-get-started-vnet-subnet.md). Esse exercício pode ajudá-lo a se familiarizar melhor com as redes virtuais.
- Para saber mais sobre os limites para redes virtuais, examine [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Entre no portal do Azure, na ferramenta de linha de comando do Azure (CLI do Azure) ou no Azure PowerShell utilizando sua conta do Azure. Caso você não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se você pretende utilizar os comandos do PowerShell para completar as tarefas descritas, primeiro deverá [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de ter a versão mais recente dos cmdlets do Azure PowerShell instalada. Para obter ajuda sobre os comandos do PowerShell nos exemplos, digite `get-help <command> -full`.
- Se você pretende utilizar os comandos CLI do Azure para completar as tarefas descritas neste artigo, primeiro deverá [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de ter a versão mais recente da CLI do Azure instalada. Para obter ajuda com os comandos CLI do Azure, digite `az <command> --help`.


## <a name="create-vnet"></a>Criar uma rede virtual

Para criar uma rede virtual:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Clique em **Nova** > **Rede** > **Rede Virtual**.
3. Na folha **Rede virtual**, na caixa **Selecionar um modelo de implantação**, deixe **Resource Manager** selecionado e, depois, clique em **Criar**.
4. Na folha **Criar rede virtual**, insira ou selecione valores para as seguintes configurações e, depois, clique em **Criar**:
    - **Nome**: O nome deve ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) em que você optar por criar a rede virtual. Você não pode alterar o nome depois de criar a rede virtual. Você pode criar várias redes virtuais ao longo do tempo. Para sugestões de nomenclaturas, consulte [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Seguir uma convenção de nomenclatura pode ajudar a tornar mais fácil de gerenciar várias redes virtuais.
    - **Espaço de endereço**: Especifique o espaço de endereço na notação CIDR. O espaço de endereço definido pode ser público ou privado (RFC 1918). Se você definir o espaço de endereço como público ou privado, o espaço de endereço é acessível somente de dentro da rede virtual, de redes virtuais interconectadas e de quaisquer redes locais que você se conectou à rede virtual. Não é possível adicionar os seguintes espaços de endereço:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (Broadcast)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (DNS interno)

      Embora seja possível definir apenas um espaço de endereço quando você cria a rede virtual, você pode adicionar mais espaços de endereço depois de criar a rede virtual. Para saber como adicionar um espaço de endereço para uma rede virtual existente, consulte [Adicionar ou remover um espaço de endereço](#add-address-spaces) neste artigo.

      >[!WARNING]
      >Se uma rede virtual tem espaços de endereço que sobrepõem a outra rede virtual ou rede local, as duas redes não podem ser conectadas. Antes de definir um espaço de endereço, considere se você deseja se conectar a rede virtual para outras redes virtuais ou redes locais no futuro.
      >
      >

    - **Nome da sub-rede**: O nome da sub-rede deve ser exclusivo na rede virtual. Você não pode alterar o nome da sub-rede após a criação da sub-rede. O portal requer que você defina uma sub-rede ao criar uma rede virtual, mesmo que uma rede virtual não precise ter nenhuma sub-rede. No portal, você pode definir apenas uma sub-rede ao criar uma rede virtual. Você pode adicionar mais sub-redes na rede virtual mais tarde, depois de criar a rede virtual. Para adicionar uma sub-rede à uma rede virtual, consulte [Criar uma sub-rede](virtual-network-manage-subnet.md#create-subnet) na [Criar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md). Você pode criar uma rede virtual que tem várias sub-redes usando a CLI do Azure ou o PowerShell.

      >[!TIP]
      >Às vezes, administradores criam sub-redes diferentes para filtrar ou controlar o roteamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como você deseja filtrar e rotear o tráfego entre suas sub-redes. Para saber mais sobre a filtragem de tráfego entre sub-redes, consulte [Grupos de segurança de rede](virtual-networks-nsg.md). O Azure faz o roteamento de tráfego entre as sub-redes automaticamente, mas você pode substituir as rotas padrão do Azure. Para saber como substituir o roteamento de tráfego de sub-rede padrão do Azure, consulte [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
      >

    - **Intervalo de endereços de sub-rede:** O intervalo deve estar dentro do espaço de endereço inserido para a rede virtual. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, uma rede virtual com um intervalo de endereços de sub-rede de /29 tem apenas três endereços IP utilizáveis. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet) em [Adicionar, alterar, ou excluir sub-redes](virtual-network-manage-subnet.md).
    - **Assinatura:** Selecione uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Você não pode usar a mesma rede virtual em mais de uma assinatura do Azure. No entanto, você pode conectar uma rede virtual em uma assinatura para redes virtuais em outras assinaturas. Para se conectar a redes virtuais em assinaturas diferentes, use o Gateway de VPN do Azure ou o emparelhamento de rede virtual. Qualquer recurso do Azure que você conectar à rede virtual deve ser na mesma assinatura da rede virtual.
    - **Grupo de recursos**: Selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Um recurso do Azure que você conecta à rede virtual pode ser no mesmo grupo de recursos da rede virtual ou em grupo de recursos diferente.
    - **Local:** Selecione um [local](https://azure.microsoft.com/regions/) do Azure, também conhecido como região. Uma rede virtual pode estar em um só local do Azure. No entanto, você pode conectar uma rede virtual em um local para uma rede virtual em outro local usando um gateway de VPN. Qualquer recurso do Azure que você conectar à rede virtual deve ser no mesmo local da rede virtual.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Exibir configurações e redes virtuais

Para exibir configurações e redes virtuais:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, clique em **Redes virtuais**.
3. Na folha **Redes virtuais**, clique na rede virtual cujas configurações você deseja exibir.
4. As configurações a seguir são listadas na folha para a rede virtual selecionada:
    - **Visão geral:** Fornece informações sobre a rede virtual, incluindo espaço de endereço e servidores DNS. A captura de tela a seguir mostra as configurações de visão geral de uma rede virtual chamada **MyVNet**:

        ![Visão geral da interface de rede](./media/virtual-network-manage-network/vnet-overview.png)

      Na folha **visão geral**, você pode mover uma rede virtual para uma assinatura ou grupo de recursos diferente. Para saber como mover uma rede virtual, consulte [Mover recursos para um outro grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure. Todos os recursos que estão conectados à rede virtual devem mover-se com a rede virtual.
    - **Espaço de endereço:** Os espaços de endereço que são atribuídos à rede virtual são listados. Para saber como adicionar e remover espaços de endereço, conclua as etapas [Adicionar ou remover um espaço de endereço](#address-spaces) deste artigo.
    - **Dispositivos conectados**: Todos os recursos que estão conectados à rede virtual são listados. Na captura de tela anterior, três interfaces de rede e um balanceador de carga são conectados à rede virtual. Os novos recursos que você criar e conectar à rede virtual são listados. Se você excluir um recurso que foi conectado à rede virtual, ele não aparecerá mais na lista.
    - **Sub-redes:** Uma lista de sub-redes que existem na rede virtual é exibida. Para saber como adicionar e remover uma sub-rede, consulte [Criar uma sub-rede](virtual-network-manage-subnet.md#create-subnet) e [Excluir uma sub-rede](virtual-network-manage-subnet.md#delete-subnet) em [Adicionar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md).
    - **Servidores DNS:** Você pode especificar se o servidor DNS interno do Azure ou o servidor DNS personalizado fornece a resolução de nome para dispositivos que estão conectados à rede virtual. Quando você cria uma rede virtual usando o portal do Azure, os servidores DNS do Azure são usados para resolução de nomes em uma rede virtual por padrão. Para modificar os servidores DNS, conclua as etapas [Adicionar, alterar ou remover servidores DNS](#dns-servers) deste artigo.
    - **Emparelhamentos:** Se houver emparelhamentos existentes na assinatura, eles serão listados aqui. Você pode exibir configurações para emparelhamentos existentes, ou criar, alterar ou excluir emparelhamentos. Para saber mais sobre emparelhamento, consulte [Emparelhamento de rede virtual](virtual-network-peering-overview.md).
    - **Propriedades:** Exibe configurações sobre a rede virtual, incluindo a ID do recurso da rede virtual e a assinatura em que ela está.
    - **Diagrama:** O diagrama apresenta uma representação visual de todos os dispositivos que estão conectados à rede virtual. O diagrama tem algumas informações importantes sobre os dispositivos. Para gerenciar um dispositivo nesta exibição, no diagrama, clique no dispositivo.
    - **Configurações comuns do Azure**: Para saber mais sobre configurações comuns do Azure, consulte as seguintes informações:
        *   [Log de atividade](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Marcas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Adicionar ou remover um espaço de endereço

Você pode adicionar e remover espaços de endereço para uma rede virtual. Um espaço de endereço deve ser especificado na notação CIDR e não pode se sobrepor a outros espaços de endereços dentro da mesma rede virtual. Os espaços de endereço definidos podem ser públicos ou privados (RFC 1918). Se você definir o espaço de endereço como público ou privado, o espaço de endereço é acessível somente de dentro da rede virtual, de redes virtuais interconectadas e de quaisquer redes locais que você se conectou à rede virtual. Não é possível adicionar os seguintes espaços de endereço:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Broadcast)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (DNS interno)

Para adicionar ou remover um espaço de endereço:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, selecione **Redes virtuais**.
3. Na folha **Redes virtuais** clique na rede virtual para a qual deseja alterar ou remover um espaço de endereço.
4. Na folha da rede virtual, em **CONFIGURAÇÕES**, clique em **Espaço de endereço**.
5. Na folha para o espaço de endereço, conclua uma das seguintes opções:
    - **Adicionar um espaço de endereço:** Digite o novo espaço de endereço. O espaço de endereço não pode se sobrepor a um espaço de endereço existente que está definido para a rede virtual.
    - **Remover um espaço de endereço:** Clique com o botão direito em um espaço de endereço, em seguida clique em **Remover**. Se existir uma sub-rede no espaço de endereços, você não poderá remover o espaço de endereços. Para remover um espaço de endereço, você deve primeiro excluir todas as sub-redes (e quaisquer recursos conectados às sub-redes) que existam no espaço de endereço.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|Somente Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Adicionar, alterar ou remover um servidor DNS

Todas as VMs que estão conectadas ao registro da rede virtual com os servidores DNS que você especificar para a rede virtual. Eles também usam o servidor DNS especificado para resolução de nome. Cada NIC (interface de rede) em uma VM pode ter suas próprias configurações do servidor DNS. Se uma NIC tiver suas próprias configurações de servidor DNS, elas substituirão as configurações do servidor DNS para a rede virtual. Para saber mais sobre as configurações de DNS da NIC, consulte [Configurações e tarefas da interface de rede](virtual-network-network-interface.md#change-dns-servers). Para saber mais sobre a resolução de nomes para VMs e instâncias de função de Serviços de Nuvem do Azure, consulte [Resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Entre no [portal](https://portal.azure.com) com uma conta que é atribuída permissões para a função Colaborador de Rede (no mínimo) para sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, selecione **Redes virtuais**.
3. Na folha **Redes virtuais**, clique na rede virtual que você deseja alterar as configurações de DNS.
4. Na folha da rede virtual, em **SETTINGS**, clique em **servidores DNS**.
5. Selecione uma das opções a seguir na folha que lista os servidores DNS:
    - **Padrão (fornecido pelo Azure):** Todos os nomes de recursos e endereços IP privados são registrados automaticamente para os servidores DNS do Azure. Você pode resolver nomes entre quaisquer recursos que estejam conectados à mesma rede virtual. Você não pode usar essa opção para resolver nomes entre redes virtuais. Para resolver nomes em outras redes virtuais, você deve usar um servidor DNS personalizado.
    - **Personalizado:** você pode adicionar um ou mais servidores, até o limite do Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Você tem as seguintes opções:
        - **Adicionar um endereço**: Adiciona o servidor à sua lista de servidores DNS da rede virtual. Essa opção também registra o servidor DNS com o Azure. Se você já tiver registrado um servidor DNS com o Azure, poderá selecionar esse servidor DNS na lista.
        - **Remover um endereço:** Ao lado do servidor que você deseja remover, clique no **X**. Excluir o servidor remove o servidor somente dessa lista de rede virtual. O servidor DNS permanece registrado no Azure para que suas outras redes virtuais possam usá-lo.
        - **Reordenar endereços do servidor DNS**: é importante verificar se os servidores DNS estão listados na ordem correta para seu ambiente. As listas de servidores DNS são usadas na ordem em que foram especificadas. Eles não funcionam como uma configuração de round-robin. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS, não importa se ele está funcionando corretamente. Remova todos os servidores DNS listados e, em seguida, adicione-os de volta na ordem que desejar.
        - **Alterar um endereço**: Destaque o servidor DNS na lista, e em seguida digite o novo nome.
6. Clique em **Salvar**.
7. Reinicie as VMs que estão conectadas à rede virtual, para que as novas configurações do servidor DNS sejam atribuídas a elas. As VMs continuam a usar suas configurações de DNS atuais até que elas sejam reiniciadas.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Excluir uma rede virtual

Você pode excluir uma rede virtual somente se não houver recursos conectados a ela. Se houver recursos conectados a qualquer sub-rede na rede virtual, primeiro exclua os recursos que estão conectados a todas as sub-redes na rede virtual. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos conectados as sub-redes, leia a documentação para cada tipo de recurso que deseja excluir. Para excluir uma rede virtual:

1. Entre no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Para saber mais sobre como atribuir funções e permissões às contas, consulte [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa do portal, insira **redes virtuais**. Nos resultados da pesquisa, clique em **Redes virtuais**.
3. Na folha **Redes virtuais**, selecione a rede virtual que você deseja excluir.
4. Na folha da rede virtual, para confirmar que não há nenhum dispositivo conectado à rede virtual, em **CONFIGURAÇÕES**, clique em **Dispositivos conectados**. Se houver dispositivos conectados, você deve excluí-los antes de excluir a rede virtual. Se não houver dispositivos conectados, clique em **Visão geral**.
5. Na parte superior da folha, clique no ícone **Excluir**.
6. Para confirmar a exclusão da rede virtual, clique em **Sim**.


**Comandos**

|Ferramenta|Command|
|---|---|
|CLI do Azure|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Próximas etapas

- Para criar uma VM e, depois, conectá-la a uma rede virtual, consulte [Criar uma rede virtual e conectar VMs](virtual-network-get-started-vnet-subnet.md#create-vms).
- Para filtrar o tráfego de rede entre as sub-redes em uma rede virtual, consulte [Criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- Para emparelhar uma rede virtual para outra rede virtual, consulte [Criar um emparelhamento de rede virtual](virtual-network-create-peering.md#portal).
- Para saber mais sobre as opções para conectar uma rede virtual a uma rede local, consulte [Sobre o gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
