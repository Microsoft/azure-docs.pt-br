---
title: Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais
description: Saiba mais sobre a criptografia de disco do Azure para VMs (máquinas virtuais) e conjuntos de dimensionamento de VM. A criptografia de disco do Azure funciona para VMs Linux e Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543666"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais

A criptografia de disco do Azure pode ser aplicada a máquinas virtuais Linux e Windows, bem como a conjuntos de dimensionamento de máquinas virtuais. 

## <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

Os artigos a seguir fornecem orientação para criptografar máquinas virtuais do Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual do Azure Disk Encryption

- [Visão geral de Azure Disk Encryption para VMs Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Cenários de Azure Disk Encryption em VMs Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criar e criptografar uma VM do Linux com a CLI do Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Linux com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Criar e criptografar uma VM do Linux com o portal do Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Esquema de extensão de Azure Disk Encryption para Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption scripts de exemplo](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Guia de solução de problemas do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Perguntas frequentes do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption com o Azure AD (versão anterior)

- [Visão geral de Azure Disk Encryption com o Azure AD para máquinas virtuais do Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption com cenários do Azure AD em VMs do Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows

Os artigos a seguir fornecem orientação para criptografar máquinas virtuais do Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual do Azure Disk Encryption

- [Visão geral de Azure Disk Encryption para máquinas virtuais Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Cenários de Azure Disk Encryption em VMs Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criar e criptografar uma VM do Windows com a CLI do Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Windows com o Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Criar e criptografar uma VM do Windows com o portal do Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Esquema de extensão Azure Disk Encryption para Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption scripts de exemplo](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Guia de solução de problemas do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Perguntas frequentes do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption com o Azure AD (versão anterior)

- [Visão geral do Azure Disk Encryption com o Azure AD para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption com cenários do Azure AD em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>conjuntos de escala de máquina virtual

Os artigos a seguir fornecem orientações para criptografar conjuntos de dimensionamento de máquinas virtuais.

- [Visão geral de Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Usar o Azure Disk Encryption com o sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da criptografia do Azure](encryption-overview.md)
- [Criptografia de dados em repouso](encryption-atrest.md)
- [Práticas recomendadas de segurança e criptografia de dados](data-encryption-best-practices.md)
