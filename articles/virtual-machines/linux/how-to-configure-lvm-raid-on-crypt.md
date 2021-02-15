---
title: Configurar o LVM e o RAID em dispositivos criptografados-Azure Disk Encryption
description: Este artigo fornece instruções para configurar o LVM e o RAID em dispositivos criptografados para VMs do Linux.
author: jofrance
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3f90d5a95d153405f9257258fba6ab9cc1ce9a35
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681295"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Configurar o LVM e o RAID em dispositivos criptografados

Este artigo é um processo passo a passo sobre como executar o LVM (gerenciamento de volume lógico) e RAID em dispositivos criptografados. O processo se aplica aos seguintes ambientes:

- Distribuições Linux
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure Disk Encryption extensão de passagem única
- Extensão de passagem dupla Azure Disk Encryption


## <a name="scenarios"></a>Cenários

Os procedimentos neste artigo dão suporte aos seguintes cenários:  

- Configurar o LVM sobre dispositivos criptografados (LVM-on-cript)
- Configurar o RAID sobre dispositivos criptografados (RAID em criptografia)

Depois que o dispositivo ou dispositivos subjacentes forem criptografados, você poderá criar as estruturas LVM ou RAID sobre essa camada criptografada. 

Os volumes físicos (PVs) são criados na parte superior da camada criptografada. Os volumes físicos são usados para criar o grupo de volumes. Você cria os volumes e adiciona as entradas necessárias em/etc/fstab. 

![Diagrama das camadas de estruturas LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

De forma semelhante, o dispositivo RAID é criado no topo da camada criptografada nos discos. Um sistema de arquivos é criado na parte superior do dispositivo RAID e adicionado ao/etc/fstab como um dispositivo normal.

## <a name="considerations"></a>Considerações

Recomendamos que você use LVM em cript. O RAID é uma opção quando o LVM não pode ser usado devido a limitações específicas de aplicativo ou ambiente.

Você usará a opção **EncryptFormatAll** . Para obter mais informações sobre essa opção, consulte [usar o recurso EncryptFormatAll para discos de dados em VMs do Linux](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Embora você possa usar esse método quando também estiver criptografando o sistema operacional, estamos apenas criptografando as unidades de dados aqui.

Os procedimentos pressupõem que você já tenha revisado os pré-requisitos em [cenários de Azure Disk Encryption em VMs do Linux](./disk-encryption-linux.md) e no [início rápido: criar e criptografar uma VM do linux com o CLI do Azure](./disk-encryption-cli-quickstart.md).

A Azure Disk Encryption versão de passagem dupla está em um caminho de substituição e não deve mais ser usada em novas criptografias.

## <a name="general-steps"></a>Etapas gerais

Quando você estiver usando as configurações "incompreensíveis", use o processo descrito nos procedimentos a seguir.

>[!NOTE] 
>Estamos usando variáveis em todo o artigo. Substitua os valores adequadamente.

### <a name="deploy-a-vm"></a>Implantar uma máquina virtual 
Os comandos a seguir são opcionais, mas é recomendável aplicá-los em uma VM (máquina virtual) implantada recentemente.

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI do Azure:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Anexar discos à VM
Repita os comandos a seguir para o `$N` número de novos discos que você deseja anexar à VM.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

CLI do Azure:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Verifique se os discos estão anexados à VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista de discos anexados no PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

CLI do Azure:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista de discos anexados no CLI do Azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista de discos anexados no portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

sistema operacional:

```bash
lsblk 
```
![Lista de discos anexados no sistema operacional](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Configurar os discos a serem criptografados
Essa configuração é feita no nível do sistema operacional. Os discos correspondentes são configurados para uma criptografia tradicional por meio de Azure Disk Encryption:

- Os sistemas de arquivos são criados em cima dos discos.
- Pontos de montagem temporários são criados para montar os sistemas de arquivos.
- Os sistemas de arquivos são configurados em/etc/fstab para serem montados no momento da inicialização.

Verifique a letra do dispositivo atribuída aos novos discos. Neste exemplo, estamos usando quatro discos de dados.

```bash
lsblk 
```
![Discos de dados anexados ao sistema operacional](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Criar um sistema de arquivos na parte superior de cada disco
Esse comando itera a criação de um sistema de arquivos EXT4 em cada disco definido na parte "em" do ciclo "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Criação de um sistema de arquivos EXT4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Localize o identificador universal exclusivo (UUID) dos sistemas de arquivos que você criou recentemente, crie uma pasta temporária, adicione as entradas correspondentes em/etc/fstab e monte todos os sistemas de arquivos.

Esse comando também itera em cada disco definido na parte "em" do ciclo "for":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Verificar se os discos estão montados corretamente
```bash
lsblk
```
![Lista de sistemas de arquivos temporários montados](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Verifique também se os discos estão configurados:

```bash
cat /etc/fstab
```
![Informações de configuração via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Criptografar os discos de dados
PowerShell usando uma chave de criptografia de chave (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

CLI do Azure usando um KEK:

```azurecli
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Verificar o status de criptografia

Continue para a próxima etapa somente quando todos os discos forem criptografados.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Status de criptografia no PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

CLI do Azure:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Status de criptografia no CLI do Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Status de criptografia no portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Nível do sistema operacional:

```bash
lsblk
```
![Status de criptografia no sistema operacional](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A extensão adicionará os sistemas de arquivos ao/var/lib/azure_disk_encryption_config/azure_crypt_mount (uma criptografia antiga) ou a/etc/crypttab (novas criptografias).

>[!NOTE] 
>Não modifique nenhum desses arquivos.

Esse arquivo cuidará da ativação desses discos durante o processo de inicialização para que o LVM ou o RAID possam usá-los mais tarde. 

Não se preocupe com os pontos de montagem neste arquivo. Azure Disk Encryption perderá a capacidade de obter os discos montados como um sistema de arquivos normal depois de criarmos um volume físico ou um dispositivo RAID sobre esses dispositivos criptografados. (Isso removerá o formato do sistema de arquivos usado durante o processo de preparação.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Remover as pastas temporárias e as entradas de fstab temporárias
Desmonte os sistemas de arquivos nos discos que serão usados como parte do LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
E remova as entradas/etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verifique se os discos não estão montados e se as entradas em/etc/fstab foram removidas

```bash
lsblk
```
![Verificação de que os sistemas de arquivos temporários estão desmontados](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

E verifique se os discos estão configurados:
```bash
cat /etc/fstab
```
![Verificação de que as entradas de fstab temporárias são removidas](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Etapas para LVM-on-cript
Agora que os discos subjacentes estão criptografados, você pode criar as estruturas LVM.

Em vez de usar o nome do dispositivo, use os caminhos/dev/mapper para cada um dos discos para criar um volume físico (na camada cript sobre o disco, não no próprio disco).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configurar o LVM na parte superior das camadas criptografadas
#### <a name="create-the-physical-volumes"></a>Criar os volumes físicos
Você receberá um aviso perguntando se está OK para apagar a assinatura do sistema de arquivos. Continue inserindo **y** ou use **echo "y"** , conforme mostrado:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verificação de que um volume físico foi criado](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Os nomes de/dev/mapper/Device aqui precisam ser substituídos para seus valores reais com base na saída de **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verificar as informações de volumes físicos
```bash
pvs
```

![Informações para volumes físicos](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Criar o grupo de volumes
Crie o grupo de volumes usando os mesmos dispositivos já inicializados:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Verifique as informações do grupo de volumes

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informações para o grupo de volumes](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Criar volumes lógicos

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Verificar os volumes lógicos criados

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informações para volumes lógicos](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Criar sistemas de arquivos sobre as estruturas para volumes lógicos

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Criar os pontos de montagem para os novos sistemas de arquivos

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Adicionar os novos sistemas de arquivos ao/etc/fstab e montá-los

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Verificar se os novos sistemas de arquivos estão montados

```bash
lsblk -fs
df -h
```
![Captura de tela mostra uma janela de console com sistemas de arquivos montados como data0 e Data1.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Nessa variação de **lsblk**, estamos listando os dispositivos que mostram as dependências na ordem inversa. Essa opção ajuda a identificar os dispositivos agrupados pelo volume lógico em vez dos nomes de dispositivo/dev/sd [disco] originais.

É importante garantir que a opção **nofail** seja adicionada às opções de ponto de montagem dos volumes LVM criados na parte superior de um dispositivo criptografado por meio de Azure Disk Encryption. Isso impede que o sistema operacional fique preso durante o processo de inicialização (ou no modo de manutenção).

Se você não usar a opção **nofalhar** :

- O sistema operacional nunca entrará no estágio em que Azure Disk Encryption é iniciado e os discos de dados são desbloqueados e montados. 
- Os discos criptografados serão desbloqueados no final do processo de inicialização. Os volumes LVM e os sistemas de arquivos serão montados automaticamente até que Azure Disk Encryption Desbloqueie-os. 

Você pode testar a reinicialização da VM e validar se os sistemas de arquivos também estão automaticamente montados após o tempo de inicialização. Esse processo pode levar vários minutos, dependendo do número e dos tamanhos dos sistemas de arquivos.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Reinicialize a VM e verifique após a reinicialização

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Etapas para RAID-on-cript.
Agora que os discos subjacentes estão criptografados, você pode continuar criando as estruturas RAID. O processo é o mesmo do LVM, mas em vez de usar o nome do dispositivo, use os caminhos/dev/mapper para cada disco.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configurar o RAID sobre a camada criptografada dos discos
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informações para o RAID configurado por meio do comando mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Os nomes de/dev/mapper/Device aqui precisam ser substituídos pelos valores reais, com base na saída de **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Verificar/monitorar a criação de RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status do RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Criar um sistema de arquivos na parte superior do novo dispositivo RAID
```bash
mkfs.ext4 /dev/md10
```

Crie um novo ponto de montagem para o sistema de arquivos, adicione o novo sistema de arquivos ao/etc/fstab e monte-o:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Verifique se o novo sistema de arquivos está montado:

```bash
lsblk -fs
df -h
```
![Captura de tela mostra uma janela de console com um sistema de arquivos montado como raiddata.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

É importante certificar-se de que a opção **nofail** seja adicionada às opções de ponto de montagem dos volumes RAID criados na parte superior de um dispositivo criptografado por meio de Azure Disk Encryption. Isso impede que o sistema operacional fique preso durante o processo de inicialização (ou no modo de manutenção).

Se você não usar a opção **nofalhar** :

- O sistema operacional nunca entrará no estágio em que Azure Disk Encryption é iniciado e os discos de dados são desbloqueados e montados.
- Os discos criptografados serão desbloqueados no final do processo de inicialização. Os volumes RAID e os sistemas de arquivos serão montados automaticamente até que Azure Disk Encryption Desbloqueie-os.

Você pode testar a reinicialização da VM e validar se os sistemas de arquivos também estão automaticamente montados após o tempo de inicialização. Esse processo pode levar vários minutos, dependendo do número e dos tamanhos dos sistemas de arquivos.

```bash
shutdown -r now
```

E quando você pode fazer logon:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Próximas etapas

- [Redimensionar dispositivos de gerenciamento de volume lógico criptografados com Azure Disk Encryption](how-to-resize-encrypted-lvm.md)
- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
