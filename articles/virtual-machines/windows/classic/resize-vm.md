---
title: "Redimensionar uma VM do Windows no modelo de implantação clássico – Azure | Microsoft Docs"
description: "Redimensione uma máquina virtual do Windows criada no modelo de implantação clássico usando o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionar uma VM do Windows criada no modelo de implantação clássico
Este artigo mostra como redimensionar uma VM do Windows criada no modelo de implantação clássico usando o Azure PowerShell.

Ao considerar a capacidade de redimensionar uma VM, há dois conceitos que controlam o intervalo de tamanhos disponíveis para redimensionar a máquina virtual. O primeiro conceito é a região na qual a VM está implantada. A lista de tamanhos de VM disponíveis na região está na guia Serviços da página da Web Regiões do Azure. O segundo conceito é o hardware físico que atualmente hospeda sua VM. Os servidores físicos que hospedam as VMs são agrupados em clusters de hardware físico comum. O método para alterar o tamanho de uma VM varia dependendo de se o cluster de hardware que atualmente hospeda a VM tem suporte para o novo tamanho de VM desejado.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Você também pode [redimensionar uma VM criada no modelo de implantação do Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Adicionar sua conta
Você deve configurar o Azure PowerShell para trabalhar com os recursos clássicos do Azure. Siga as etapas abaixo para configurar o Azure PowerShell para gerenciar os recursos clássicos.

1. No prompt do PowerShell, digite `Add-AzureAccount` e clique em **Enter**. 
2. Digite o endereço de email associado à sua assinatura do Azure e clique em **Continuar**. 
3. Digite a senha da sua conta. 
4. Clique em **Entrar**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionar no mesmo cluster de hardware
Para redimensionar uma VM para um tamanho disponível no cluster de hardware que hospeda a VM, execute as seguintes etapas.

1. Execute o seguinte comando do PowerShell para listar os tamanhos de VM disponíveis no cluster de hardware que hospeda o serviço de nuvem que contém a VM.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Execute os seguintes comandos para redimensionar a VM.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionar em um novo cluster de hardware
Para redimensionar uma VM para um tamanho indisponível no cluster de hardware que hospeda a VM, a nuvem de serviço e todas as VMs no serviço de nuvem devem ser recriadas. Cada serviço de nuvem é hospedado em um único cluster de hardware, de modo que todas as VMs no serviço de nuvem devem ter um tamanho com suporte em um cluster de hardware. As etapas a seguir descreverão como redimensionar uma VM excluindo e recriando o serviço de nuvem.

1. Execute o seguinte comando do PowerShell para listar os tamanhos de VM disponíveis na região. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Anote todas as configurações para cada VM no serviço de nuvem que contém a VM a ser redimensionada. 
3. Exclua todas as VMs no serviço de nuvem selecionando a opção para reter os discos para cada VM.
4. Recrie a VM a ser redimensionada usando o tamanho de VM desejado.
5. Recrie todas as outras VMs que estavam no serviço de nuvem usando um tamanho de VM disponível no cluster de hardware que agora hospeda o serviço de nuvem.

Um exemplo de script para excluir e recriar um serviço de nuvem usando um novo tamanho VM pode ser encontrado [aqui](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Próximas etapas
* [Redimensione uma VM criada no modelo de implantação do Gerenciador de Recursos](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

