---
title: Criptografar discos em uma VM do Windows no Azure | Microsoft Docs
description: Criptografar discos virtuais em uma VM do Windows para segurança aprimorada usando o Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: fadbb4668dbaed46cc30841d2b04a92ea41cd5a1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718671"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Criptografar discos virtuais em uma VM do Windows
Para conformidade e segurança aprimorados da VM (máquina virtual), os discos virtuais no Azure podem ser criptografados. Os discos são criptografados usando chaves criptográficas protegidas em um Azure Key Vault. Você controla essas chaves criptográficas e pode auditar seu uso. Este artigo descreve como criptografar discos virtuais em uma VM do Windows usando o Azure PowerShell. Você também pode [criptografar uma VM do Linux usando a CLI do Azure](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Os discos virtuais em VMs do Windows são criptografados em repouso usando o BitLocker. Não há custo para criptografar discos virtuais no Azure. As chaves criptográficas são armazenadas em um Azure Key Vault usando a proteção de software ou você pode importar ou gerar suas chaves nos Módulos de Segurança de Hardware (HSMs) certificados para os padrões FIPS 140-2 nível 2. Chaves criptográficas são usadas para criptografar e descriptografar discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. 

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Cofre de chaves do Azure.
1. Configure a chave de criptografia a ser usada para criptografar discos.
1. Ative a criptografia de disco para seus discos virtuais.
1. A Azure Key Vault criptográficas necessárias são solicitadas do Cofre de Chaves do Azure.
1. Os discos virtuais são criptografados usando a chave de criptografia fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações

Requisitos e cenários com suporte para criptografia de disco:

* Ativando a criptografia em novas VMs do Windows a partir de imagens do Azure Marketplace ou imagens VHD personalizadas.
* Habilitar a criptografia em VMs existentes do Windows no Azure.
* Ativando a criptografia em VMs do Windows configuradas usando Espaços de Armazenamento.
* Como desabilitar a criptografia no OS e em unidades de dados para VMs do Windows.
* VMs de camada padrão, como as VMs da série A, D, DS, G e GS.

    > [!NOTE]
    > Todos os recursos (incluindo o cofre de chaves, a conta de armazenamento e a VM) devem estar na mesma região e assinatura do Azure.

Atualmente, a criptografia de Disk não é suportada nos seguintes cenários:

* VMs de camada básica.
* VMs criadas usando o modelo de implantação clássico.
* Atualização das chaves de criptografia em uma VM já criptografada.
* Integração com o serviço de gerenciamento de chaves local.


## <a name="create-an-azure-key-vault-and-keys"></a>Criar um Azure Key Vault e as chaves
Antes de começar, verifique se a versão mais recente do módulo do Azure PowerShell foi instalada. Para obter mais informações, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Nos exemplos de comando a seguir, substitua todos os parâmetros de exemplo por seus próprios nomes, locais e valores de chave, como *myResourceGroup*, *myKeyVault*, *myVM* e assim por diante.

A primeira etapa é criar um Cofre de Chaves do Azure para armazenar as chaves criptográficas. Os cofres de chaves do Azure podem armazenar chaves, segredos ou senhas que permitem implementá-los com segurança em seus aplicativos e serviços. Para criptografia de disco virtual, você criará um Key Vault para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais. 

Habilite o provedor do Azure Key Vault em sua assinatura do Azure com [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider) e crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Leste dos EUA*:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

O Azure Key Vault que mantém as chaves criptográficas e os recursos de computação associados, como armazenamento e a própria VM, devem estar todos na mesma região. Crie um Azure Key Vault com [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) e habilite o Key Vault para uso com criptografia de disco. Especifique um nome exclusivo de Key Vault para *keyVaultName* da seguinte maneira:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Você pode armazenar chaves criptográficas usando o software ou a proteção do modelo de segurança de hardware (HSM).  Um cofre de chaves padrão armazena apenas chaves protegidas por software. O uso de um HSM exige um cofre de chave premium por um custo adicional. Para criar um cofre de chave premium, na etapa anterior, adicione o parâmetro *-Sku "Premium"* . O exemplo a seguir usa chaves protegidas por software, desde que criamos um Cofre de Chaves padrão. 

Para ambos os modelos de proteção, a plataforma do Azure deve ter acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Crie uma chave de criptografia em seu Key Vault com [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). O exemplo a seguir cria uma chave chamada *myKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para testar o processo de criptografia, vamos criar uma VM com [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* usando uma imagem do *Datacenter do Windows Server 2016*. Quando as credenciais forem solicitadas, digite o nome de usuário e a senha a ser usada para sua VM:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Criptografar uma máquina virtual
Criptografe sua VM com [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) usando a chave do Azure Key Vault. O exemplo a seguir recupera todas as informações de chave e depois criptografa a VM chamada *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Aceite o aviso para continuar com a criptografia da VM. A VM é reiniciada durante o processo. Após a conclusão do processo de criptografia e reinicialização da VM, examine o status de criptografia com [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A saída deverá ser semelhante ao seguinte exemplo:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como gerenciar um Azure Key Vault, consulte [Configurar um Cofre de Chaves para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
