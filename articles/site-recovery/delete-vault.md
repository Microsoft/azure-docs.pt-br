---
title: Excluir um cofre de Azure Site Recovery
description: Saiba como excluir um cofre dos Serviços de Recuperação configurado o Azure Site Recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426377"
---
# <a name="delete-a-site-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação de Site

Este artigo descreve como excluir um cofre dos serviços de recuperação para Site Recovery. Para excluir um cofre usado no Backup do Azure, consulte [Excluir um cofre de Backup no Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Antes de começar

Antes de excluir um cofre, você deve remover os servidores registrados e os itens no cofre. O que você precisa remover depende dos cenários de replicação que você implantou. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Excluir um cofre-VM do Azure para o Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) para excluir todas as VMs protegidas.
2. Em seguida, exclua o cofre.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Excluir um cofre-VM do VMware para o Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para excluir todas as VMs protegidas.
2. Siga [estas etapas](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) para excluir todas as políticas de replicação.
3. Exclua as referências ao vCenter usando [estas etapas](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Siga [estas instruções](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) para desativar um servidor de configuração.
5. Em seguida, exclua o cofre.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Excluir um cofre-VM Hyper-V (com VMM) para o Azure

1. Siga [estas etapas](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) para excluir VMs do Hyper-V gerenciadas pelo System Center VMM.
2. Desassocie e exclua todas as políticas de replicação. Faça isso em seu cofre > **infraestrutura**  >  **de site Recovery para as políticas de replicação do System Center VMM**  >  .
3. Siga [estas etapas](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) para cancelar o registro de um servidor VMM conectado.
4. Em seguida, exclua o cofre.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Excluir um cofre-VM Hyper-V para o Azure

1. Siga [estas etapas](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) para excluir todas as VMs protegidas.
2. Desassocie e exclua todas as políticas de replicação. Faça isso em seu cofre > **site Recovery infraestrutura**  >  **para políticas de replicação de sites do Hyper-V**  >  .
3. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) para cancelar o registro de um host Hyper-V.
4. Exclua o site do Hyper-V.
5. Em seguida, exclua o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usar o PowerShell para forçar a exclusão do cofre 

> [!Important]
> Se estiver testando o produto e não estiver preocupado com a perda de dados, use o método de exclusão forçada para remover rapidamente o cofre e todas as suas dependências.
> O comando do PowerShell exclui todo o conteúdo do cofre e **não é reversível**.

Para excluir o cofre o Azure Site Recovery, mesmo se houver itens protegidos, use estes comandos:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Saiba mais sobre [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
