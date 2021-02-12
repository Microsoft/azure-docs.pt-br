---
title: Criar VM do Windows com rede acelerada-Azure PowerShell
description: Crie uma VM (máquina virtual) do Windows com rede acelerada para melhorar muito seu desempenho de rede.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919962"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Criar uma VM do Windows com rede acelerada usando Azure PowerShell

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Windows com rede acelerada.

> [!NOTE]
> Para usar a rede acelerada com uma máquina virtual Linux, confira [criar uma VM do Linux com rede acelerada](create-vm-accelerated-networking-cli.md).

Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, o que reduz a latência, a tremulação e a utilização da CPU para as cargas de trabalho de rede mais exigentes em tipos de VM com suporte. O diagrama a seguir ilustra como duas VMs se comunicam com e sem rede acelerada:

![Comunicação entre máquinas virtuais do Azure com e sem rede acelerada](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede.

> [!NOTE]
> Para saber mais sobre os comutadores virtuais, consulte [comutador virtual do Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Com a rede acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e, em seguida, é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas em hardware. Como a política é aplicada em hardware, a NIC pode encaminhar o tráfego de rede diretamente para a VM. A NIC ignora o host e o comutador virtual, enquanto mantém toda a política aplicada no host.

Os benefícios da rede acelerada só se aplicam à VM em que ela está habilitada. Para obter os melhores resultados, habilite esse recurso em pelo menos duas VMs conectadas à mesma rede virtual do Azure. Ao se comunicar entre redes virtuais ou conectar-se localmente, esse recurso tem um impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Benefícios

- **Latência mais baixa/pacotes maiores por segundo (PPS)**: a eliminação do comutador virtual do caminho de dados remove o tempo que os pacotes gastam no host para o processamento da política. Ele também aumenta o número de pacotes que podem ser processados dentro da VM.

- **Variação reduzida**: o processamento do comutador virtual depende da quantidade de política que precisa ser aplicada. Ele também depende da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM. O descarregamento também remove a comunicação de host para VM, todas as interrupções de software e todas as opções de contexto.

- **Redução da utilização da CPU**: ignorar o comutador virtual no host leva a menos utilização de CPU para processar o tráfego de rede.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

As distribuições a seguir têm suporte diretamente da galeria do Azure:

- **Windows Server 2019 Datacenter**
- **Datacenter do Windows Server 2016** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Limitações e restrições

### <a name="supported-vm-instances"></a>Instâncias de VM compatíveis

A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 2 ou mais vCPUs. Em instâncias que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 4 ou mais vCPUs. 

O suporte para rede acelerada pode ser encontrado na documentação de [tamanhos de máquinas virtuais](../virtual-machines/sizes.md) individuais. 

### <a name="custom-images"></a>Imagens personalizadas

Se você estiver usando uma imagem personalizada e sua imagem der suporte à rede acelerada, verifique se você tem os drivers necessários que funcionam com NICs Mellanox ConnectX-3 e ConnectX-4 LX no Azure.

### <a name="regions"></a>Regiões

A rede acelerada está disponível em todas as regiões globais do Azure e na nuvem do Azure governamental.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitando a rede acelerada em uma VM em execução

Um tamanho de VM com suporte sem rede acelerada habilitada só pode ter o recurso habilitado quando é interrompido e desalocado.

### <a name="deployment-through-azure-resource-manager"></a>Implantação por meio do Azure Resource Manager

As máquinas virtuais (clássicas) não podem ser implantadas com rede acelerada.

## <a name="vm-creation-using-the-portal"></a>Criação de VM usando o portal

Embora este artigo forneça etapas para criar uma VM com rede acelerada usando Azure PowerShell, você também pode [usar o portal do Azure para criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que permite a rede acelerada. Ao criar uma VM no portal, na página **criar uma máquina virtual** , escolha a guia **rede** . Essa guia tem uma opção para **rede acelerada**. Se você tiver escolhido um [sistema operacional com suporte](#supported-operating-systems) e um [tamanho de VM](#supported-vm-instances), essa opção será definida automaticamente como **ativado**. Caso contrário, a opção será definida como **off** e o Azure exibirá o motivo pelo qual ela não pode ser habilitada.

> [!NOTE]
> Somente sistemas operacionais com suporte podem ser habilitados por meio do Portal. Se você estiver usando uma imagem personalizada e sua imagem der suporte à rede acelerada, crie sua VM usando a CLI ou o PowerShell. 

Depois de criar a VM, você pode confirmar se a rede acelerada está habilitada. Siga estas instruções:

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas VMs. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Na lista máquina virtual, escolha sua nova VM.

3. Na barra de menus da VM, escolha **rede**.

Nas informações de interface de rede, ao lado do rótulo **rede acelerada** , o portal exibe **desabilitado** ou **habilitado** para o status de rede acelerada.

## <a name="vm-creation-using-powershell"></a>Criação de VM usando o PowerShell

Antes de prosseguir, instale [Azure PowerShell](/powershell/azure/install-az-ps) versão 1.0.0 ou posterior. Para localizar a versão atualmente instalada, execute `Get-Module -ListAvailable Az`. Se você precisar instalar ou atualizar o, instale a versão mais recente do módulo AZ do [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az). Em uma sessão do PowerShell, entre em uma conta do Azure usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Exemplos de nomes de parâmetro incluem *MyResource*, *myNic* e *myVM*.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O comando a seguir cria um grupo de recursos chamado *MyResource* Group no local *centralus* :

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O comando a seguir cria uma sub-rede chamada *mysubnet*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), com a sub-rede *mysubnet* .

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Crie uma regra do grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua a regra de segurança *Allow-RDP-All* a ele. Além da regra *Allow-RDP-All* , o grupo de segurança de rede contém várias regras padrão. Uma regra padrão desabilita todo o acesso de entrada da Internet. Depois de criada, a regra *Allow-RDP-All* é atribuída ao grupo de segurança de rede para que você possa se conectar remotamente à VM.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Associe o grupo de segurança de rede à sub-rede *mysubnet* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança de rede é eficaz para todos os recursos implantados na sub-rede.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar um adaptador de rede com rede acelerada

1. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Um endereço IP público é desnecessário se você não planeja acessar a VM pela Internet. No entanto, é necessário concluir as etapas neste artigo.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com rede acelerada habilitada e atribua o endereço IP público à interface de rede. O exemplo a seguir cria uma interface de rede chamada *myNic* na sub-rede *mysubnet* da rede virtual *myVnet* , atribuindo o endereço IP público *myPublicIp* a ela:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Criar uma VM e anexar a interface de rede

1. Defina suas credenciais de VM para a `$cred` variável usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), que solicita que você entre:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Defina sua VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O comando a seguir define uma VM chamada *myVM* com um tamanho de VM que dá suporte à rede acelerada (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Para obter uma lista de todos os tamanhos e características de VM, consulte [Tamanhos de VM do Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Crie o restante da configuração da VM com [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O comando a seguir cria uma VM do Windows Server 2016:

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. Anexe a interface de rede que você criou anteriormente com [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Crie sua VM com [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Confirme se o controlador Ethernet está instalado na VM do Windows

Depois de criar a VM no Azure, conecte-se à VM e confirme se o controlador Ethernet está instalado no Windows.

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas VMs. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Na lista máquina virtual, escolha sua nova VM.

3. Na página Visão geral da VM, se o **status** da VM estiver listado como **criando**, aguarde até que o Azure termine de criar a VM. O **status** será alterado para **em execução** após a conclusão da criação da VM.

4. Na barra de ferramentas visão geral da VM, selecione **conectar**  >  **RDP**  >  **baixar arquivo RDP**.

5. Abra o arquivo. RDP e entre na VM com as credenciais que você inseriu na seção [criar uma VM e anexar a interface de rede](#create-a-vm-and-attach-the-network-interface) . Se você nunca se conectou a uma VM do Windows no Azure, consulte [Conectar-se a máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Depois que a sessão da área de trabalho remota para sua VM for exibida, clique com o botão direito do mouse no botão Iniciar do Windows e escolha **Gerenciador de dispositivos**.

7. Na janela **Gerenciador de dispositivos** , expanda o nó **adaptadores de rede** .

8. Confirme se o **adaptador Ethernet de função virtual Mellanox ConnectX-3** aparece, conforme mostrado na imagem a seguir:

    ![Adaptador Ethernet de função virtual Mellanox ConnectX-3, novo adaptador de rede para rede acelerada, Gerenciador de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

A rede acelerada agora está habilitada para sua VM.

> [!NOTE]
> Se o adaptador Mellanox falhar ao iniciar, abra um prompt de administrador na sessão da área de trabalho remota e digite o seguinte comando:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Habilitar rede acelerada em VMs existentes

Se você tiver criado uma VM sem rede acelerada, poderá habilitar esse recurso em uma VM existente. A VM deve dar suporte à rede acelerada ao atender aos seguintes pré-requisitos, que também são descritos acima:

* A VM deve ter um tamanho com suporte para rede acelerada.
* A VM deve ser uma imagem da galeria do Azure com suporte (e a versão do kernel para Linux).
* Todas as VMs em um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais devem ser interrompidas ou desalocadas antes de habilitar a rede acelerada em qualquer NIC.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>VMs individuais e VMs em um conjunto de disponibilidade

1. Pare ou Desaloque a VM ou, se houver um conjunto de disponibilidade, todas as VMs no conjunto:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Ao criar uma VM individualmente, sem um conjunto de disponibilidade, você só precisa interromper ou desalocar a VM individual para habilitar a rede acelerada. Se a VM tiver sido criada com um conjunto de disponibilidade, você deverá parar ou desalocar todas as VMs contidas no conjunto de disponibilidade antes de habilitar a rede acelerada em qualquer uma das NICs, para que as VMs acabem em um cluster que ofereça suporte à rede acelerada. O requisito de parar ou desalocar é desnecessário se você desabilitar a rede acelerada, pois os clusters que dão suporte à rede acelerada também funcionam bem com NICs que não usam rede acelerada.

2. Habilite a rede acelerada na NIC de sua VM:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Reinicie a VM ou, se estiver em um conjunto de disponibilidade, todas as VMs no conjunto e confirme se a rede acelerada está habilitada:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual

Um conjunto de dimensionamento de máquinas virtuais é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.

1. Pare as VMs:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Atualize a propriedade de rede acelerada na interface de rede:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Defina as atualizações aplicadas como automáticas para que as alterações sejam imediatamente selecionadas:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Um conjunto de dimensionamento tem atualizações de VM que aplicam atualizações usando três configurações diferentes: automática, sem interrupção e manual. Nestas instruções, a política é definida como automática, portanto, o conjunto de dimensionamento pega as alterações imediatamente após a reinicialização.

4. Reinicie o conjunto de dimensionamento:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Depois de reiniciar, aguarde até que as atualizações sejam concluídas. Depois que as atualizações forem concluídas, a função virtual (VF) aparecerá dentro da VM. Verifique se você está usando um sistema operacional e tamanho de VM com suporte.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionando VMs existentes com rede acelerada

Se uma VM tiver a rede acelerada habilitada, você só poderá redimensioná-la para uma VM que dá suporte à rede acelerada.  

Uma VM com rede acelerada habilitada não pode ser redimensionada para uma instância de VM que não dá suporte à rede acelerada usando a operação de redimensionamento. Em vez disso, para redimensionar uma dessas VMs:

1. Pare ou Desaloque a VM. Para um conjunto de disponibilidade ou conjunto de dimensionamento, pare ou desaloque todas as VMs no conjunto de disponibilidade ou conjunto de dimensionamento.

2. Desabilite a rede acelerada na NIC da VM. Para um conjunto de disponibilidade ou conjunto de dimensionamento, desabilite a rede acelerada nas NICs de todas as VMs no conjunto de disponibilidade ou conjunto de dimensionamento.

3. Depois de desabilitar a rede acelerada, mova a VM, o conjunto de disponibilidade ou o conjunto de dimensionamento para um novo tamanho que não dê suporte à rede acelerada e reinicie-os.