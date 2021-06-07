---
title: Verificar o status de criptografia para Linux – Azure Disk Encryption
description: Este artigo fornece instruções sobre como verificar o status de criptografia dos níveis da plataforma e do SO.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564128"
---
# <a name="verify-encryption-status-for-linux"></a>Verificar o status da criptografia para Linux 

O escopo deste artigo é validar o status de criptografia de uma máquina virtual usando métodos diferentes: o portal do Azure, o PowerShell, a CLI do Azure ou o sistema operacional da VM (máquina virtual). 

Você pode validar o status de criptografia durante ou após a criptografia realizando uma das seguintes ações:

- Verificar os discos anexados a uma VM específica. 
- Consultar as configurações de criptografia em cada disco, independentemente de o disco estar anexado ou desanexado.

Esse cenário se aplica às extensões de passagem dupla e de passagem única do Azure Disk Encryption. As distribuições do Linux são o único ambiente para esse cenário.

>[!NOTE] 
>Estamos usando variáveis em todo o artigo. Substitua os valores adequadamente.

## <a name="portal"></a>Portal

Na portal do Azure, dentro da seção **Extensões**, selecione a extensão Azure Disk Encryption na lista. As informações para **Mensagem de status** indicam o status de criptografia atual:

![Verificação do portal com status, versão e mensagem de status realçados](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na lista de extensões, você verá a versão de extensão do Azure Disk Encryption correspondente. A versão 0.x corresponde à passagem dupla do Azure Disk Encryption e a versão 1.x corresponde à passagem única do Azure Disk Encryption.

Você pode obter mais detalhes selecionando a extensão e, em seguida, selecionando **Exibir o status detalhado**. O status detalhado do processo de criptografia é exibido no formato JSON.

![Verificação do portal com o link "Exibir status detalhado" realçado](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Status detalhado no formato JSON](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Outra maneira de validar o status de criptografia é observando a seção **Configurações de disco**.

![Status de criptografia para disco do sistema operacional e discos de dados](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Esse status significa que as configurações de criptografia estão registradas nos discos, não que eles foram realmente criptografados no nível do sistema operacional.
>
> Por concepção, essas configurações são registradas nos discos primeiro e eles são criptografados posteriormente. Se o processo de criptografia falhar, esse processo de registro nos discos poderá ser concluído, mas o mesmo não ocorre para o processo de criptografia. 
>
> Para confirmar se os discos estão realmente criptografados, você pode verificar a criptografia de cada disco no nível do sistema operacional.

## <a name="powershell"></a>PowerShell

Você pode validar o status de criptografia *geral* de uma VM criptografada usando os seguintes comandos do PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Status de criptografia geral no PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Você pode capturar as configurações de criptografia de cada disco usando os comandos do PowerShell a seguir.

### <a name="single-pass"></a>Passagem única
Em uma passagem, as configurações de criptografia são registradas em cada um dos discos (SO e dados). Você pode capturar as configurações de criptografia para um disco do sistema operacional em uma passagem única, da seguinte maneira:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Configurações de criptografia para um disco do sistema operacional](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se não houver configurações de criptografia registradas no disco, a saída estará vazia:

![Saída vazia](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Use os seguintes comandos para capturar as configurações de criptografia para discos de dados:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Configurações de criptografia para discos de dados](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Passagem dupla
Em uma passagem dupla, as configurações de criptografia são registradas no modelo de VM e não em cada disco individual.

Para verificar se as configurações de criptografia foram registradas em uma passagem dupla, use os seguintes comandos:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Configurações de criptografia em uma passagem dupla](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Discos desconectados

Verifique as configurações de criptografia de discos que não estão anexados a uma VM.

### <a name="managed-disks"></a>Discos gerenciados
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>CLI do Azure

Você pode validar o status de criptografia *geral* de uma VM criptografada usando os seguintes comandos da CLI do Azure:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Status geral de criptografia da CLI do Azure ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passagem única
Você pode validar as configurações de criptografia de cada disco usando os seguintes comandos da CLI do Azure:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Configurações de criptografia de dados](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Se as configurações de criptografia não estiverem registradas no disco, você verá o texto **O disco não está criptografado**.

Use os comandos a seguir para obter configurações detalhadas de status e de criptografia.

Disco do sistema operacional:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Status detalhado e configurações de criptografia para o disco do sistema operacional](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Discos de dados:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Status detalhado e configurações de criptografia para os discos de dados](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Passagem dupla

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Configurações de criptografia gerais para passagem dupla por meio da CLI do Azure](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Você também pode verificar as configurações de criptografia no perfil de armazenamento do modelo de VM do disco do sistema operacional:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Perfil de VM para passagem dupla por meio da CLI do Azure](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Discos desconectados

Verifique as configurações de criptografia de discos que não estão anexados a uma VM.

### <a name="managed-disks"></a>Discos gerenciados

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Discos não gerenciados

Discos não gerenciados são arquivos VHD armazenados como blobs de páginas nas contas de armazenamento do Microsoft Azure.

Para obter os detalhes de um disco específico, você precisa fornecer:

- A ID da conta de armazenamento que contém o disco.
- Uma cadeia de conexão para essa conta de armazenamento específica.
- O nome do contêiner que armazena o disco.
- O nome do disco.

Este comando lista todas as IDs de todas as suas contas de armazenamento:

```azurecli
az storage account list --query [].[id] -o tsv
```
As IDs da conta de armazenamento são listadas no seguinte formato:

/subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /Providers/Microsoft.Storage/storageAccounts/\<storage account name>

Selecione a ID apropriada e armazene-a em uma variável:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Esse comando obtém a cadeia de conexão para uma conta de armazenamento específica e a armazena em uma variável:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

O seguinte comando lista todos os contêineres em uma conta de armazenamento:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
O contêiner usado para discos normalmente é denominado "vhds".

Armazene o nome do contêiner em uma variável: 
```bash
ContainerName="name of the container"
```

Use este comando para listar todos os BLOBs em um contêiner específico:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Escolha o disco que você deseja consultar e armazene o nome dele em uma variável:
```bash
DiskName="diskname.vhd"
```
Consultar as configurações de criptografia de disco:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Sistema operacional
Validar se as partições de disco de dados estão criptografadas (e o disco do sistema operacional não está).

Quando uma partição ou um disco está criptografado, ele é exibido como um tipo **crypt**. Quando não estiver criptografado, ele será exibido como um tipo **part/disk**.

```bash
lsblk
```

![Camada cript do sistema operacional para uma partição](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Você pode obter mais detalhes de configuração usando a variante **lsblk** a seguir. 

Você verá uma camada de tipo **crypt** que é montada pela extensão. O exemplo a seguir mostra volumes lógicos e discos normais com **crypto\_LUKS FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Camada crypt do sistema operacional para volumes lógicos e discos normais](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Como uma etapa extra, você pode validar se o disco de dados tem alguma chave carregada:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

E você pode verificar quais dispositivos **dm** estão listados como **crypt**:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
