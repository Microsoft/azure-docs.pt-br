---
title: "Adicionar ou remover interfaces de rede de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 57f95b765b1b116814683a6643db16091c3041f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Adicionar ou remover interfaces de rede de máquinas virtuais

Saiba como adicionar um adaptador de rede existente ao criar uma VM ou adicionar ou remover adaptadores de rede de uma VM existente no estado parado (desalocado). Um adaptador de rede permite que uma VM (máquina virtual) do Azure comunique-se com a Internet, com o Azure e com recursos locais. Uma VM pode ter um ou mais adaptadores de rede. 

Se você precisar adicionar, alterar ou remover endereços IP de um adaptador de rede, leia o artigo [Gerenciar endereços IP de adaptador de rede](virtual-network-network-interface-addresses.md). Se você precisar criar, alterar ou excluir adaptadores de rede, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md).

## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Saiba mais sobre quantos adaptadores de rede cada VM Linux e Windows dá suporte examinando os artigos de tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Faça logon no [Portal](https://portal.azure.com) do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se usar comandos do PowerShell para concluir as tarefas neste artigo, [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você for usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no botão **>_** do Cloud Shell na parte superior do [Portal](https://portal.azure.com).

## <a name="about"></a>Sobre adaptadores de rede e VMs

Você pode adicionar (anexar) um adaptador de rede existente a uma VM ao criar a VM, desde que o adaptador de rede não esteja conectado no momento a outra VM. Você pode adicionar um adaptador de rede ou remover (desanexar) um adaptador de rede de uma VM existente, desde que a VM esteja no estado parado (desalocado). Se você criar uma VM usando o portal do Azure, o portal criará um adaptador de rede para você com as configurações padrão. O portal não permite que você:

- Especificar um adaptador de rede existente a adicionar ao criar a VM
- Criar uma VM com várias interfaces de rede
- Especifique um nome para o adaptador de rede (o portal cria o adaptador de rede com um nome padrão)

Você pode usar o Azure PowerShell ou a CLI para criar uma VM ou um adaptador de rede com todos os atributos anteriores para os quais você não pode usar o portal. Antes de concluir as tarefas nas seções a seguir, considere as seguintes restrições e comportamentos:

- Todos os tamanhos VM dão suporte a pelo menos dois adaptadores de rede, mas alguns tamanhos de VM dão suporte a mais de dois adaptadores de rede. No passado, alguns tamanhos de VM só davam suporte a um adaptador de rede. Para saber a quantos adaptadores de rede cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- No passado, adaptadores de rede podiam apenas ser adicionados a VMs que tinham suporte a vários adaptadores de rede e eram criadas com pelo menos dois adaptadores de rede. Você não podia adicionar um adaptador de rede a uma VM criada com um adaptador de rede, mesmo que o tamanho da VM desse suporte a vários adaptadores de rede. Por outro lado, você só pode remover interfaces de rede de uma VM com pelo menos três adaptadores de rede, pois VMs criadas com a rede pelo menos dois adaptadores sempre precisam ter pelo menos dois adaptadores de rede. Nenhuma dessas restrições se aplicam mais. Agora você pode criar uma VM com qualquer número de adaptadores de rede (até o número com suporte pelo tamanho da VM) e adicionar ou remover qualquer número de adaptadores de rede (de VMs no estado parado [desalocado]), desde que a VM sempre tenha pelo menos um adaptador de rede.
- Por padrão, o primeiro adaptador de rede em uma VM é definido como o adaptador de rede *primário*. Todos os outros adaptadores de rede na VM são adaptadores de rede *secundários*.
- Adaptadores de rede primários são atribuídos a um gateway padrão pelos servidores DHCP do Azure, mas interfaces de rede secundárias não são. Uma vez que os adaptadores de rede secundários não são atribuídos a um gateway padrão, eles não conseguem se comunicar com recursos fora da sub-rede por padrão. Para habilitar adaptadores de rede secundários em uma VM do Windows a comunicarem-se com recursos fora da sub-rede, adicione rotas ao sistema operacional usando o comando `route add` de uma linha de comando do Windows. Para VMs do Linux, uma vez que o comportamento padrão usa roteamento de host fraco, é recomendável restringir o tráfego para adaptadores de rede secundários a uma única sub-rede. Se você precisar de conectividade fora da sub-rede para adaptadores de rede secundários, habilite o roteamento baseado em política para garantir que os tráfegos de entrada e de saída usem o mesmo adaptador de rede.
- Por padrão, todo o tráfego de saída da VM é enviado pelo endereço IP atribuído à configuração de IP primária do adaptador de rede primário. Você controla qual endereço IP é usado para tráfego de saída no sistema operacional da VM, mas, por padrão, o adaptador de rede primário é utilizado.
- No passado, todas as VMs dentro do mesmo conjunto de disponibilidade precisavam ter um único adaptador de rede ou vários adaptadores de rede. VMs com qualquer número de adaptadores de rede agora podem existir no mesmo conjunto de disponibilidade até o número com suporte pelo tamanho da VM. Porém, você pode adicionar uma VM a um conjunto de disponibilidade apenas quando ela é criada. Para saber mais sobre os conjuntos de disponibilidade, leia o artigo [Gerenciamento da disponibilidade de VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Enquanto os adaptadores de rede na mesma VM podem ser conectados a diferentes sub-redes em uma rede VNet, os adaptadores de rede devem todos estar conectados à mesma VNet.
- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer adaptador de rede primário ou secundário para um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para o adaptador de rede primário podia ser adicionado a um pool de back-end. Para saber mais sobre endereços IP e configurações, consulte o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- Excluir uma VM não exclui os adaptadores de rede anexados a ele. Quando uma VM é excluída, os adaptadores de rede são desanexados da VM. Você pode adicionar os adaptadores de rede a diferentes VMs ou excluí-los.
- Se uma adaptador de rede tiver um endereço IPv6 privado atribuído a ele, você poderá anexá-lo a uma máquina virtual ao criá-la. Não é possível anexar um adaptador de rede com um endereço IPv6 atribuído a uma VM depois que a VM é criada. Se você anexar um adaptador de rede com um endereço IPv6 privado atribuído durante a criação de uma máquina virtual, você somente poderá anexar esse adaptador de rede à máquina virtual, independentemente de quantos adaptadores de rede o tamanho da VM suporta. Confira [Endereços IP do adaptador de rede](virtual-network-network-interface-addresses.md) para saber mais sobre como atribuir endereços IP a adaptadores de rede.

## <a name="vm-create"></a>Adicionar adaptadores de rede existentes a uma nova VM

Ao criar uma VM por meio do portal, o portal cria um adaptador de rede com configurações padrão e anexa-o à VM para você. Você não pode adicionar adaptadores de rede existente a uma nova VM nem criar uma VM com vários adaptadores de rede usando o portal do Azure. Você pode fazer essas duas ações usando a CLI ou o PowerShell. Você pode adicionar tantos adaptadores de rede a uma VM quantos o tamanho da VM que você estiver criando suporte. Para saber mais sobre a quantos adaptadores de rede cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Os adaptadores de rede que você adicionar a uma VM no momento não podem ser anexados a outra VM. Para saber mais sobre a criação de adaptadores de rede, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md#create-a-network-interface).

> [!WARNING]
> Se um adaptador de rede tiver um endereço IPv6 privado atribuído a ele, você só poderá adicioná-lo à máquina virtual ao criá-la. Você não pode anexar mais de uma interface de rede à máquina virtual ao criá-la, ou depois que a máquina virtual for criada, desde que um endereço IPv6 seja atribuído a uma interface de rede anexada a uma máquina virtual. Confira [Endereços IP do adaptador de rede](virtual-network-network-interface-addresses.md) para saber mais sobre como atribuir endereços IP a adaptadores de rede.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicionar um adaptador de rede existente a uma VM existente

Você pode adicionar a uma VM tantos adaptadores de rede o tamanho da respectiva VM suportar. Para saber a quantos adaptadores de rede cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A VM à qual você deseja adicionar um adaptador de rede deve dar suporte ao número de adaptadores de rede que você deseja adicionar e deve estar no estado parado (desalocado). Os adaptadores de rede que você deseja adicionar não podem estar anexados a outra uma VM no momento. Você não pode adicionar adaptadores de rede a uma VM existente usando o portal do Azure. Você deve usar a CLI ou o PowerShell para adicionar adaptadores de rede a uma VM existente. 

> [!WARNING]
> Se um adaptador de rede tiver um endereço IPv6 privado atribuído a ele, ele não poderá ser adicionado a uma máquina virtual existente. Você só pode adicionar um adaptador de rede com um endereço IPv6 privado atribuído a uma máquina virtual quando criá-la. Confira [Endereços IP do adaptador de rede](virtual-network-network-interface-addresses.md) para saber mais sobre como atribuir endereços IP a adaptadores de rede.

|Ferramenta|Command|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referência) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Exibir adaptadores de rede para uma VM

Você pode exibir os adaptadores de rede atualmente anexados a uma VM para saber mais sobre a configuração de cada adaptador de rede e os endereços IP atribuídos a cada adaptador de rede. 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de Proprietário, Colaborador ou Colaborador de rede para sua assinatura. Para saber mais sobre como atribuir funções às contas, veja [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *máquinas virtuais*. Quando **máquinas virtuais** aparecerem nos resultados da pesquisa, clique nelas.
3. Na folha **Máquinas virtuais** que aparece, clique no nome da VM para a qual você deseja exibir os adaptadores de rede.
4. Na seção **CONFIGURAÇÕES** da folha da máquina virtual que é exibida para a VM selecionada, clique em **Interfaces de rede**. Para saber mais sobre as configurações de adaptador de rede e como alterá-las, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a um adaptador de rede, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Remover um adaptador de rede de uma VM

A VM da qual você deseja remover (ou desanexar) um adaptador de rede deve estar no estado parado (desalocado) e ter pelo menos dois adaptadores de rede anexados. Você pode remover qualquer adaptador de rede, mas a VM sempre deve ter pelo menos um adaptador de rede anexado. Se você remover um adaptador de rede principal, o Azure atribuirá o atributo primário ao adaptador de rede que ficou anexado à VM por mais tempo. 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de Proprietário, Colaborador ou Colaborador de rede para sua assinatura. Para saber mais sobre como atribuir funções às contas, veja [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *máquinas virtuais*. Quando **máquinas virtuais** aparecerem nos resultados da pesquisa, clique nelas.
3. Na folha **Máquinas virtuais** que aparece, clique no nome da VM da qual você deseja remover os adaptadores de rede.
4. Na seção **CONFIGURAÇÕES** da folha da máquina virtual que é exibida para a VM selecionada, clique em **Interfaces de rede**. Para saber mais sobre as configurações de adaptador de rede e como alterá-las, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a um adaptador de rede, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).
5. Na folha de interfaces de rede que aparece, clique no **...** à direita da interface de rede que você deseja desanexar.
6. Clique em **Desanexar**. Se houver apenas uma interface de rede conectada à máquina virtual, o **desanexar** opção não está disponível. Clique em **Sim** na caixa de confirmação que aparece.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referência) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM com vários adaptadores de rede ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
