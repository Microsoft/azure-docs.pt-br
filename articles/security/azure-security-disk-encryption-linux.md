---
title: Habilitar o Azure Disk Encryption para VMs de IaaS do Linux
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: ffdda51c4739dfad6c326a5c90f4a93ebfb321cd
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294893"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Habilitar o Azure Disk Encryption para VMs de IaaS do Linux 

Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários com mais detalhes para VMs da IaaS do Linux. Antes de poder usar criptografia de disco, o [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) deve ser concluída e o [pré-requisitos adicionais para VMs IaaS Linux](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms) seção deve ser revisada.

Tire um [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você pode usar o cmdlet Set-AzVMDiskEncryptionExtension para criptografar managed disks especificando o parâmetro - skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Se você usou o [Azure Disk Encryption com aplicativo do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) anteriormente para criptografar essa VM, você deverá continuar usando essa opção para criptografar a VM. Não é possível usar o [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nessa máquina virtual criptografada pois esse cenário não é suportado, o que significa abandonar o aplicativo Azure Active Directory nessa VM criptografada que ainda não tem suporte.
 > - O Azure Disk Encryption precisa que o cofre de chaves e as VMs estejam localizados na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada.
> - Ao criptografar volumes de sistema operacional Linux, a VM deve ser considerada indisponível. É altamente recomendável para evitar logons SSH, enquanto a criptografia está em andamento para evitar problemas de bloqueio de todos os arquivos abertos que precisam ser acessados durante o processo de criptografia. Para verificar o andamento, o [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [show de criptografia de vm](/cli/azure/vm/encryption#az-vm-encryption-show) comandos podem ser usados. Esse processo deve levar algumas horas para um volume do sistema operacional de 30 GB, mais algum tempo para criptografar volumes de dados. O tempo para criptografia de volume de dados será proporcional ao tamanho e à quantidade dos volumes de dados, a menos que a opção EncryptFormatAll seja usada. 
> - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução
Nesse cenário, é possível habilitar a criptografia usando o modelo do Resource Manager, os cmdlets do PowerShell ou os comandos da CLI. Se você precisar de informações de esquema para a extensão de máquina virtual, consulte o [Azure Disk Encryption para extensão do Linux](../virtual-machines/extensions/azure-disk-enc-linux.md) artigo.

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar os discos gerenciados, especificando o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension não funcionará em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro foi especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Habilitar criptografia em uma VM do Linux existente ou em execução usando CLI do Azure 
É possível habilitar a criptografia de disco no VHD criptografado, instalando e usando a ferramenta de linha de comando da [Azure CLI 2.0](/cli/azure)l. Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell. Para habilitar a criptografia em VMs da IaaS do Linux existentes ou em execução no Azure, use os comandos a seguir da CLI:

Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

- **Criptografar uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografar uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificar se os discos estão criptografados:** Para verificar o status da criptografia de uma VM IaaS, use o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>Habilitar criptografia em uma VM do Linux existente ou em execução usando PowerShell
Use o [AzVMDiskEncryptionExtension conjunto](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet para habilitar a criptografia em uma máquina virtual de IaaS em execução no Azure. Tirar uma [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou fazer backup da VM com [Backup do Azure](../backup/backup-azure-vms-encryption.md) antes que os discos são criptografados. O parâmetro - skipVmBackup já está especificado nos scripts do PowerShell para criptografar uma VM do Linux em execução.

-  **Criptografar uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores. Modifique o parâmetro - VolumeType para especificar quais discos que você está criptografando.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Criptografar uma VM em execução usando KEK:** Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verificar se os discos estão criptografados:** Para verificar o status da criptografia de uma VM IaaS, use o [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desabilitar criptografia de disco:** Para desabilitar a criptografia, use o [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM Linux IaaS existente ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Clique em **Implantar no Azure** no modelo de início rápido do Azure.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Criar** para habilitar a criptografia na VM da IaaS em execução ou existente.

A tabela a seguir lista os parâmetros de modelo do Resource Manager existente para VMs em execução ou existentes:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. É possível obtê-lo, usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` da CLI do Azure|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre de chaves|
|  keyEncryptionKeyURL | URL da chave de criptografia de chaves que é usada para criptografar a chave gerada do BitLocker. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. 
| forceUpdateTag | Passe um valor exclusivo como um GUID sempre que a execução da operação precise ser forçada. |
| resizeOSDisk | A partição do SO deve ser redimensionada para ocupar o VHD do SO completo antes de dividir o volume do sistema. |
| location | Local de todos os recursos. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Criptografar os conjuntos de dimensionamento de máquinas virtuais
Os [conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/overview.md) permitem criar e gerenciar um grupo de VMs idênticas e com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Use a CLI ou o Azure PowerShell para criptografar conjuntos de dimensionamento de máquinas virtuais. Apenas a criptografia de discos de dados tem suporte em máquinas de virtuais do conjunto de dimensionamento do Linux.

Um exemplo de arquivo em lotes para criptografia de disco de dados do conjunto de dimensionamento Linux pode ser encontrado [aqui](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Este exemplo cria um grupo de recursos e o conjunto de dimensionamento Linux, monta um disco de dados de 5 GB e criptografa o conjunto de dimensionamento de máquinas virtuais.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Criptografar conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure

Use [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) para habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows. Se você definir a política de atualização no conjunto de dimensionamento como manual, inicie a criptografia com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. 

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução usando KEK para encapsular a chave**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter o status de criptografia de um conjunto de dimensionamento de máquinas virtuais:** use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: use [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Criptografar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Use o [AzVmssDiskEncryptionExtension conjunto](/powershell/module/az.compute/set-azvmssdiskencryptionextension) cmdlet para habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos.

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução usando KEK para encapsular a chave**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter o status de criptografia de um conjunto de máquinas virtuais**: Use o [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) cmdlet.
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: Use o [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) cmdlet. 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Modelos do Azure Resource Manager para conjuntos de dimensionamento de máquinas virtuais do Linux

Para criptografar ou descriptografar conjuntos de dimensionamento de máquinas virtuais do Linux, use os modelos e instruções do Azure Resource Manager abaixo:

- [Habilitar criptografia em um conjunto de dimensionamento de máquinas virtuais do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Desabilitar a criptografia em um conjunto de dimensionamento de máquina virtual do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Clique em **Implantar no Azure**.
     2. Preencha os campos obrigatórios e concorde com os termos e condições.
     3. Clique em **Comprar** para implantar o modelo.

## <a name="bkmk_EFA"> </a>Use o recurso EncryptFormatAll para discos de dados em VMs da IaaS do Linux

O parâmetro **EncryptFormatAll** reduz o tempo de criptografia dos discos de dados do Linux. As partições que atendem a determinados critérios serão formatadas (com o sistema de arquivos atual). Em seguida, serão remontadas de volta para onde estavam antes da execução do comando. Se você quiser excluir um disco de dados que atenda aos critérios, será possível desmontá-lo antes de executar o comando.

 Após executar esse comando, todas as unidades que foram montadas anteriormente serão formatadas. Em seguida, a camada de criptografia será iniciada na parte superior da unidade agora vazia. Quando essa opção for selecionada, o disco de recurso efêmero anexado à VM também será criptografado. Se a unidade temporária for reiniciada, ela será reformatada e criptografada novamente para a VM pela solução do Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> O EncryptFormatAll não deverá ser usado quando houver dados necessários nos volumes de dados de uma VM. É possível excluir discos da criptografia, desmontando-os. Primeiro será necessário testar o EncryptFormatAll, primeiro em uma VM de teste, e compreender o parâmetro de recurso e a respectiva implicação antes de testá-lo na VM de produção. A opção EncryptFormatAll formata o disco de dados e todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que deseja excluir estão corretamente desmontados. </br></br>
 >Se você estiver configurando esse parâmetro ao atualizar as configurações de criptografia, isso poderá levar a um reinício antes da criptografia real. Nesse caso, é recomendável também remover o disco que você não quer que seja formatado no arquivo fstab. Da mesma forma, é necessário adicionar a partição que deseja criptografar ao arquivo fstab antes de iniciar a operação de criptografia. 

### <a name="bkmk_EFACriteria"> </a> Critérios do EncryptFormatAll
O parâmetro passa por todas as partições e criptografa-as desde que atendam a **todos** os critérios abaixo: 
- Não é uma partição de inicialização/SO/raiz
- Ainda não está criptografado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="bkmk_EFAPSH"> </a> Usar o parâmetro EncryptFormatAll com a CLI do Azure
Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Criptografe uma VM em execução usando EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use o [AzVMDiskEncryptionExtension conjunto](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet com o parâmetro EncryptFormatAll. 

**Criptografe uma VM em execução usando EncryptFormatAll:** Por exemplo, o script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="bkmk_EFALVM"> </a> Usar o parâmetro EncryptFormatAll com LVM (Gerenciador de Volume Lógico) 
É recomendável uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem pelo que melhor adequar-se ao seu caso de uso. Essa configuração pode ser feita da seguinte maneira:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar esses discos ao arquivo fstab.

    1. Formate o disco adicionado recentemente. Usamos symlinks gerados pelo Azure aqui. O uso de symlinks evita problemas relacionados à alteração de nomes de dispositivos. Para obter mais informações, consulte o artigo [Solucionar problemas de Nomes do Dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adicione ao fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Execute o cmdlet do PowerShell Set-AzVMDiskEncryptionExtension com - EncryptFormatAll para criptografar esses discos.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configure a LVM sobre esses novos discos. Observe que as unidades criptografadas serão desbloqueadas após a VM concluir a inicialização. Portanto, a montagem de LVM também deverá ser subsequentemente atrasada.


## <a name="bkmk_VHDpre"> </a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente
Nesse cenário, é possível habilitar a criptografia usando cmdlets do PowerShell ou comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD Linux previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar os discos gerenciados, especificando o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension não funcionará em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro foi especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 



### <a name="bkmk_VHDprePSH"> </a> Usar o Azure PowerShell para criptografar VMs da IaaS com VHDs previamente criptografados 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [AzVMOSDisk conjunto](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente

É possível adicionar um novo disco de dados usando [az vm disk attach](../virtual-machines/linux/add-disk.md) ou [por meio do portal do Azure](../virtual-machines/linux/attach-disk-portal.md). Antes de poder criptografar, primeiro será necessário montar o disco de dados anexado recentemente. É necessário solicitar a criptografia da unidade de dados, pois a unidade ficará inutilizável enquanto a criptografia estiver em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure

 Se a VM foi previamente criptografada com "All", então o --parâmetro de tipo de volume deve permanecer como Todos. Todos inclui o sistema operacional e os discos de dados. Se a VM foi previamente criptografada com um tipo de volume de "OS", então o parâmetro --volume-type deverá ser alterado para All para que ambos o sistema operacional e o novo disco de dados sejam incluídos. Se a VM foi criptografada apenas com o tipo de volume "Dados", ela poderá permanecer como "Dados", conforme demonstrado abaixo. Adicionar e anexar um novo disco de dados a uma VM não é preparação suficiente para criptografia. O disco anexado recentemente também deve ser formatado e montado adequadamente na VM antes de habilitar a criptografia. No Linux, o disco deve ser montado em /etc/fstab com um [nome do dispositivo de bloco persistente](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Em contraste com a sintaxe do Powershell, a CLI não exige que o usuário forneça uma versão de sequência exclusiva ao habilitar a criptografia. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.

-  **Criptografe volumes de dados de uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe volumes de dados de uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o Powershell para criptografar um novo disco para Linux, uma nova versão da sequência deverá ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. Tirar uma [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou fazer backup da VM com [Backup do Azure](../backup/backup-azure-vms-encryption.md) antes que os discos são criptografados. O parâmetro - skipVmBackup já está especificado nos scripts do PowerShell para criptografar um disco de dados adicionado recentemente.
 

-  **Criptografe volumes de dados de uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores. Valores aceitáveis para o parâmetro do tipo de volume são Todos, SO e Dados. Se a VM foi criptografada anteriormente com um tipo de volume de "OS" ou “ALL”, então o parâmetro -VolumeType deverá ser alterado para All para que ambos o sistema operacional e o novo disco de dados sejam incluídos.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Criptografe volumes de dados de uma VM em execução usando KEK:** Valores aceitáveis para o parâmetro do tipo de volume são Todos, SO e Dados. Se a VM foi criptografada anteriormente com um tipo de volume de "OS" ou “ALL”, então o parâmetro -VolumeType deverá ser alterado para All para que ambos o sistema operacional e o novo disco de dados sejam incluídos.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro disk-encryption-keyvault é a cadeia de caracteres do identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desabilitar criptografia para VMs do Linux
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

- **Desabilitar a criptografia de disco com o Azure PowerShell:** Para desabilitar a criptografia, use o [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Desabilitar a criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** Use o modelo [Desabilitar a criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) para desabilitar a criptografia.
     1. Clique em **Implantar no Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
     3.  Clique em **Comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Windows](azure-security-disk-encryption-windows.md)
