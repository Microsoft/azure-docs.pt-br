---
title: Excluir um cofre dos Serviços de Recuperação configurado para o serviço Azure Site Recovery
description: Saiba como excluir um cofre dos Serviços de Recuperação configurado o Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876036"
---
# <a name="delete-a-site-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação de Site

Dependências podem impedir a exclusão de um cofre do Azure Site Recovery. As ações que você precisa realizar variam de acordo com o cenário do Site Recovery. Para excluir um cofre usado no Backup do Azure, consulte [Excluir um cofre de Backup no Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Excluir um cofre do Site Recovery 
Para excluir o cofre, siga as etapas recomendadas para seu cenário.
### <a name="azure-vms-to-azure"></a>VMs do Azure para o Azure

1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Exclua o cofre.

### <a name="vmware-vms-to-azure"></a>VMs VMware para o Azure

1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Exclua todas as políticas de replicação seguindo as etapas em [Excluir uma política de replicação](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Exclua as referências ao vCenter seguindo as etapas em [Excluir um servidor vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Exclua o servidor de configuração seguindo as etapas em [Desativar um servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Exclua o cofre.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>VMs do Hyper-V (com VMM) para Azure
1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de uma VM Hyper-V (com VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Desassocie e exclua todas as políticas de replicação navegando até seu Cofre –> **Infraestrutura do Site Recovery** –> **Para o VMM do System Center** -> **Políticas de replicação**

3.  Exclua as referências aos servidores do VMM seguindo as etapas em [Cancelar o registro de um servidor do VMM conectado](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Exclua o cofre.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>VMs do Hyper-V (sem o Virtual Machine Manager) para o Azure
1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de uma máquina virtual Hyper-V (Hyper-V para Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Desassocie e exclua todas as políticas de replicação navegando até seu Cofre –> **Infraestrutura do Site Recovery** –> **Para Sites do Hyper-V** -> **Políticas de replicação**

3. Exclua as referências aos servidores do Hyper-V seguindo as etapas em [Cancelar o registro de um host Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Exclua o site do Hyper-V.

5. Exclua o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usar o PowerShell para forçar a exclusão do cofre 

> [!Important]
> Se estiver testando o produto e não estiver preocupado com a perda de dados, use o método de exclusão forçada para remover rapidamente o cofre e todas as suas dependências.
> O comando do PowerShell exclui todo o conteúdo do cofre e **não é reversível**.

Para excluir o cofre o Azure Site Recovery, mesmo se houver itens protegidos, use estes comandos:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Saiba mais sobre [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
