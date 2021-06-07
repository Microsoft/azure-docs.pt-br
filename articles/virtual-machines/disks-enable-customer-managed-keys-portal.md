---
title: Portal do Azure-habilitar chaves gerenciadas pelo cliente com discos gerenciados por SSE
description: Habilite chaves gerenciadas pelo cliente em seus discos gerenciados por meio do portal do Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5452b1e2ffc3ca8a11f3acb591c6288806445e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499383"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Usar o portal do Azure para habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para discos gerenciados

Armazenamento em Disco do Azure permite que você gerencie suas próprias chaves ao usar a criptografia do lado do servidor (SSE) para discos gerenciados, se você escolher. Para obter informações conceituais sobre o SSE com chaves gerenciadas pelo cliente, bem como outros tipos de criptografia de disco gerenciado, consulte a seção **chaves gerenciadas pelo cliente** do nosso artigo sobre criptografia de disco:

- Para Linux: [chaves gerenciadas pelo cliente](./disk-encryption.md#customer-managed-keys)
- Para Windows: [chaves gerenciadas pelo cliente](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não é possível desabilitá-lo.
    Se você precisar solucionar esse erro, deverá copiar todos os dados para um disco gerenciado totalmente diferente que não esteja usando chaves gerenciadas pelo cliente:

    - Para Linux: [copiar um disco gerenciado](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Para Windows: [copiar um disco gerenciado](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Somente [chaves de software e HSM RSA](../key-vault/keys/about-keys.md) de tamanhos de 2.048 bits, 3.072 bits e 4.096 bits têm suporte, sem outras chaves ou tamanhos.
    - As chaves [HSM](../key-vault/keys/hsm-protected-keys.md) exigem a camada **Premium** de cofres de chaves do Azure.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

As seções a seguir abordam como habilitar e usar chaves gerenciadas pelo cliente para Managed disks:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Agora que você criou e configurou o cofre de chaves e o conjunto de criptografia de disco, você pode implantar uma VM usando a criptografia.
O processo de implantação de VM é semelhante ao processo de implantação padrão, as únicas diferenças são que você precisa implantar a VM na mesma região que seus outros recursos e optar por usar uma chave gerenciada pelo cliente.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Na folha **básico** , selecione a mesma região que o conjunto de criptografia de disco e Azure Key Vault.
1. Preencha os outros valores na folha **básica** como desejar.

    ![Captura de tela da experiência de criação da VM, com o valor de região realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Na folha **discos** , selecione **criptografia em repouso com uma chave gerenciada pelo cliente**.
1. Selecione o conjunto de criptografia de disco na lista suspensa **conjunto de criptografia de disco** .
1. Faça as seleções restantes como desejar.

    ![Captura de tela da experiência de criação da VM, a folha discos. Com a lista suspensa conjunto de criptografia de disco realçada.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Habilitar em um disco existente

> [!CAUTION]
> Habilitar a criptografia de disco em qualquer disco anexado a uma VM exigirá que você interrompa a VM.
    
1. Navegue até uma VM que está na mesma região que um de seus conjuntos de criptografia de disco.
1. Abra a VM e selecione **parar**.

    ![Captura de tela da sobreposição principal para sua VM de exemplo, com o botão parar realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Após a interrupção da VM, selecione **discos** e, em seguida, selecione o disco que você deseja criptografar.

    ![Captura de tela de sua VM de exemplo, com a folha discos aberta. O disco do sistema operacional é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecione **criptografia** e selecione **criptografia em repouso com uma chave gerenciada pelo cliente** e, em seguida, selecione o conjunto de criptografia de disco na lista suspensa.
1. Clique em **Salvar**.

    ![Captura de tela do seu exemplo de disco do so. A folha criptografia está aberta, a criptografia em repouso com uma chave gerenciada pelo cliente é selecionada, bem como seu Azure Key Vault de exemplo. Depois de fazer essas seleções, o botão salvar é selecionado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Repita esse processo para todos os outros discos anexados à VM que você gostaria de criptografar.
1. Quando os discos terminarem de alternar para chaves gerenciadas pelo cliente, se não houver nenhum outro disco anexado que você queira criptografar, você poderá iniciar sua VM.

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente contam com as identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Ao configurar chaves gerenciadas pelo cliente, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, subsequentemente, você mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, confira [Transferência de uma assinatura entre diretórios do Azure Active Directory](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Próximas etapas

- [Conheça os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs VMware para o Azure usando o PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)