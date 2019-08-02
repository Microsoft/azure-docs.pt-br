---
title: 'Apêndice: Azure Disk Encryption para VMs IaaS | Microsoft Docs'
description: Este artigo é o apêndice do Microsoft Azure Disk Encryption para VMs da IaaS do Windows e Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2872d106eea56a37c362195e7a3250058336768b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295050"
---
# <a name="appendix-for-azure-disk-encryption"></a>Apêndice para Azure Disk Encryption 

Este artigo é um apêndice para [Azure Disk Encryption para VMs da IaaS](azure-security-disk-encryption-overview.md). Primeiro leia os artigos do Azure Disk Encryption para VMs da IaaS para compreender o contexto. Este artigo descreve como preparar VHDs pré-criptografados e outras tarefas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Conecte-se as suas assinaturas
Antes de começar, veja o artigo [Pré-requisitos](azure-security-disk-encryption-prerequisites.md). Depois que todos os pré-requisitos forem atendidos, conecte-se à sua assinatura executando os seguintes cmdlets:

### <a name="bkmk_ConnectPSH"></a> Conectar a assinatura com PowerShell

1. Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure com o seguinte comando:

     ```powershell
     Connect-AzAccount 
     ```
2. Se tiver várias assinaturas e quiser especificar uma a ser usada, digite o seguinte para ver as assinaturas da sua conta:
     
     ```powershell
     Get-AzSubscription
     ```
3. Para especificar a assinatura que você quer usar, digite:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Para verificar se a assinatura configurada está correta, digite:
     
     ```powershell
     Get-AzSubscription
     ```
5. Se necessário, conecte o Azure AD com [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Para confirmar que os cmdlets de Azure Disk Encryption estão instalados, digite:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Revise [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps) e [AzureAD](/powershell/module/azuread), se necessário.

### <a name="bkmk_ConnectCLI"></a> Conectar a assinatura com CLI do Azure

1. Entre no Azure com [az login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Se você deseja selecionar um locatário a ser usado para entrar, use:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Se você tiver várias assinaturas e quiser especificar uma lista específica, obtenha a lista de assinaturas com [az account list](/cli/azure/account#az-account-list) e especifique com [az account set](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Verifique a versão instalada.
     
     ```azurecli
        az --version
     ``` 

5. Revise a [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli), se necessário. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exemplos de scripts do PowerShell para Azure Disk Encryption 

- **Listar todas as VMs criptografadas na assinatura**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Listar todos os segredos de criptografia de disco usados para criptografar VMs em um cofre de chaves** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Usando o script do PowerShell de pré-requisitos do Azure Disk Encryption
Se você já estiver familiarizado com os pré-requisitos do Azure Disk Encryption, use o [script do PowerShell de pré-requisitos do Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter um exemplo de como usar esse script do PowerShell, confira o [Guia de início rápido para criptografar uma VM](quick-encrypt-vm-powershell.md). Você pode remover os comentários de uma seção do script, começando na linha 211, para criptografar todos os discos de VMs existentes em um grupo de recursos existente. 

A tabela a seguir mostra quais parâmetros podem ser usados no script do PowerShell: 


|Parâmetro|DESCRIÇÃO|É obrigatório|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos ao qual o KeyVault pertence.  Um grupo de recursos com esse nome será criado caso ele ainda não exista.| True|
|$keyVaultName|Nome do KeyVault no qual as chaves de criptografia devem ser colocadas. Um cofre com esse nome será criado caso ele ainda não exista.| True|
|$location|Local do KeyVault. Verifique se o KeyVault e as VMs a serem criptografadas estão no mesmo local. Obtenha uma lista de locais com `Get-AzLocation`.|True|
|$subscriptionId|Identificador da assinatura do Azure a ser usada.  Você pode obter sua ID de assinatura com `Get-AzSubscription`.|True|
|$aadAppName|Nome do aplicativo do Azure AD que será usado para gravar segredos no KeyVault. Será criado um novo aplicativo com esse nome caso ele não exista. Se esse aplicativo já existir, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente do aplicativo do Azure AD que já foi criado.|Falso|
|$keyEncryptionKeyName|Nome da chave de criptografia da chave opcional no KeyVault. Uma chave com esse nome será criada caso ela ainda não exista.|Falso|


## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografar ou descriptografar VMs sem um aplicativo do Azure AD


- [Habilitar criptografia de disco em VMs do Windows da IaaS em execução ou existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Desabilitar a criptografia de disco em VMs de IaaS do Windows existentes ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Habilitar criptografia de disco em uma VM do Linux da IaaS em execução ou existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Desabilitar criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.  

### <a name="encrypt-or-decrypt-virtual-machine-scale-sets"></a>Criptografar ou descriptografar conjuntos de dimensionamento de máquina virtual

- [Habilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Habilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Implantar um conjunto de dimensionamento de VMs do Linux com um jumpbox e habilita a criptografia em VMSS do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Implantar um conjunto de dimensionamento de máquinas virtuais do Windows com um jumpbox e habilita a criptografia em VMSS do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Desabilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Desabilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografar ou descriptografar VMs com um aplicativo do Azure AD (versão anterior) 
 
- [Habilitar criptografia de disco em VMs do Windows da IaaS em execução ou existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Habilitar criptografia de disco em uma VM do Linux da IaaS em execução ou existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Desabilitar criptografia de disco em IaaS do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Desabilitar criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux. 

- [Habilitar criptografia de disco na nova VM do Windows da IaaS do Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Esse modelo cria uma nova VM do Windows criptografada que usa a imagem de galeria do Windows Server 2012.

- [Criar uma nova VM criptografada de Disco Gerenciado da IaaS do Windows a partir da imagem da galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Esse modelo cria uma nova VM do Windows criptografada com discos gerenciados usando a imagem da galeria do Windows Server 2012.

- [Criar um novo disco gerenciado criptografado a partir de um blob de armazenamento/VHD previamente criptografado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um disco gerenciado criptografado fornecido usando um VHD pré-criptografado e as configurações de criptografia correspondentes

- [Habilitar a criptografia de disco em uma VM do Windows em execução usando uma impressão digital de certificado do cliente do Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Prepare um VHD do Windows previamente criptografado
As seções a seguir são necessárias para preparar um VHD do Windows previamente criptografado para implantação como um VHD criptografado no Azure IaaS. Use as informações para preparar e inicializar uma nova VHD (VM do Windows) no Azure Site Recovery ou no Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir não TPM na proteção do sistema operacional
Configure a política de grupo do BitLocker **Criptografia de Unidade de Disco BitLocker**, que você encontrará em **Política de Computador Local** > **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows**. Altere essa configuração para: **Unidades do Sistema Operacional** > **Exigir autenticação adicional na inicialização** > **Permitir BitLocker sem TPM compatível**, como mostrado na seguinte figura:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de recursos do BitLocker
Para o Windows Server 2012 e versões posteriores, use o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, use o seguinte comando:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume do sistema operacional para o BitLocker usando `bdehdcfg`
Para compactar a partição do SO e preparar o computador para BitLocker, execute o [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment), se necessário:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Use o comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) para habilitar a criptografia no volume de inicialização usando um protetor de chave externo. Também coloque a chave externa (arquivo .bek) na unidade ou no volume. A criptografia é habilitada no volume de inicialização/sistema após a próxima reinicialização.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um VHD de dados/recursos separado para obter a chave externa usando o BitLocker.

## <a name="bkmk_LinuxRunning"></a> Criptografar uma unidade do SO em uma VM do Linux em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para a criptografia de disco do sistema operacional

* A VM deve estar usando uma distribuição compatível com a criptografia de disco do sistema operacional conforme listado no [sistemas operacionais com suporte do Azure Disk Encryption: Linux](azure-security-disk-encryption-prerequisites.md#linux) 
* A VM deve ser criada com base na imagem do Marketplace no Azure Resource Manager.
* VM do Azure com, no mínimo, 4 GB de RAM (o tamanho recomendável é de 7 GB).
* (Para RHEL e CentOS) Desabilite o SELinux. Para desabilitar SELinux, confira "4.4.2. Desabilitando o SELinux" no [Guia do Administrador e Usuário do SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na VM.
* Depois de desabilitar o SELinux, reinicialize a VM pelo menos uma vez.

### <a name="steps"></a>Etapas
1. Crie uma VM usando uma das distribuições especificadas anteriormente.

   Para o CentOS 7.2, há suporte para a criptografia de disco do sistema operacional por meio de uma imagem especial. Para usar essa imagem, especifique "7.2n" como o SKU quando você criar a VM:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configure a VM de acordo com suas necessidades. Se você for criptografar todas as unidades (sistema operacional + dados), as unidades de dados precisarão ser especificadas e montadas em /etc/fstab.

   > [!NOTE]
   > Use UUID=... para especificar unidades de dados em/etc/fstab em vez de especificar o nome do dispositivo de blocos (por exemplo, /dev/sdb1). Durante a criptografia, a ordem das é alterada na VM. Se a VM se basear em uma ordem específica de dispositivos de blocos, ela não conseguirá montá-los após a criptografia.

3. Saia das sessões do SSH.

4. Para criptografar o SO, especifique volumeType como **Todos** ou **SO** ao habilitar a criptografia.

   > [!NOTE]
   > Todos os processos de espaço de usuário que não estão sendo executados como serviços `systemd` deverão ser encerrados com um `SIGKILL`. Reinicialize a VM. Ao habilitar a criptografia de disco do sistema operacional em uma VM em execução, planeje o tempo de inatividade da VM.

5. Periodicamente, monitore o progresso da criptografia usando as instruções da [próxima seção](#monitoring-os-encryption-progress).

6. Depois de Get-AzVmDiskEncryptionStatus mostrar "VMRestartPending", reinicie a VM entrando nele ou usando o portal, PowerShell ou CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Antes de reinicializar, recomendamos que você salve os [diagnósticos de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) da VM.

## <a name="monitoring-os-encryption-progress"></a>Monitorando o progresso da criptografia do sistema operacional
Você pode monitorar o progresso de criptografia do sistema operacional de três maneiras:

* Use o cmdlet `Get-AzVmDiskEncryptionStatus` e verifique o campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Depois que a VM atingir a "Criptografia de disco do sistema operacional iniciada", levará cerca de 40 a 50 minutos em uma VM com backup de armazenamento Premium.

  Devido ao [problema 388](https://github.com/Azure/WALinuxAgent/issues/388) no WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` são mostrados como `Unknown` em algumas distribuições. Com a versão 2.1.5 WALinuxAgent e posterior, esse problema é corrigido automaticamente. Caso você veja `Unknown` na saída, poderá verificar o status da criptografia de disco usando o Explorador de Recursos do Azure.

  Vá para [Explorador de Recursos do Azure](https://resources.azure.com/) e expanda essa hierarquia no painel de seleção à esquerda:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  Em InstanceView, role a tela para baixo para ver o status da criptografia das unidades.

  ![Exibição de instância VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Examine o [diagnóstico de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). As mensagens da extensão ADE devem ser prefixadas com `[AzureDiskEncryption]`.

* Entre na VM via SSH e obtenha o log de extensão de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  É recomendado que você não entre na máquina virtual enquanto a criptografia do sistema operacional está em andamento. Copie os logs apenas quando os outros dois métodos falharem.

## <a name="bkmk_preLinux"></a> Preparar um VHD do Linux previamente criptografado
A preparação para VHDs previamente criptografados pode variar dependendo da distribuição. Exemplos sobre a preparação do [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE) e [CentOS 7](#bkmk_CentOS) estão disponíveis. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Configure a criptografia durante a instalação da distribuição, realizando as seguintes etapas:

1. Selecione **Configurar volumes criptografados** ao particionar os discos.

   ![Instalação do Ubuntu 16.04: configurar volumes criptografados](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de inicialização separada que não deverá ser criptografada. Criptografe a unidade de inicialização.

   ![Instalação do Ubuntu 16.04: selecionar dispositivos para criptografar](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase secreta. Essa é a senha que você enviou para o cofre de chaves.

   ![Instalação do Ubuntu 16.04: fornecer a frase secreta](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua o particionamento.

   ![Instalação do Ubuntu 16.04: concluir o particionamento](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando você inicializar a VM e precisar fornecer uma frase secreta, use a frase secreta que forneceu na etapa 3.

   ![Instalação do Ubuntu 16.04: fornecer a frase secreta na inicialização](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare a VM para upload no Azure usando [estas instruções](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute a última etapa (desprovisionamento da VM) ainda.

Configure a criptografia para trabalhar com o Azure, executando as seguintes etapas:

1. Crie um arquivo em /usr/local/sbin/azure_crypt_key.sh com o conteúdo no script a seguir. Preste atenção ao KeyFileName, pois esse é o nome do arquivo de frase secreta usado pelo Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Altere a configuração de criptografia em */etc/crypttab*. O resultado deve ser assim:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se você estiver editando *azure_crypt_key.sh* no Windows e o tiver copiado para o Linux, execute `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adicione permissões executáveis ao script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edite */etc/initramfs-tools/modules* acrescentando linha:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Execute `update-initramfs -u -k all` para atualizar o initramfs para fazer com que o `keyscript` entre em vigor.

7. Agora, você poderá desprovisionar a VM.

   ![Instalação do Ubuntu 16.04: update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue na próxima etapa e carregue o VHD no Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
Para configurar a criptografia durante a instalação da distribuição, execute as seguintes etapas:
1. Ao particionar os discos, selecione **Criptografar o Grupo de Volumes** e digite uma senha. Essa é a senha que você carregará no cofre de chaves.

   ![Instalação do openSUSE 13.2: criptografar grupo de volume](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Inicialize a VM usando uma senha.

   ![Instalação do openSUSE 13.2: fornecer a frase secreta na inicialização](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare a VM para carregamento no Azure seguindo as instruções em [Preparar uma máquina virtual do SLES ou openSUSE para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute a última etapa (desprovisionamento da VM) ainda.

Para configurar a criptografia para funcionar com o Azure, execute as seguintes etapas:
1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente essas linhas ao final do arquivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Acrescente a seguinte linha no início do arquivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   E altere todas as ocorrências de:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   para:
   ```bash
    if [ 1 ]; then
   ```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente-o depois de “# Open LUKS device”:

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Execute `/usr/sbin/dracut -f -v` para atualizar o initrd.

6. Agora é possível desprovisionar a VM e carregar o VHD no Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Para configurar a criptografia durante a instalação da distribuição, execute as seguintes etapas:
1. Selecione **Criptografar meus dados** ao particionar os discos.

   ![Instalação do CentOS 7: destino da instalação](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Verifique se a opção **Criptografar** está selecionada para a partição raiz.

   ![Instalação do CentOS 7: selecionar criptografia para a partição raiz](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase secreta. Essa é a frase secreta que você enviará ao cofre de chaves.

   ![Instalação do CentOS 7: fornecer a frase secreta](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando você inicializar a VM e precisar fornecer uma frase secreta, use a frase secreta que forneceu na etapa 3.

   ![Instalação do CentOS 7: inserir a frase secreta na inicialização](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Prepare a VM para carregamento no Azure usando as instruções de "CentOS 7.0+" em [Preparar uma máquina virtual baseada em CentOS para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute a última etapa (desprovisionamento da VM) ainda.

6. Agora é possível desprovisionar a VM e carregar o VHD no Azure.

Para configurar a criptografia para funcionar com o Azure, execute as seguintes etapas:

1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente essas linhas ao final do arquivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Acrescente a seguinte linha no início do arquivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   E altere todas as ocorrências de:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   para
   ```bash
    if [ 1 ]; then
   ```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente o seguinte após o "# Open LUKS device":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Execute o “/usr/sbin/dracut -f -v” para atualizar o initrd.

![Instalação do CentOS 7: executar /usr/sbin/dracut -f -v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Carregar VHD criptografado em uma conta de armazenamento do Azure
Depois que a criptografia BitLocker ou criptografia DM-Crypt estiver habilitada, o VHD criptografado local precisará ser carregado para a conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Carregar o segredo da VM previamente criptografada no cofre de chaves
Ao criptografar usando um aplicativo do Azure AD (versão anterior), você precisa carregar o segredo da criptografia de disco já obtido, como um segredo no cofre de chaves. O cofre de chaves deve ter criptografia de disco e permissões habilitadas para o cliente do Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Segredo de criptografia de disco não criptografado com KEK
Para configurar o segredo no cofre de chaves, use [AzKeyVaultSecret conjunto](/powershell/module/az.keyvault/set-azkeyvaultsecret). Se você tem uma máquina virtual do Windows, o arquivo bek é codificado como uma cadeia de caracteres em base64 e carregado no cofre de chaves usando o cmdlet `Set-AzKeyVaultSecret`. Para o Linux, a frase secreta é codificada como uma cadeia de caracteres base64 e carregada para o cofre de chaves. Além disso, verifique se as seguintes marcas estão definidas ao criar o segredo no cofre de chaves.

#### <a name="windows-bek-file"></a>Arquivo BEK do Windows
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Use o `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional sem usar KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Segredo de criptografia de disco criptografado com KEK
Antes de carregar o segredo no cofre de chaves, opcionalmente, você pode criptografá-lo usando uma chave de criptografia de chave. Use a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamento para primeiro criptografar o segredo usando a chave de criptografia de chave. A saída dessa operação wrap é uma cadeia de caracteres de URL codificada em base64, que você pode carregar como um segredo usando o cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Use `$KeyEncryptionKey` e `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional usando KEK](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a> Especificar uma URL secreta ao anexar um disco de SO

###  <a name="bkmk_URLnoKEK"></a>Sem uso de KEK
Enquanto você está anexando o disco do sistema operacional, é necessário passar `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco não criptografado com uma KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Com uso de KEK
Ao anexar o disco do sistema operacional, passe `$KeyEncryptionKey` e `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco criptografado com KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
