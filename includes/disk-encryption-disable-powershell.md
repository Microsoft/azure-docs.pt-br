---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776768"
---
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. Desabilitar a criptografia de disco de dados na VM do Windows quando os discos de dados e o sistema operacional foram criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

- **Desabilitar a criptografia de disco com o Azure PowerShell:** para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desabilitar a criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** 

    1. Clique em **Implantar no Azure** no modelo [Desabilitar criptografia de disco na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Selecione a assinatura, o grupo de recursos, o local, a VM, o tipo de volume, os termos legais e o contrato.
    3.  Clique em **Comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 
