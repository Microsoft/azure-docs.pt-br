---
title: 'Tutorial: Criar e gerenciar um conjunto de dimensionamento de VM do Azure – Azure PowerShell'
description: Saiba como usar o Azure PowerShell para criar um conjunto de dimensionamento de máquinas virtuais e algumas tarefas de gerenciamento comuns, por exemplo, como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 102ab619a3f341658383dc4997f9fbfc2cd12a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519173"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell

Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Durante todo o ciclo de vida do conjunto de dimensionamento de uma máquina virtual, você poderá precisar executar uma ou mais tarefas de gerenciamento. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de instância de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar um conjunto de dimensionamento de máquinas virtuais. Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *EastUS*. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
O nome do grupo de recursos é especificado quando você cria ou modifica um conjunto de dimensionamento ao longo deste tutorial.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Primeiro, defina o nome de usuário e a senha de um administrador para as instâncias de VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Leva alguns minutos para criar e configurar todos os recursos e as instâncias de VM do conjunto de dimensionamento.

> [!IMPORTANT]
> Se não for possível se conectar ao seu conjunto de dimensionamento, talvez seja necessário criar um Grupo de Segurança de Rede adicionando o parâmetro *[-SecurityGroupName "mySecurityGroup"](/powershell/module/az.compute/new-azvmss)* .


## <a name="view-the-vm-instances-in-a-scale-set"></a>Exibir as instâncias de VM em um conjunto de dimensionamento
Para exibir uma lista de instâncias de VM em um conjunto de dimensionamento, use [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) da seguinte forma:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A saída de exemplo abaixo mostra duas instâncias de VM no conjunto de dimensionamento:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `-InstanceId` a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). O exemplo abaixo exibe informações sobre a instância de VM *1*:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Listar informações de conexão
Um endereço IP público é atribuído ao balanceador de carga que direciona o tráfego para as instâncias de VM individuais. Por padrão, as regras de Conversão de endereços de rede (NAT) são adicionadas ao balanceador de carga do Azure, que encaminha o tráfego de conexão remota para cada VM em determinada porta. Para conectar as instâncias de VM em um conjunto de dimensionamento, crie uma conexão remota para um endereço IP público e número de porta atribuídos.

Para listar as portas NAT que devem se conectar a instâncias de VM em um conjunto de dimensionamento, primeiro obtenha o objeto do balanceador de carga com [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Depois, exiba as regras NAT de entrada com [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número de porta para onde as regras de NAT encaminham o tráfego:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

O *Nome* da regra está de acordo com o nome da instância de VM conforme mostrado em um comando [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) anterior. Por exemplo, para se conectar à instância de VM *0*, use *myScaleSet3389.0* e conecte-se à porta *50001*. Para se conectar à instância de VM *1*, use o valor de *myScaleSet3389.1* e conecte-se à porta *50002*. Para usar a comunicação remota do PowerShell, conecte-se à regra de instância de VM apropriada para a porta *TCP* *5985*.

Exiba o endereço IP público do balanceador de carga com [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Saída de exemplo:

```powershell
IpAddress
---------
52.168.121.216
```

Crie uma conexão remota para sua primeira instância de VM. Especifique o endereço IP público e o número da porta da instância de VM necessária, conforme mostrado com os comandos anteriores. Quando solicitado, insira as credenciais usadas quando você criou o conjunto de dimensionamento (por padrão, nos comandos de exemplo: *azureuser* e *P\@ssw0rd!* ). Caso use o Azure Cloud Shell, execute esta etapa de um prompt do PowerShell local ou do Cliente da Área de Trabalho Remota. O exemplo abaixo se conecta a uma instância de VM em *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Depois de conectado à instância de VM, você poderá executar algumas alterações de configuração manualmente, caso seja necessário. Por enquanto, feche a conexão remota.


## <a name="understand-vm-instance-images"></a>Entender imagens de instância de VM
O Azure Marketplace inclui muitas imagens que podem ser usadas para criar instâncias de VM. Para ver uma lista de fornecedores disponíveis, use o comando [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Para exibir uma lista de imagens de determinado publicador, use [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). A lista de imagens também pode ser filtrada por `-PublisherName` ou `-Offer`. No exemplo a seguir, a lista é filtrada para todas as imagens com o nome do fornecedor *MicrosoftWindowsServer* e uma oferta que corresponde a *WindowsServer*:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

A saída de exemplo abaixo mostra todas as imagens disponíveis do Windows Server:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Quando você criou um conjunto de dimensionamento no início do tutorial, uma imagem de VM padrão do *Windows Server 2016 DataCenter* foi fornecida para as instâncias de VM. É possível especificar uma imagem de VM diferente com base na saída de [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). O exemplo a seguir cria um conjunto de dimensionamento com o parâmetro `-ImageName` para especificar uma imagem de VM do *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> É recomendável usar a versão *mais recente* da imagem. Especifique 'mais recente' para usar a versão mais recente de uma imagem disponível no momento da implantação. Observe que, mesmo que você use 'mais recente', a imagem da VM não será atualizada automaticamente após o tempo de implantação, mesmo se uma nova versão ficar disponível.

## <a name="understand-vm-instance-sizes"></a>Entender tamanhos de instância de VM
Um tamanho de instância de VM, ou *SKU*, determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a instância de VM. As instâncias de VM em um conjunto de dimensionamento precisam ser dimensionadas apropriadamente para a carga de trabalho esperada.

### <a name="vm-instance-sizes"></a>Tamanhos de instância de VM
A tabela a seguir categoriza tamanhos de VMs comuns para determinados casos de uso.

| Type                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| [Computação otimizada](../virtual-machines/sizes-compute.md)   | Fs, F             | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| [Memória otimizada](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](../virtual-machines/sizes-storage.md)      | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| [Alto desempenho](../virtual-machines/sizes-hpc.md) | H, A8-11          | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. 

### <a name="find-available-vm-instance-sizes"></a>Localizar tamanhos de instância de VM disponíveis
Para ver uma lista de tamanhos de instância de VM disponíveis em uma região específica, use o comando [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

O resultado é semelhante ao exemplo condensado abaixo, que mostra os recursos atribuídos a cada tamanho de VM:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Quando você criou um conjunto de dimensionamento no início do tutorial, um SKU de VM padrão *Standard_DS1_v2* foi fornecido para as instâncias de VM. É possível especificar um tamanho de instância de VM diferente com base na saída de [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). O exemplo a seguir cria um conjunto de dimensionamento com o parâmetro `-VmSize` para especificar um tamanho de instância de VM *Standard_F1*. Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Quando você criou um conjunto de dimensionamento, solicitou duas instâncias de VM. Para aumentar ou diminuir o número de instâncias de VM no conjunto de dimensionamento, você pode alterar a capacidade manualmente. O conjunto de dimensionamento cria ou remove o número necessário de instâncias de VM e configura o balanceador de carga para distribuir o tráfego.

Primeiro, crie um objeto de conjunto de dimensionamento com [Get-AzVmss](/powershell/module/az.compute/get-azvmss), em seguida, especifique um novo valor para `sku.capacity`. Para aplicar a alteração de capacidade, use [Update-AzVmss](/powershell/module/az.compute/update-azvmss). O seguinte exemplo aumenta o número de instâncias de VM no conjunto de dimensionamento para *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Demora alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Para ver o número de instâncias que você tem agora no conjunto de dimensionamento, use [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A seguinte saída de exemplo mostra que a capacidade do conjunto de dimensionamento agora é *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Tarefas comuns de gerenciamento
Agora você pode criar um conjunto de dimensionamento, listar as informações de conexão e se conectar a instâncias de VM. Você aprendeu como pode usar uma imagem de sistema operacional diferente para suas instâncias de VM, selecionar um tamanho de VM diferente ou dimensionar o número de instâncias manualmente. Como parte do gerenciamento diário, talvez seja necessário parar, iniciar ou reiniciar as instâncias de VM em seu conjunto de dimensionamento.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Parar e desalocar instâncias de VM em um conjunto de dimensionamento
Para parar uma ou mais VMs em um conjunto de dimensionamento, use [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs a serem paradas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são paradas. O exemplo abaixo interrompe a instância *1*:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Por padrão, as VMs paradas são desalocadas e não incorrem em encargos de computação. Se desejar que a VM permaneça em um estado de provisionamento quando interrompido, adicione o parâmetro `-StayProvisioned` ao comando anterior. VMs paradas que permanecem provisionadas incorrem em encargos de computação regulares.

### <a name="start-vm-instances-in-a-scale-set"></a>Iniciar instâncias de VM em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [Start-AzVmss](/powershell/module/az.compute/start-azvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs a serem iniciadas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. O exemplo abaixo inicia a instância *1*:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Reiniciar instâncias de VM em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [Retart-AzVmss](/powershell/module/az.compute/restart-azvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para reiniciar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. O exemplo abaixo reinicia a instância *1*:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Limpar os recursos
Quando você excluir um grupo de recursos, todos os recursos contidos, como as instâncias de VM, as rede virtuais e os discos, também serão excluídos. O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a executar algumas tarefas básicas de criação e gerenciamento do conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

Avance para o próximo tutorial a fim de saber mais sobre discos de conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Usar discos de dados com conjuntos de dimensionamento](tutorial-use-disks-powershell.md)
