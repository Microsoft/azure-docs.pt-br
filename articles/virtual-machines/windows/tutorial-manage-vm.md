---
title: Tutorial - Criar e gerenciar VMs do Windows com o Microsoft PowerShell | Microsoft Docs
description: Neste tutorial, você aprende a usar o Microsoft Azure PowerShell para criar e gerenciar as VMs Windows no Microsoft Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b1093c75e5552fea875f81d2860deb0d61405022
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707949"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Tutorial: Criar e gerenciar as VMs do Windows com o Azure PowerShell

Máquinas virtuais do Azure fornecem um ambiente de computação totalmente configurável e flexível. Este tutorial aborda itens básicos de tarefas de implantação de VM (máquina virtual) do Azure, como a seleção de um tamanho de VM, a seleção de uma imagem de VM e a implantação de uma VM. Você aprenderá como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Redimensionar uma VM
> * Exibir e compreender o estado da VM

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup).

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. No exemplo a seguir, um grupo de recursos chamado *myResourceGroupVM* é criado na região *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto durante este tutorial.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Há várias opções disponíveis ao criar uma VM, como a imagem do sistema operacional, a configuração de rede e as credenciais administrativas. Este exemplo cria uma VM, denominada *myVM*, que executa a versão padrão do Windows Server 2016 Datacenter.

Defina o nome de usuário e a senha necessários para a conta de administrador na VM com [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6):

```azurepowershell-interactive
$cred = Get-Credential
```

Crie a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Conectar-se a uma VM

Após a implantação, crie uma conexão de área de trabalho remota com a VM.

Execute os comandos a seguir para retornar o endereço IP público da VM. Anote esse endereço IP para se conectar a ele com o navegador para testar a conectividade à Web em uma etapa futura.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Use o comando a seguir em seu computador local para criar uma sessão remota de área de trabalho com a VM. Substitua o endereço IP pelo *publicIPAddress* da VM. Quando solicitado, insira as credenciais usadas ao criar a VM.

```powershell
mstsc /v:<publicIpAddress>
```

Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome de usuário e a senha que você criou para a VM e clique em **OK**.

## <a name="understand-marketplace-images"></a>Noções básicas sobre as imagens do Marketplace

O Azure Marketplace inclui muitas imagens que podem ser usadas para criar uma nova VM. Nas etapas anteriores, uma VM foi criada usando a imagem do Windows Server 2016 Datacenter. Nesta etapa, o módulo do PowerShell é usado para pesquisar no marketplace por outras imagens do Windows, que também pode ser usado como base para novas VMs. Este processo consiste em localizar o publicador, a oferta, o SKU e, opcionalmente, um número de versão para [identificar](cli-ps-findimage.md#terminology) a imagem.

Use o comando [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) para retornar uma lista de editores de imagem:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Use o comando [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) para retornar uma lista de ofertas de imagem. Com este comando, a lista retornada é filtrada no editor especificado chamado `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Os resultados serão algo parecido com este exemplo: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

O comando [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) filtrará o nome do editor e da oferta para retornar uma lista com nomes de imagem.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Os resultados serão algo parecido com este exemplo: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Essas informações podem ser usadas para implantar uma VM com uma imagem específica. Este exemplo implanta uma VM usando a versão mais recente de um Windows Server 2016 com imagem de contêineres.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os prompts do PowerShell sejam exibidos de volta para você. Você pode exibir os detalhes de trabalhos em segundo plano com o cmdelt `Get-Job`.

## <a name="understand-vm-sizes"></a>Entender os tamanhos de VM

O tamanho da VM determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a VM. As máquinas virtuais devem ser criadas usando um tamanho de VM adequado para a carga de trabalho. Se uma carga de trabalho aumentar, uma máquina virtual existente também poderá ser redimensionada.

### <a name="vm-sizes"></a>Tamanhos de VM

A tabela a seguir categoriza tamanhos em casos de uso.  

| Type                     | Tamanhos comuns           |    DESCRIÇÃO       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| [Computação otimizada](sizes-compute.md)   | Fsv2          | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| [Memória otimizada](sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](sizes-storage.md)      | Lsv2, Ls              | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| [Alto desempenho](sizes-hpc.md) | H        | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. |

### <a name="find-available-vm-sizes"></a>Encontrar tamanhos de VM disponíveis

Para ver uma lista de tamanhos de VM disponíveis em uma região específica, use o comando [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize).

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Redimensionar uma VM

Após a implantação de uma VM, ela pode ser redimensionada para aumentar ou diminuir a alocação de recursos.

Antes de redimensionar uma VM, verifique se o tamanho desejado está disponível no cluster da VM atual. O comando [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) retorna uma lista de tamanhos.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Se o tamanho estiver disponível, a VM poderá ser redimensionada com base em um estado ligado. No entanto, ela será reinicializada durante a operação.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Se o tamanho desejado não estiver disponível no cluster atual, a VM precisará ser desalocada antes que a operação de redimensionamento ocorra. Desalocar uma VM removerá todos os dados no disco temporário e alterará o endereço IP público, a menos que um endereço IP estático esteja sendo usado.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Estados de energia da VM

Uma VM do Azure pode ter um dentre vários estados de energia. 


| Estado de energia | DESCRIÇÃO
|----|----|
| Iniciando | A máquina virtual está sendo iniciada. |
| Executando | A máquina virtual está em execução. |
| Parando | A máquina virtual está sendo interrompida. |
| Parado | A máquina virtual está parada. Máquinas virtuais no estado interrompido ainda incorrerá em encargos de computação.  |
| Desalocando | A VM está sendo desalocada. |
| Desalocada | Indica que a VM é removida do hipervisor, mas ainda está disponível no plano de controle. As máquinas virtuais no estado `Deallocated` não incorrem em encargos de computação. |
| - | O estado de energia da VM é desconhecido. |


Para obter o estado de uma VM específica, use o comando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Especifique nomes válidos para uma VM e um grupo de recursos.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

A saída será parecida com este exemplo:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Tarefas de gerenciamento

Durante o ciclo de vida de uma VM, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma VM. Além disso, é possível que você queira criar scripts para automatizar tarefas repetitivas ou complexas. Usando o Azure PowerShell, muitas tarefas comuns de gerenciamento podem ser executadas em linha de comando ou em scripts.

### <a name="stop-a-vm"></a>Parar uma VM

Interrompa e desaloque uma VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Se você quiser manter a VM em um estado de provisionamento, use o parâmetro -StayProvisioned.

### <a name="start-a-vm"></a>Iniciar uma VM

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Tudo dentro de um grupo de recursos é excluído quando você o exclui.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação e o gerenciamento básico de VM e como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Redimensionar uma VM
> * Exibir e compreender o estado da VM

Avança para o próximo tutorial para saber mais sobre os discos de VM.  

> [!div class="nextstepaction"]
> [Criar e gerenciar discos de VM](./tutorial-manage-data-disk.md)
