---
title: Adicionar ou remover interfaces de rede de VMs do Azure
description: Saiba como adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 43d3da49688a7daeb3ea4e0c1c3dba505dcd3b59
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508322"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais

Saiba como adicionar uma interface de rede existente ao criar uma VM (máquina virtual) do Azure. Aprenda também a adicionar ou remover interfaces de rede de uma VM existente no estado parado (desalocado). Uma interface de rede permite que uma VM do Azure se comunique com a Internet, com o Azure e com recursos locais. Uma VM tem uma ou mais interfaces de rede. 

Se você precisar adicionar, alterar ou remover endereços IP de um adaptador de rede, consulte [Gerenciar endereços IP de adaptador de rede](virtual-network-network-interface-addresses.md). Para criar, alterar ou excluir interfaces de rede, consulte [gerenciar interfaces de rede](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Conclua uma dessas tarefas antes de iniciar o restante deste artigo:

- **Usuários do portal**: entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia Azure Cloud Shell navegador, localize a lista suspensa **selecionar ambiente** e escolha **PowerShell** se ela ainda não estiver selecionada.

    Se você estiver executando o PowerShell localmente, use Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma conexão com o Azure.

- **Usuários da CLI (interface de linha de comando) do Azure**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou execute a CLI do seu computador. Use CLI do Azure versão 2.0.26 ou posterior se você estiver executando o CLI do Azure localmente. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Execute `az login` para criar uma conexão com o Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Adicionar adaptadores de rede existentes a uma nova VM

Quando você cria uma máquina virtual por meio do portal, o portal cria um adaptador de rede com as configurações padrão e anexa a interface de rede à VM para você. Você não pode usar o portal para adicionar adaptadores de rede existentes a uma nova VM ou para criar uma VM com várias interfaces de rede. Você pode fazer ambos usando a CLI ou o PowerShell. Certifique-se de se familiarizar com as [restrições](#constraints). Se você criar uma VM com vários adaptadores de rede, também deverá configurar o sistema operacional para usá-las corretamente depois que a VM for criada. Aprenda como configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para vários adaptadores de rede.

### <a name="commands"></a>Comandos

Antes de criar a VM, [crie uma interface de rede](virtual-network-network-interface.md#create-a-network-interface).

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Adicionar um adaptador de rede a uma VM existente

Para adicionar uma interface de rede à sua máquina virtual:

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar uma máquina virtual existente. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Selecione o nome da sua VM. A VM deve dar suporte ao número de adaptadores de rede que você deseja adicionar. Para descobrir a quantos adaptadores de rede cada tamanho de VM dá suporte, consulte os tamanhos no Azure para [VMs Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [VMs do Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Na barra de comandos da VM, selecione **parar** e, em seguida, **OK** na caixa de diálogo de confirmação. Em seguida, aguarde até que o **status** da VM seja alterado para **parado (desalocado)**.

4. Na barra de menus da VM, escolha **rede**  >  **anexar interface de rede**. Em seguida, em **anexar a interface de rede existente**, escolha a interface de rede que você deseja anexar e selecione **OK**.

    >[!NOTE]
    >A interface de rede que você selecionar não pode ter a rede acelerada habilitada, não pode ter um endereço IPv6 atribuído a ela e deve existir na mesma rede virtual com a interface de rede atualmente conectada à VM.

    Se você ainda não tem um adaptador de rede, deve primeiro criar um. Para isso, selecione **Criar interface de rede**. Para saber mais sobre como criar um adaptador de rede, consulte [Criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre as restrições adicionais durante a adição de adaptadores de rede a máquinas virtuais, consulte [Restrições](#constraints).

5. Na barra de menus da VM, escolha **visão geral**  >  **Iniciar** para reiniciar a máquina virtual.

Agora você pode configurar o sistema operacional da VM para usar várias interfaces de rede corretamente. Aprenda como configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para vários adaptadores de rede.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (Reference); [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência); [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Exibir as Interfaces de rede para uma VM

Você pode exibir os adaptadores de rede atualmente anexados a uma VM para saber mais sobre a configuração de cada adaptador de rede e os endereços IP atribuídos a cada adaptador de rede. 

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar uma máquina virtual existente. Pesquise por **Máquinas virtuais** e selecione essa opção.

    >[!NOTE]
    >Entre usando uma conta que é atribuída à função de proprietário, colaborador ou colaborador de rede para sua assinatura. Para saber mais sobre como atribuir funções às contas, veja [Funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Selecione o nome da VM para a qual você deseja exibir as interfaces de rede anexadas.

3. Na barra de menus da VM, selecione **rede**.

Para saber mais sobre as configurações de adaptador de rede e como alterá-las, consulte [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a um adaptador de rede, consulte [Gerenciar endereços IP de adaptador de rede](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[lista AZ VM NIC](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Remover um adaptador de rede de uma VM

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar uma máquina virtual existente. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Selecione o nome da VM para a qual você deseja exibir as interfaces de rede anexadas.

3. Na barra de ferramentas da VM, escolha **parar**.

4. Aguarde até que o **Status** da VM seja alterado para **Parado (desalocado)**.

5. Na barra de menus da VM, escolha **rede**  >  **desanexar interface de rede**.

6. Na caixa de diálogo **desanexar interface de rede** , selecione a interface de rede que você deseja desanexar. Depois, selecione **OK**.

    >[!NOTE]
    >Se apenas uma interface de rede estiver listada, você não poderá desanexá-la, pois uma máquina virtual sempre deve ter pelo menos um adaptador de rede anexado a ela.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[AZ VM NIC remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (referência); [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência); [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Restrições

- Uma VM deve ter pelo menos um adaptador de rede anexado.

- Uma VM só pode ter tantos adaptadores de rede anexados quantos o tamanho da VM der suporte. Para saber mais sobre quantos adaptadores de rede cada tamanho de VM dá suporte, confira os tamanhos no Azure para [VMs Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [VMs do Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos aceitam pelo menos dois adaptadores de rede.

- As interfaces de rede que você adicionar a uma VM não podem ser conectadas no momento a outra VM. Para saber mais sobre como criar adaptadores de rede, consulte [Criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface).

- No passado, você poderia adicionar adaptadores de rede somente a VMs com suporte para várias interfaces de rede e foram criadas com pelo menos duas interfaces de rede. Não foi possível adicionar um adaptador de rede a uma VM que foi criada com uma interface de rede, mesmo que o tamanho da VM tenha suporte para mais de uma interface de rede. Por outro lado, você só pode remover interfaces de rede de uma VM com pelo menos três adaptadores de rede, pois VMs criadas com a rede pelo menos dois adaptadores sempre precisam ter pelo menos dois adaptadores de rede. Essas restrições não se aplicam mais. Agora você pode criar uma VM com qualquer número de adaptadores de rede (até o número com suporte pelo tamanho da VM).

- Por padrão, a primeira interface de rede anexada a uma VM é a interface de rede *principal* . Todos os outros adaptadores de rede na VM são adaptadores de rede *secundários*.

- Você pode controlar a qual interface de rede você envia o tráfego de saída. No entanto, uma VM envia, por padrão, todo o tráfego de saída para o endereço IP atribuído à configuração de IP primário da interface de rede primária.

- No passado, todas as VMs dentro do mesmo conjunto de disponibilidade precisavam ter um único adaptador de rede ou vários adaptadores de rede. VMs com qualquer número de adaptadores de rede agora podem existir no mesmo conjunto de disponibilidade até o número com suporte pelo tamanho da VM. Porém, você pode adicionar uma VM a um conjunto de disponibilidade apenas quando ela é criada. Para saber mais sobre os conjuntos de disponibilidade, consulte [Gerenciamento da disponibilidade de VMs no Azure](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

- Você pode conectar adaptadores de rede na mesma VM para diferentes sub-redes em uma rede virtual. No entanto, as interfaces de rede devem estar conectadas à mesma rede virtual.

- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer adaptador de rede primário ou secundário para um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para o adaptador de rede primário podia ser adicionado a um pool de back-end. Para saber mais sobre endereços IP e configurações, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

- A exclusão de uma VM não exclui as interfaces de rede que estão anexadas a ela. Quando você exclui uma VM, os adaptadores de rede são desanexados da VM. Você pode adicionar essas interfaces de rede a diferentes VMs ou excluí-las.

- Alcançar o desempenho ideal documentado requer rede acelerada. Em alguns casos, você deve habilitar explicitamente a rede acelerada para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Próximas etapas

Para criar uma VM com vários adaptadores de rede ou endereços IP, consulte:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|