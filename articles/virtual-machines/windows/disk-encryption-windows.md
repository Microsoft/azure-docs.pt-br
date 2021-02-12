---
title: Cenários de Azure Disk Encryption em VMs Windows
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs do Windows para vários cenários
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: df74a15a84ba04addb75b021d3b77b06cfe2ea48
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781106"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Cenários de Azure Disk Encryption em VMs Windows

Azure Disk Encryption para VMs (máquinas virtuais) do Windows usa o recurso BitLocker do Windows para fornecer criptografia de disco completa do disco do sistema operacional e do disco de dados. Além disso, ele fornece criptografia do disco temporário quando o parâmetro VolumeType é All.

O Azure Disk Encryption [é integrado ao Azure Key Vault](disk-encryption-key-vault.md) para ajudar você a controlar e gerenciar as chaves de criptografia de disco e os segredos. Para obter uma visão geral do serviço, consulte [Azure Disk Encryption para VMs do Windows](disk-encryption-overview.md).

Você só pode aplicar a criptografia de disco a máquinas virtuais de [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems). Você também deve atender aos seguintes pré-requisitos:

- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Se você já tiver usado o Azure Disk Encryption com o Azure AD anteriormente para criptografar uma VM, deverá continuar usando essa opção para criptografar a VM. Confira [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para detalhes. 
>
> - Você deve [fazer um instantâneo](snapshot-copy-managed-disk.md) e/ou criar um backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você poderá usar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para criptografar discos gerenciados, especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte [fazer backup e restaurar a VM do Azure criptografada](../../backup/backup-azure-vms-encryption.md). 
>
> - Criptografar ou desabilitar a criptografia pode fazer com que uma VM seja reinicializada.

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Habilitar a criptografia em uma VM do Windows existente ou em execução
Nesse cenário, é possível habilitar a criptografia usando o modelo do Resource Manager, os cmdlets do PowerShell ou os comandos da CLI. Se você precisar de informações de esquema para a extensão de máquina virtual, consulte o [Azure Disk Encryption para extensão do artigo Windows](../extensions/azure-disk-enc-windows.md).

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a> Habilitar criptografia em VMs existentes ou em execução com Azure PowerShell 
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. 

-  **Criptografar uma VM em execução:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografar uma VM em execução usando KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). Desabilitar a criptografia de disco de dados na VM do Windows quando os discos de dados e o sistema operacional foram criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Habilitar a criptografia em VMs existentes ou em execução com o CLI do Azure
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
     > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar a criptografia de disco de dados na VM do Windows quando os discos de dados e o sistema operacional foram criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Usando o modelo do Gerenciador de Recursos

É possível habilitar a criptografia de disco em VMs do Windows da IaaS em execução ou existentes no Azure usando o [modelo do Resource Manager para criptografar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local, as configurações, os termos legais e o contrato. Clique em **Comprar** para habilitar a criptografia na VM da IaaS em execução ou existente.

A tabela a seguir lista os parâmetros de modelo do Resource Manager existentes para as VMs em execução ou existentes:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. É possível obtê-lo, usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando `az keyvault list --resource-group "MyKeyVaultResourceGroup"` da CLI do Azure|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre de chaves|
|  keyEncryptionKeyURL | A URL da chave de criptografia de chave, no formato https:// &lt; keyvault-name &gt; . Vault.Azure.net/Key/ &lt; Key-Name &gt; . Se você não quiser usar um KEK, deixe esse campo em branco. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. 
| forceUpdateTag | Passe um valor exclusivo como um GUID sempre que a execução da operação precise ser forçada. |
| resizeOSDisk | A partição do SO deve ser redimensionada para ocupar o VHD do SO completo antes de dividir o volume do sistema. |
| local | Local de todos os recursos. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Habilitar a criptografia em discos de NVMe para VMs Lsv2

Este cenário descreve a habilitação de Azure Disk Encryption em discos de NVMe para VMs da série Lsv2.  A série Lsv2 apresenta o armazenamento de NVMe local. Os discos do NVMe local são temporários e os dados serão perdidos nesses discos se você parar/desalocar sua VM (consulte: [Lsv2-Series](../lsv2-series.md)).

Para habilitar a criptografia em discos do NVMe:

1. Inicialize os discos do NVMe e crie volumes NTFS.
1. Habilite a criptografia na VM com o parâmetro Volumetype definido como todos. Isso habilitará a criptografia para todos os discos de sistema operacional e de dados, incluindo volumes apoiados por discos de NVMe. Para obter informações, consulte [habilitar a criptografia em uma VM do Windows existente ou em execução](#enable-encryption-on-an-existing-or-running-windows-vm).

A criptografia persistirá nos discos de NVMe nos seguintes cenários:
- Reinicialização da VM
- Refazer imagem do VMSS
- Sistema operacional de permuta

Os discos de NVMe não serão inicializados nos seguintes cenários:

- Iniciar VM após desalocação
- Recuperação de serviço
- Backup

Nesses cenários, os discos de NVMe precisam ser inicializados depois que a VM é iniciada. Para habilitar a criptografia nos discos do NVMe, execute o comando para habilitar Azure Disk Encryption novamente depois que os discos de NVMe forem inicializados.

Além dos cenários listados na seção [cenários sem suporte](#unsupported-scenarios) , a criptografia de discos de NVMe não tem suporte para:

- VMs criptografadas com Azure Disk Encryption com o AAD (versão anterior)
- Discos de NVMe com espaços de armazenamento
- Azure Site Recovery de SKUs com discos de NVMe (consulte [matriz de suporte para recuperação de desastre de VM do Azure entre regiões do Azure: máquinas replicadas-armazenamento](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente

Nesse cenário, você pode criar uma nova VM de um VHD previamente criptografado e as chaves de criptografia associadas usando cmdlets do PowerShell ou comandos da CLI. 

Use as instruções em [preparar um VHD do Windows previamente criptografado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> Criptografar VMs com VHDs previamente criptografados com Azure PowerShell
É possível habilitar a criptografia de disco no VHD criptografado usando o cmdlet do PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
É possível [adicionar um novo disco a uma VM do Windows usando o PowerShell ](attach-disk-ps.md) ou [por meio do portal do Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o PowerShell para criptografar um novo disco para VMs do Windows, uma nova versão de sequência deve ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. Em alguns casos, um disco de dados adicionado recentemente pode ser criptografado automaticamente pela extensão do Azure Disk Encryption. A criptografia automática geralmente ocorre quando a VM é reinicializada depois que o novo disco fica online. Isso geralmente é causado porque "All" foi especificado para o tipo de volume quando a criptografia de disco foi executada anteriormente na VM. Se a criptografia automática ocorrer em um disco de dados recém-adicionado, recomendamos executar o cmdlet Set-AzVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o seu novo disco de dados for criptografado automaticamente e você não quiser ser criptografado, primeiro descriptografe todas as unidades e criptografe novamente com uma nova versão de sequência, especificando o SO para o tipo de volume. 
  
 

-  **Criptografar uma VM em execução:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores. Este exemplo usa "All" para o parâmetro -VolumeType, que inclui ambos os volumes de Dados e SO. Se você quiser apenas criptografar o volume do SO, use "OS" para o parâmetro -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Criptografar uma VM em execução usando KEK:** Este exemplo usa "All" para o parâmetro -VolumeType, que inclui ambos os volumes de Dados e SO. Se você quiser apenas criptografar o volume do SO, use "OS" para o parâmetro -VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão da sequência quando você executar o comando para habilitar a criptografia. Os exemplos usam "All" para o parâmetro do tipo de volume. Talvez seja necessário alterar o parâmetro do tipo de volume para disco do SO. Em contraste com a sintaxe do PowerShell, a CLI não exige que o usuário forneça uma versão de sequência exclusiva ao habilitar a criptografia. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.   

-  **Criptografar uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografar uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desabilitar criptografia
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Azure Disk Encryption não funciona para os seguintes cenários, recursos e tecnologia:

- criptografia de VM de camada básica ou VMs criadas por meio do método de criação de VM clássico.
- Criptografia de VMs configuradas com sistemas RAID baseados em software.
- Criptografia de VMs configuradas com o Espaços de Armazenamento Diretos (S2D) ou versões do Windows Server anteriores a 2016 configuradas com espaços de armazenamento do Windows.
- Integração ao sistema de gerenciamento de chaves local.
- Arquivos do Azure (sistema de arquivo compartilhado).
- NFS (Network File System).
- Volumes dinâmicos.
- Contêineres do Windows Server, que criam volumes dinâmicos para cada contêiner.
- Discos do SO Efêmero.
- Criptografia de sistemas de arquivos compartilhados/distribuídos como (mas não se limitando a) DFS, GFS, DRDB e CephFS.
- Movendo VMs criptografadas para outra assinatura ou região.
- Criar uma imagem ou um instantâneo de uma VM criptografada e usá-la para implantar VMs adicionais.
- VMs Gen2 (consulte: [suporte para VMs de geração 2 no Azure](../generation-2.md#generation-1-vs-generation-2-capabilities))
- VMs da série M com discos Acelerador de Gravação.
- Aplicando ADE a uma VM que tem discos criptografados com [criptografia do lado do servidor com chaves gerenciadas pelo cliente](../disk-encryption.md) (SSE + CMK). A aplicação de SSE + CMK a um disco de dados em uma VM criptografada com ADE também é um cenário sem suporte.
- Migrar uma VM criptografada com ADE ou **já** foi criptografada com Ade, para a [criptografia do lado do servidor com chaves gerenciadas pelo cliente](../disk-encryption.md).
- [Tamanhos de VM do Azure sem disco temporário local](../azure-vms-no-temp-disk.md); especificamente, DV4, Dsv4, Ev4 e Esv4.
- Criptografia de VMs em clusters de failover.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption scripts de exemplo](disk-encryption-sample-scripts.md)
- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)