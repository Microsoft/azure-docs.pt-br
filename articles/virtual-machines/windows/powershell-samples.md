---
title: Amostras do PowerShell de máquina virtual do Azure | Microsoft Docs
description: Amostras do PowerShell de máquina virtual do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 576fe268bec12c16c7c2e2076dfa066c908693d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583682"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Amostras do PowerShell de máquina virtual do Azure

A tabela a seguir fornece links para amostras de script do PowerShell que criam e gerenciam máquinas virtuais do Windows (VMs).

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar rapidamente uma máquina virtual](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados, com um mínimo de prompts.|
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais em uma configuração altamente disponível e com balanceamento de carga.|
| [Crie uma VM e execute um script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e usa a Extensão de Script Personalizado do Azure para instalar o IIS. |
| [Criar uma VM e executar uma configuração de DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e usa a extensão DSC (Configuração de Estado de Desejado) do Azure para instalar o IIS. |
| [Carregar um VHD e criar VMs](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Carrega um arquivo VHD local no Azure, cria uma imagem a partir do VHD e cria uma VM a partir dessa imagem. |
| [Criar VM por meio de um disco do sistema operacional gerenciado](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. |
| [Criar uma VM de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo criando primeiro um disco gerenciado a partir do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do sistema operacional. |
|**Gerenciar armazenamento**||
| [Crie um disco gerenciado a partir de um VHD na mesma ou em uma assinatura diferente](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerenciado a partir de um VHD especializado como um disco do sistema operacional ou de um VHD de dados como um disco de dados, na mesma assinatura ou em uma assinatura diferente.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Copie um disco gerenciado para a mesma ou para uma assinatura diferente](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia um disco gerenciado para a mesma ou uma assinatura diferente que está na mesma região que o disco gerenciado pai.
| [Exporte um instantâneo como um VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerenciado como um VHD para uma conta de armazenamento em uma região diferente. |
| [Exportar o VHD de um disco gerenciado para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento em uma região diferente. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um instantâneo de um VHD e, em seguida, usa esse instantâneo para criar vários discos gerenciados idênticos rapidamente.  |
| [Copie um instantâneo para a mesma ou para uma assinatura diferente](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia um instantâneo para a mesma ou uma assinatura diferente que está na mesma região do instantâneo pai. |
|**Proteger máquinas virtuais**||
| [Criptografar uma VM e seus discos de dados](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Cria um cofre de chaves do Azure, uma chave de criptografia e uma entidade de serviço e, em seguida, criptografa uma VM. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com o Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente do Azure Log Analytics e registra a VM em um espaço de trabalho do Log Analytics.  |
| | |
