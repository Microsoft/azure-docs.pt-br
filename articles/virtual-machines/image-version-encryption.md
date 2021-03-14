---
title: Versão prévia - Crie uma versão da imagem criptografada com suas próprias chaves
description: Crie uma versão de imagem em uma galeria de imagens compartilhadas usando chaves de criptografia gerenciadas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554081"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Visualização: Use chaves gerenciadas pelo cliente para criptografar imagens

As imagens em uma galeria de imagens compartilhadas são armazenadas como instantâneos, portanto, são criptografadas automaticamente por meio da criptografia do lado do servidor. A criptografia do lado do servidor usa a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis. A criptografia do lado do servidor também é compatível com FIPS 140-2. Para obter mais informações sobre os módulos de criptografia subjacentes ao Azure Managed disks, consulte [API de criptografia: próxima geração](/windows/desktop/seccng/cng-portal).

Você pode contar com chaves gerenciadas por plataforma para a criptografia de suas imagens ou usar suas próprias chaves. Você também pode usar ambos juntos para criptografia dupla. Se você optar por gerenciar a criptografia com suas próprias chaves, pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os discos nas suas imagens. 

A criptografia do lado do servidor por meio de chaves gerenciadas pelo cliente usa Azure Key Vault. Você pode importar [suas chaves RSA](../key-vault/keys/hsm-protected-keys.md) para o cofre de chaves ou gerar novas chaves rsa em Azure Key Vault.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você já tenha um conjunto de criptografia de disco em cada região em que você deseja replicar a imagem:

- Para usar apenas uma chave gerenciada pelo cliente, consulte os artigos sobre como habilitar chaves gerenciadas pelo cliente com a criptografia do lado do servidor usando o [portal do Azure](./disks-enable-customer-managed-keys-portal.md) ou o [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation).

- Para usar chaves gerenciadas por plataforma e gerenciadas pelo cliente (para criptografia dupla), consulte os artigos sobre como habilitar a criptografia dupla em repouso usando o [portal do Azure](./disks-enable-double-encryption-at-rest-portal.md) ou o [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Você deve usar o link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público, a menos que você use esse link.

## <a name="limitations"></a>Limitações

Quando você está usando chaves gerenciadas pelo cliente para criptografar imagens em uma galeria de imagens compartilhada, essas limitações se aplicam:   

- Os conjuntos de chaves de criptografia devem estar na mesma assinatura que a imagem.

- Os conjuntos de chaves de criptografia são recursos regionais, portanto, cada região requer um conjunto de chaves de criptografia diferente.

- Você não pode copiar ou compartilhar imagens que usam chaves gerenciadas pelo cliente. 

- Depois de usar suas próprias chaves para criptografar um disco ou uma imagem, você não pode voltar a usar chaves gerenciadas pela plataforma para criptografar esses discos ou imagens.


> [!IMPORTANT]
> A criptografia por meio de chaves gerenciadas pelo cliente está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Para a visualização pública, primeiro você precisa registrar o recurso:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Demora alguns minutos para que o registro seja concluído. Use `Get-AzProviderFeature` para verificar o status do registro do recurso:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando `RegistrationState` retorna `Registered` , você pode passar para a próxima etapa.

Verifique o registro do seu provedor. Verifique se ele volta como `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se não retornar `Registered` , use o seguinte código para registrar os provedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar um conjunto de criptografia de disco para uma versão de imagem, use  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) com o `-TargetRegion` parâmetro: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Criar uma máquina virtual

Você pode criar uma VM (máquina virtual) de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma de criar uma VM [generalizada](vm-generalized-image-version-powershell.md) ou [especializada](vm-specialized-image-version-powershell.md) a partir de uma imagem. Use o conjunto de parâmetros estendidos e adicione `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração da VM.

Para discos de dados, adicione o `-DiskEncryptionSetId $setID` parâmetro ao usar [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a visualização pública, primeiro você precisa se registrar para o recurso. O registro leva cerca de 30 minutos.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o status do registro do recurso:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando esse código retornar `"state": "Registered"` , você poderá passar para a próxima etapa.

Verifique seu registro:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser registrado, execute o seguinte comando:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar um conjunto de criptografia de disco para uma versão de imagem, use [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) com o `--target-region-encryption` parâmetro. O formato de `--target-region-encryption` é uma lista separada por vírgulas de chaves para criptografar o sistema operacional e os discos de dados. O resultado deve ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Use `--managed-image` para especificar a origem da versão da imagem se a origem do disco do sistema operacional for um disco gerenciado ou uma VM. Neste exemplo, a origem é uma imagem gerenciada que tem um disco do sistema operacional e um disco de dados no LUN 0. O disco do sistema operacional será criptografado com DiskEncryptionSet1 e o disco de dados será criptografado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Use `--os-snapshot` para especificar o disco do sistema operacional se a origem do disco do sistema operacional for um instantâneo. Se houver instantâneos de disco de dados que também devem fazer parte da versão da imagem, adicione-os. Use `--data-snapshot-luns` para especificar o LUN e use `--data-snapshots` para especificar os instantâneos.

Neste exemplo, as origens são instantâneos de disco. Há um disco do sistema operacional e um disco de dados no LUN 0. O disco do sistema operacional será criptografado com DiskEncryptionSet1 e o disco de dados será criptografado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Criar a VM

É possível criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma de criar uma VM [generalizada](vm-generalized-image-version-cli.md) ou [especializada](vm-specialized-image-version-cli.md) a partir de uma imagem. Basta adicionar o `--os-disk-encryption-set` parâmetro com a ID do conjunto de criptografia. Para discos de dados, adicione `--data-disk-encryption-sets` com uma lista delimitada por espaço dos conjuntos de criptografia de disco para os discos de dados.


## <a name="portal"></a>Portal

Ao criar a versão da imagem no portal, você pode usar a guia **criptografia** para aplicar os conjuntos de criptografia de armazenamento.

> [!IMPORTANT]
> Para usar a criptografia dupla, você deve usar o link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público, a menos que você use esse link.


1. Na página **criar uma imagem de versão** , selecione a guia **criptografia** .
2. Em **tipo de criptografia**, selecione **criptografia em repouso com uma chave gerenciada pelo cliente** ou **criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente**. 
3. Para cada disco na imagem, selecione um conjunto de criptografia na lista suspensa **conjunto de criptografia de disco** . 

### <a name="create-the-vm"></a>Criar a VM

Você pode criar uma VM com base em uma versão de imagem e usar chaves gerenciadas pelo cliente para criptografar os discos. Quando você cria a VM no portal, na guia **discos** , selecione **criptografia em repouso com chaves gerenciadas pelo cliente** ou **criptografia dupla com chaves** gerenciadas por plataforma e gerenciadas pelo cliente para o tipo de **criptografia**. Em seguida, você pode selecionar o conjunto de criptografia na lista suspensa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Criptografia de disco do lado do servidor](./disk-encryption.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).