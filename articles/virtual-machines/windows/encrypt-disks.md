---
title: Criptografar discos em uma VM do Windows no Azure | Microsoft Docs
description: "Como criptografar discos virtuais em uma VM do Windows para aumentar a segurança usando o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/10/2017
ms.author: iainfou
ms.openlocfilehash: 98b42b252a601af090579e3939f3c7ab91c3803b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Como criptografar discos virtuais em uma VM do Windows
Para conformidade e segurança aprimorados da VM (máquina virtual), os discos virtuais no Azure podem ser criptografados. Discos são criptografados usando chaves criptográficas que são protegidas em um Cofre de chaves do Azure. Você controla essas chaves criptográficas e pode auditar seu uso. Este artigo detalha como criptografar discos virtuais em uma VM do Windows usando o Azure PowerShell. Também é possível [Criptografar uma VM do Linux usando a CLI 2.0 do Azure](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Discos virtuais em VMs do Windows são criptografados em repouso usando o Bitlocker. Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Uma entidade de serviço do Azure Active Directory fornece um mecanismo seguro para emitir essas chaves de criptografia, enquanto as VMs são ligadas e desligadas.

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Cofre de chaves do Azure.
2. Configure a chave de criptografia a ser usada para criptografar discos.
3. Para ler a chave de criptografia do Azure Key Vault, crie uma entidade de serviço do Azure Active Directory com as permissões apropriadas.
4. Execute o comando para criptografar seus discos virtuais, especificando a entidade de serviço do Azure Active Directory e a chave de criptografia apropriada a ser usada.
5. A entidade de serviço do Azure Active Directory solicita a chave de criptografia necessária do Azure Key Vault.
6. Os discos virtuais são criptografados usando a chave de criptografia fornecida.

## <a name="encryption-process"></a>Processo de criptografia
A criptografia de disco depende dos seguintes componentes adicionais:

* **Cofre de Chaves do Azure** – usado para proteger chaves criptográficas e segredos usados para o processo de criptografia/descriptografia do disco. 
  * Se houver um, você pode usar um Cofre de Chaves do Azure existente. Não é necessário dedicar um Cofre de Chaves para criptografar discos.
  * Para separar os limites administrativos e visibilidade de chave, crie um Cofre de Chaves dedicado.
* **Azure Active Directory** – realiza a troca segura de chaves criptográficas necessárias e a autenticação para ações solicitadas. 
  * Normalmente, você pode usar uma instância existente do Azure Active Directory para hospedar seu aplicativo.
  * A entidade de serviço fornece um mecanismo seguro para solicitar e receber as chaves de criptografia apropriadas. Você não está desenvolvendo um aplicativo real que se integra ao Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos e cenários com suporte para criptografia de disco:

* Habilitar a criptografia em novas VMs do Windows a partir de imagens do Azure Marketplace ou de uma imagem VHD personalizada.
* Habilitar a criptografia em VMs existentes do Windows no Azure.
* Habilitar a criptografia em VMs do Windows que são configuradas usando Espaços de Armazenamento.
* Como desabilitar a criptografia no OS e em unidades de dados para VMs do Windows.
* Todos os recursos (por exemplo, Cofre de chaves, conta de Armazenamento e VM) devem pertencer à mesma assinatura e região do Azure.
* VMs de camada padrão, como as VMs da série A, D, DS, G e GS.

A criptografia de disco não tem suporte atualmente nos seguintes cenários:

* VMs de camada básica.
* VMs criadas com o modelo de implantação Clássico.
* Atualização das chaves de criptografia em uma VM já criptografada.
* Integração com o Serviço de Gerenciamento de Chaves local.

## <a name="create-azure-key-vault-and-keys"></a>Criar o Azure Key Vault e as chaves
Antes de começar, verifique se você tem a versão mais recente do módulo Azure PowerShell instalar. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview): Em todos os exemplos de comando, substitua todos os parâmetros de exemplo pelos seus próprios valores de nome, localização e chave. Os exemplos a seguir usam uma convenção de *myResourceGroup*, *myKeyVault*, *myVM*, etc.

A primeira etapa é criar um Cofre de Chaves do Azure para armazenar as chaves criptográficas. O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Para criptografia de disco virtual, crie o Key Vault para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais. 

Habilite o provedor do Azure Key Vault em sua assinatura do Azure com [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) e crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Leste dos EUA*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

O Cofre de chaves do Azure que contém as chaves criptográficas e recursos de computação associados, como armazenamento e a própria VM deve residir na mesma região. Crie um Azure Key Vault com [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) e habilite o Key Vault para uso com criptografia de disco. Especifique um nome exclusivo de Key Vault para *keyVaultName* da seguinte maneira:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

É possível armazenar chaves de criptografia usando a proteção do Modelo de segurança de Hardware (HSM) ou software. Usar um HSM requer um Cofre de Chaves premium. Há um custo adicional para a criação de um Cofre de Chaves premium em vez do Cofre de Chaves padrão que armazena as chaves protegidas por software. Para criar um Key Vault Premium, na etapa anterior, adicione os parâmetros *-SKU "Premium"*. O exemplo a seguir usa chaves protegidas por software, desde que criamos um Cofre de Chaves padrão. 

Para ambos os modelos de proteção, a plataforma do Azure deve ter acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Crie uma chave de criptografia em seu Key Vault com [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). O exemplo a seguir cria uma chave chamada *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Criar a entidade de serviço do Azure Active Directory
Quando os discos virtuais são criptografados ou descriptografados, especifique uma conta para lidar com a autenticação e a troca de chaves de criptografia do Key Vault. Essa conta, uma entidade de serviço do Azure Active Directory, permite que a plataforma do Azure solicite as chaves de criptografia apropriadas em nome da VM. Uma instância do Azure Active Directory padrão está disponível em sua assinatura, embora muitas organizações tenham diretórios do Azure Active Directory dedicados.

Crie uma entidade de serviço no Azure Active Directory com [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Para especificar uma senha segura, siga as [Políticas e restrições de senha do Azure Active Directory](../../active-directory/active-directory-passwords-policy.md):

```powershell
$appName = "My App"
$securePassword = "P@ssword!"
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Para criptografar ou descriptografar discos virtuais com êxito, as permissões na chave de criptografia armazenada no Key Vault devem ser definidas para permitir que a entidade de serviço do Azure Active Directory leia as chaves. Defina permissões no Key Vault com [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Criar máquina virtual
Para testar o processo de criptografia, vamos criar uma VM. O exemplo a seguir cria uma VM chamada *myVM* usando uma imagem do *Datacenter do Windows Server 2016*:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "mypublicdns$(Get-Random)"

$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$cred = Get-Credential

$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```


## <a name="encrypt-virtual-machine"></a>Criptografar máquina virtual
Para criptografar os discos virtuais, reúna todos os componentes anteriores:

1. Especifique a entidade de serviço e senha do Azure Active Directory.
2. Especifique o Cofre de chaves para armazenar os metadados para os discos criptografados.
3. Especifique as chaves de criptografia a serem usadas para criptografia e descriptografia.
4. Especifique se deseja criptografar o disco do sistema operacional, os discos de dados ou todos.

Criptografe sua VM com [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) usando a chave do Azure Key Vault e as credenciais de entidade de serviço do Azure Active Directory. O exemplo a seguir recupera todas as informações de chave e depois criptografa a VM chamada *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName $vmName `
    -AadClientID $app.ApplicationId `
    -AadClientSecret $securePassword `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Aceite o aviso para continuar com a criptografia da VM. A VM é reiniciada durante o processo. Após a conclusão do processo de criptografia e reinicialização da VM, examine o status de criptografia com [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName $vmName
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar o Azure Key Vault, confira [Configurar o Key Vault para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
