---
title: Habilitar criptografia de ponta a ponta usando criptografia em discos gerenciados por portal do Azure de host
description: Use a criptografia no host para habilitar a criptografia de ponta a ponta em seus Azure Managed disks-portal do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721861"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Use o portal do Azure para habilitar a criptografia de ponta a ponta usando a criptografia no host

Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. Para obter informações conceituais sobre criptografia no host, bem como outros tipos de criptografia de disco gerenciado, consulte:

* Linux: [criptografia na criptografia de ponta a ponta de host para os dados da VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [criptografia na criptografia de ponta a ponta de host para os dados da VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve habilitar o recurso para sua assinatura antes de usar a propriedade EncryptionAtHost para sua VM/VMSS. Siga as etapas abaixo para habilitar o recurso para sua assinatura:

1. **Portal do Azure**: selecione o ícone de Cloud Shell na [portal do Azure](https://portal.azure.com):

    ![Ícone para iniciar o Cloud Shell do portal do Azure](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  Execute o comando a seguir para registrar o recurso para sua assinatura

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  Verifique se o estado do registro está registrado (leva alguns minutos) usando o comando a seguir antes de experimentar o recurso.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Entre no portal do Azure usando o [link fornecido](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Você deve usar o [link fornecido](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia no host não está visível no momento no portal do Azure público sem usar o link.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Criar um Azure Key Vault e um conjunto de criptografia de disco

Depois que o recurso estiver habilitado, você precisará configurar um Azure Key Vault e um conjunto de criptografia de disco, se ainda não tiver feito isso.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Você deve implantar uma nova VM para habilitar a criptografia no host, ela não pode ser habilitada em VMs existentes.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Crie uma nova máquina virtual, selecione uma região apropriada e um tamanho de VM com suporte.
1. Preencha os outros valores na folha **básica** como desejar e, em seguida, vá para a folha **discos** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Captura de tela da folha noções básicas de criação de máquina virtual, região e tamanho de V M são realçados.":::

1. Na folha **discos** , selecione **Sim** para **criptografia no host**.
1. Faça as seleções restantes como desejar.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Captura de tela da folha discos de criação de máquina virtual, a criptografia no host é realçada.":::

1. Conclua o processo de implantação da VM, faça seleções que se ajustam ao seu ambiente.

Agora você implantou uma VM com criptografia no host habilitado, todos os discos associados serão criptografados usando a criptografia no host.

## <a name="next-steps"></a>Próximas etapas

[Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
