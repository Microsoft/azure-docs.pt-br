---
title: Use ferramentas remotas para solucionar problemas do VM do Azure | Microsoft Docs
description: Saiba mais sobre o PsExec, scripts do PowerShell e outras ferramentas remotas que você pode usar para solucionar problemas de VM remota do Azure sem usar o RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: ac785d43a71039ce52f0c8cd4315149a11e91cfc
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737346"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Use ferramentas remotas para solucionar problemas do VM do Azure

Ao solucionar problemas em uma VM (máquina virtual) do Azure, você pode se conectar à VM usando as ferramentas remotas que são discutidas neste artigo em vez de usar o protocolo RDP (RDP).

## <a name="serial-console"></a>Console serial

Use um [console serial para máquinas virtuais do Azure](serial-console-windows.md) para executar comandos na VM remota do Azure.

## <a name="remote-cmd"></a>CMD remoto

Baixe o [PsExec](/sysinternals/downloads/psexec). Conecte-se à VM executando o seguinte comando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* O comando deve ser executado em um computador que esteja na mesma rede virtual.
>* Nome de host ou DIP pode ser usado para substituir \<computer>.
>* O parâmetro -s garante que o comando seja chamado usando a conta do sistema (permissão de administrador).
>* PsExec usa portas TCP 135 e 445. Como resultado, as duas portas precisam estar abertas no firewall.

## <a name="run-command"></a>Executar comando

Para obter mais informações sobre como usar o recurso executar comando para executar scripts na VM, consulte [executar scripts do PowerShell em sua VM do Windows com o comando executar](../windows/run-command.md).

## <a name="custom-script-extension"></a>Extensão de script personalizado

Você pode usar o recurso Custom Script Extension para executar um script personalizado na VM de destino. Para usar esse recurso, as seguintes condições devem ser atendidas:

* A VM tem conectividade.
* O agente de máquina virtual do Azure está instalado e funcionando conforme o esperado na VM.
* A extensão não foi instalada anteriormente na VM.
 
  A extensão injeta o script somente na primeira vez em que ele é usado. Se você usar esse recurso mais tarde, a extensão reconhecerá que ele já foi usado e não carregará o novo script.

Carregue seu script em uma conta de armazenamento e gere seu próprio contêiner. Em seguida, execute o seguinte script do PowerShell do Azure em um computador que tenha conectividade com a VM.

### <a name="for-classic-deployment-model-vms"></a>Para VMs do modelo de implantação clássica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Para VMs Azure Resource Manager

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!NOTE]
>A porta TCP 5986 (HTTPS) deve estar aberta para que você possa usar essa opção.
>
>Para VMs Azure Resource Manager, você deve abrir a porta 5986 no grupo de segurança de rede (NSG). Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada (5986) e uma porta pública. Em seguida, você também precisa abrir essa porta voltada para o público no NSG.

### <a name="set-up-the-client-computer"></a>Configurar o computador cliente

Para usar o PowerShell para conectar-se à VM remotamente, primeiro você precisa configurar o computador cliente para permitir a conexão. Para isso, adicione a VM à lista de hosts confiáveis do PowerShell executando o seguinte comando, conforme apropriado.

Para adicionar uma VM à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Para adicionar várias VMs à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Para adicionar todos os computadores à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Habilitar RemotePS na VM

Para VMs criadas usando o modelo de implantação clássico, use a extensão de script personalizado para executar o script a seguir:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Para VMs Azure Resource Manager, use comandos de execução do portal para executar o script EnableRemotePS:

![Executar comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Execute o seguinte comando com base no local do computador cliente:

* Fora da rede virtual ou da implantação

  * Para uma VM criada usando o modelo de implantação clássico, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Para uma VM Azure Resource Manager, primeiro adicione um nome DNS ao endereço IP público. Para obter etapas detalhadas, consulte [Criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Windows](../create-fqdn.md). Em seguida, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Dentro da rede virtual ou implantação, execute o seguinte comando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Definir o sinalizador SkipCaCheck ignora o requisito para importar um certificado para a VM quando você inicia a sessão.

Você também pode usar o cmdlet Invoke-Command para executar um script na VM remotamente.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro Remoto

>[!NOTE]
>A porta TCP 135 ou 445 deve estar aberta para usar esta opção.
>
>Para VMs Azure Resource Manager, você precisa abrir a porta 5986 no NSG. Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada 5986 e uma porta pública. Você também precisa abrir essa porta voltada para o público no NSG.

1. Em outra VM na mesma rede virtual, abra o editor do registro (regedit.exe).

2. Selecione **arquivo**  >  **conectar registro de rede**.

   ![Editor do registro](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Localize a VM de destino por **nome de host** ou **IP dinâmico** (preferível) inserindo-a na caixa **Inserir o nome do objeto a ser selecionado** .

   ![Insira o nome do objeto para selecionar a caixa](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Digite as credenciais para a VM de destino.

5. Faça as alterações necessárias no registro.

## <a name="remote-services-console"></a>Console de serviços remotos

>[!NOTE]
>As portas TCP 135 ou 445 devem estar abertas para usar essa opção.
>
>Para VMs Azure Resource Manager, você precisa abrir a porta 5986 no NSG. Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada 5986 e uma porta pública. Você também precisa abrir essa porta voltada para o público no NSG.

1. De outra VM na mesma rede virtual, abra uma instância de **Services. msc**.

2. Clique com botão direito **serviços (Local)**.

3. Selecione **Conectar-se a outro computador**.

   ![Serviço remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Insira o IP dinâmico da VM de destino.

   ![IP dinâmico de entrada](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Faça as alterações necessárias para os serviços.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o cmdlet Enter-PSSession, consulte [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession).
- Para obter mais informações sobre a extensão de script personalizado para Windows usando o modelo de implantação clássico, consulte [extensão de script personalizado para Windows](../extensions/custom-script-windows.md).
- PsExec é parte do [conjunto PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Para obter mais informações sobre o conjunto de PSTools, consulte [PsTools](/sysinternals/downloads/pstools).
