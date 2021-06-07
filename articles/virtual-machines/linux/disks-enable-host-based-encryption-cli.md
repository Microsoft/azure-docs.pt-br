---
title: Habilitar criptografia de ponta a ponta usando criptografia em discos gerenciados por CLI do Azure de host
description: Use a criptografia no host para habilitar a criptografia de ponta a ponta em seus Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3eecb584f468bc170f0325da8d734a1890691483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601764"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Use o CLI do Azure para habilitar a criptografia de ponta a ponta usando a criptografia no host

Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. Para obter informações conceituais sobre criptografia no host, bem como outros tipos de criptografia de disco gerenciado, consulte [criptografia em criptografia de host de ponta a ponta para os dados da VM](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Você também pode encontrar os tamanhos de VM programaticamente. Para saber como recuperá-los programaticamente, consulte a seção [localizando tamanhos de VM com suporte](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Pré-requisitos

Você deve habilitar o recurso para sua assinatura antes de usar a propriedade EncryptionAtHost para sua VM/VMSS. Siga as etapas abaixo para habilitar o recurso para sua assinatura:

1.  Execute o comando a seguir para registrar o recurso para sua assinatura

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Verifique se o estado do registro está registrado (leva alguns minutos) usando o comando a seguir antes de experimentar o recurso.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Criar um Azure Key Vault e DiskEncryptionSet

Depois que o recurso estiver habilitado, você precisará configurar um Azure Key Vault e um DiskEncryptionSet, se ainda não tiver feito isso.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Exemplos

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Crie uma VM com criptografia no host habilitado com chaves gerenciadas pelo cliente. 

Crie uma VM com discos gerenciados usando o URI de recurso do DiskEncryptionSet criado anteriormente para criptografar o cache do sistema operacional e discos de dados com chaves gerenciadas pelo cliente. Os discos temporários são criptografados com chaves gerenciadas pela plataforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Crie uma VM com criptografia no host habilitado com chaves gerenciadas pela plataforma. 

Crie uma VM com criptografia no host habilitada para criptografar o cache de discos de sistema operacional/dados e discos temporários com chaves gerenciadas pela plataforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Atualize uma VM para habilitar a criptografia no host. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Verificar o status da criptografia no host de uma VM

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Crie um conjunto de dimensionamento de máquinas virtuais com criptografia no host habilitado com chaves gerenciadas pelo cliente. 

Crie um conjunto de dimensionamento de máquinas virtuais com discos gerenciados usando o URI de recurso do DiskEncryptionSet criado anteriormente para criptografar o cache do sistema operacional e dos discos de dados com chaves gerenciadas pelo cliente. Os discos temporários são criptografados com chaves gerenciadas pela plataforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Crie um conjunto de dimensionamento de máquinas virtuais com criptografia no host habilitado com chaves gerenciadas pela plataforma. 

Crie um conjunto de dimensionamento de máquinas virtuais com criptografia no host habilitado para criptografar o cache de discos de sistema operacional/dados e discos temporários com chaves gerenciadas pela plataforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Atualize um conjunto de dimensionamento de máquinas virtuais para habilitar a criptografia no host. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Verificar o status da criptografia no host para um conjunto de dimensionamento de máquinas virtuais

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Localizando tamanhos de VM com suporte

Não há suporte para tamanhos de VM herdados. Você pode encontrar a lista de tamanhos de VM com suporte por meio de:

Chamar a [API de SKUs de recursos](/rest/api/compute/resourceskus/list) e verificar se a `EncryptionAtHostSupported` funcionalidade está definida como **true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Ou, chamando o cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) do PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e configurou esses recursos, você pode usá-los para proteger seus discos gerenciados. O link a seguir contém scripts de exemplo, cada um com um cenário respectivo, que você pode usar para proteger seus discos gerenciados.

[Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
