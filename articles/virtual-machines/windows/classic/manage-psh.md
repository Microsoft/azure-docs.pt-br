---
title: "Gerenciar suas máquinas virtuais usando o Azure PowerShell | Microsoft Docs"
description: "Aprenda os comandos que você pode usar para automatizar tarefas de gerenciamento de suas máquinas virtuais."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: fd2df7e1029ced11974d0b832258bed2cf3bbb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gerenciar suas máquinas virtuais usando o Azure PowerShell
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para comandos comuns do PowerShell usando o modelo do Resource Manager, veja [aqui](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Muitas tarefas realizadas diariamente para gerenciar suas VMs podem ser automatizadas usando cmdlets do Azure PowerShell. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

> [!NOTE]
> Se você ainda não instalou e configurou o Azure PowerShell, você pode obter instruções no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Como usar os comandos de exemplo
Você precisará substituir parte do texto nos comandos por texto apropriado para seu ambiente. O < e > símbolos indicam o texto que você deve substituir. Ao substituir o texto, remova os símbolos, mas mantenha as aspas.

## <a name="get-a-vm"></a>Obter uma VM
Essa é uma tarefa básica que você usará com frequência. Use-a para obter informações sobre uma VM, executar tarefas em uma VM ou obter a saída para armazenar em uma variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo entre aspas, incluindo os caracteres < e >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída em uma variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Faça logon em uma VM baseada em Windows
Execute estes comandos:

> [!NOTE]
> Você pode obter o nome do serviço de nuvem e de máquina virtual na exibição do comando **Get-AzureVM** .
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<local da unidade e da pasta para armazenar o arquivo RDP baixado, exemplo: c:\temp >" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>Parar uma VM
Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Use esse parâmetro para manter o VIP (IP virtual) do serviço de nuvem, caso essa seja a última VM no serviço de nuvem. <br><br> Se usar o parâmetro StayProvisioned, você ainda será cobrado pela VM.
> 
> 

## <a name="start-a-vm"></a>Iniciar uma VM
Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Essa tarefa requer algumas etapas. Primeiro, use o cmdlet ****Add-AzureDataDisk**** para adicionar o disco ao objeto $vm. Em seguida, use o cmdlet **Update-AzureVM** para atualizar a configuração da VM.

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e o anexa.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para anexar um disco de dados existente, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para anexar discos de dados de um arquivo .vhd existente no armazenamento de blob, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Criar uma VM baseada no Windows
Para criar no Azure uma nova máquina virtual baseada no Windows, use as instruções em [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas no Windows](create-powershell.md). Este tópico o orienta durante a criação de um conjunto de comandos do Azure PowerShell que cria uma VM baseada em Windows que pode ser pré-configurada:

* Com associação de domínio do Active Directory.
* Com discos adicionais.
* Como membro de um conjunto de balanceamento de carga existente.
* Com um endereço IP estático.

