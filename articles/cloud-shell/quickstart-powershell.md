---
title: Guia de início rápido do Azure Cloud Shell-PowerShell
description: Saiba como usar o PowerShell em seu navegador com Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045273"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Início rápido do PowerShell no Azure Cloud Shell

Este documento fornece detalhes sobre como usar o PowerShell no Cloud Shell no [Portal do Azure](https://portal.azure.com/).

> [!NOTE]
> Um início rápido do [Bash no Azure Cloud Shell](quickstart.md) também está disponível.

## <a name="start-cloud-shell"></a>Iniciar o Cloud Shell

1. Clique no botão **Cloud Shell** no painel de navegação superior do portal do Azure

   ![Captura de tela mostrando como iniciar Azure Cloud Shell da portal do Azure.](media/quickstart-powershell/shell-icon.png)

2. Selecione o ambiente do PowerShell na lista suspensa e você irá para unidade do Azure`(Azure:)`

   ![Captura de tela mostrando como selecionar o ambiente do PowerShell para o Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Executar comandos do PowerShell

Execute comandos regulares do PowerShell no Cloud Shell, tais como:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Interagir com máquinas virtuais

Você pode encontrar todas as suas máquinas virtuais da assinatura atual por meio do diretório `VirtualMachines`.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Invocar o script do PowerShell entre VMs remotas

 > [!WARNING]
 > Consulte [Solucionando problemas de gerenciamento remoto de máquinas virtuais do Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Supondo que você tenha uma VM, MyVM1, vamos usar `Invoke-AzVMCommand` para invocar um bloco de script do PowerShell no computador remoto.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Você também pode primeiro navegar até o diretório VirtualMachines e executar `Invoke-AzVMCommand` como mostrado a seguir.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Fazer logon interativamente em uma VM remota

Você pode usar `Enter-AzVM` para fazer logon interativamente em uma VM em execução no Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Você também pode primeiro navegar até o diretório `VirtualMachines` e executar `Enter-AzVM` como mostrado a seguir

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Descobrir WebApps

Inserindo no `WebApps` diretório, você pode navegar facilmente seus recursos de aplicativos web

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Para autenticar servidores ou VMs usando o SSH, gere o par de chaves privadas/públicas no Cloud Shell e publique a chave pública para `authorized_keys` no computador remoto, como `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> É possível criar chaves privadas/públicas SSH usando `ssh-keygen` e publicá-las para `$env:USERPROFILE\.ssh` no Cloud Shell.

### <a name="using-ssh"></a>Usando o SSH

Siga as instruções [aqui](../virtual-machines/linux/quick-create-powershell.md) para criar uma nova configuração de VM usando cmdlets Azure PowerShell.
Antes de chamar em `New-AzVM` para iniciar a implantação, adicione chave pública SSH à configuração da VM.
A VM recém-criada conterá a chave pública no local `~\.ssh\authorized_keys`, permitindo assim a sessão SSH sem credenciais para a VM.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Listar comandos disponíveis

Na unidade `Azure`, digite `Get-AzCommand` para obter comandos do Azure específicos ao contexto.

Como alternativa, você pode sempre usar `Get-Command *az* -Module Az.*` para descobrir os comandos disponíveis do Azure.

## <a name="install-custom-modules"></a>Instalar módulos personalizados

Você pode executar `Install-Module` para instalar módulos da [Galeria do PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Digite `Get-Help` para obter informações sobre o PowerShell no Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Para um comando específico, você também pode executar `Get-Help` seguido de um cmdlet, por exemplo.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Usar os Arquivos do Azure para armazenar seus dados

Você pode criar um script, digamos `helloworld.ps1`, e salvá-lo em seu `clouddrive` para usá-lo em sessões de shell.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Na próxima vez que utilizar o PowerShell no Cloud Shell, o arquivo `helloworld.ps1` estará no diretório `$HOME\clouddrive` que monta o compartilhamento de arquivos do Azure.

## <a name="use-custom-profile"></a>Usar perfil personalizado

Você pode personalizar seu ambiente do PowerShell criando perfis do PowerShell - `profile.ps1` (ou `Microsoft.PowerShell_profile.ps1`).
Salve-o em `$profile.CurrentUserAllHosts` (ou `$profile.CurrentUserAllHosts`), de modo que ele pode ser carregado em cada PowerShell na sessão do Cloud Shell.

Para saber como criar um perfil, consulte [Sobre perfis][profile].

## <a name="use-git"></a>Usar o Git

Para clonar um repositório git no Cloud Shell, você precisa criar um [token de acesso pessoal][githubtoken] e usá-lo como o nome de usuário. Quando tiver seu token, faça um clone do repositório da seguinte maneira:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Sair do shell

Digite `exit` para encerrar a sessão.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
