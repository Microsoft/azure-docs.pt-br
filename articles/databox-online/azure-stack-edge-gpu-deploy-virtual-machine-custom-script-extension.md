---
title: Uso de extensões de script personalizado para VMs em seu dispositivo Azure Stack Edge pro
description: Descreve como instalar extensões de script personalizado em máquinas virtuais (VMs) em execução em um dispositivo Azure Stack Edge pro usando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 8b233211f47250d4742d35cd0782cdd241839496
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804871"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Implantar a extensão de script personalizado em VMs em execução no seu dispositivo Azure Stack Edge pro

A extensão de script personalizado baixa e executa scripts ou comandos em máquinas virtuais em execução em seus dispositivos Azure Stack Edge pro. Este artigo fornece detalhes sobre como instalar e executar a extensão de script personalizado usando um modelo de Azure Resource Manager. 

Este artigo aplica-se a Azure Stack Edge pro GPU, Azure Stack borda pro R e Azure Stack dispositivos mini R do Edge.

## <a name="about-custom-script-extension"></a>Sobre a extensão de script personalizado

A extensão de script personalizado é útil para configuração pós-implantação, instalação de software ou qualquer outra tarefa de configuração/gerenciamento. Você pode baixar scripts do armazenamento do Azure ou outro local de Internet acessível, ou pode fornecer scripts ou comandos para o tempo de execução de extensão.

A extensão de Script personalizado se integra com os modelos do Azure Resource Manager. Você também pode executá-la usando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de máquinas virtuais do Azure.

## <a name="os-for-custom-script-extension"></a>SO para extensão de script personalizado

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Sistema operacional com suporte para extensão de script personalizado no Windows

A extensão de script personalizado para Windows será executada no seguinte OSs. Outras versões podem funcionar, mas não foram testadas internamente em VMs em execução em dispositivos Azure Stack Edge pro.

| Distribuição | Versão |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Sistema operacional com suporte para extensão de script personalizado no Linux

A extensão de script personalizado para Linux será executada no seguinte OSs. Outras versões podem funcionar, mas não foram testadas internamente em VMs em execução em dispositivos Azure Stack Edge pro.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Pré-requisitos

1. [Baixe os modelos de VM e os arquivos de parâmetros](https://aka.ms/ase-vm-templates) em seu computador cliente. Descompacte o download em um diretório que você usará como um diretório de trabalho.

1. Você deve ter uma VM criada e implantada em seu dispositivo. Para criar VMs, siga todas as etapas em [implantar VM em seu Azure Stack Edge pro usando modelos](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Se você precisar baixar um script, como no GitHub ou no armazenamento do Azure externamente, ao configurar a rede de computação, habilite a porta que está conectada à Internet para computação. Isso permite que você baixe o script.

    No exemplo a seguir, a porta 2 estava conectada à Internet e foi usada para habilitar a rede de computação. Se você identificou que o kubernetes não é necessário na etapa anterior, você pode ignorar o IP do nó kubernetes e a atribuição de IP do serviço externo.

    ![Habilitar configurações de computação na porta conectada à Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Instalar extensão de script personalizado

Dependendo do sistema operacional da sua VM, você pode instalar a extensão de script personalizado para Windows ou para Linux.
 

### <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

Para implantar a extensão de script personalizado para Windows para uma VM em execução em seu dispositivo, edite o `addCSExtWindowsVM.parameters.json` arquivo de parâmetros e, em seguida, implante o modelo `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O arquivo `addCSExtWindowsVM.parameters.json` usa os seguintes parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Forneça o nome da VM, o nome da extensão e o comando que você deseja executar.

Aqui está o arquivo de parâmetro de exemplo que foi usado neste artigo.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Implantar modelo

Implante o modelo `addCSextensiontoVM.json` . Este modelo implanta a extensão em uma VM existente. Execute o comando a seguir:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> A implantação de extensão é um trabalho de longa execução e leva cerca de 10 minutos para ser concluída.

Aqui está um exemplo de saída:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Acompanhar a implantação

Para verificar o estado de implantação das extensões de uma determinada VM, execute o seguinte comando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Aqui está um exemplo de saída:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Quando a implantação for concluída, as `ProvisioningState` alterações para `Succeeded` .

A saída da extensão é registrada nos arquivos localizados na pasta a seguir, na máquina virtual de destino. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados na pasta a seguir, na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
em que <n> é um inteiro decimal que pode ser alterado entre as execuções da extensão. O valor 1. * corresponde ao valor atual real `typeHandlerVersion` da extensão. Por exemplo, o diretório real nesta instância era `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


Nesta instância, o comando a ser executado para a extensão personalizada foi: `md C:\\Users\\Public\\Documents\\test` . Quando a extensão é instalada com êxito, você pode verificar se o diretório foi criado na VM no caminho especificado no comando. 


### <a name="custom-script-extension-for-linux"></a>Extensão de script personalizado para o Linux

Para implantar a extensão de script personalizado para Windows para uma VM em execução em seu dispositivo, edite o `addCSExtLinuxVM.parameters.json` arquivo de parâmetros e, em seguida, implante o modelo `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O arquivo `addCSExtLinuxVM.parameters.json` usa os seguintes parâmetros:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Forneça o nome da VM, o nome da extensão e o comando que você deseja executar.

Aqui está um arquivo de parâmetro de exemplo que foi usado neste artigo:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> A implantação de extensão é um trabalho de longa execução e leva cerca de 10 minutos para ser concluída.

Aqui está um exemplo de saída:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

O `commandToExecute` foi definido para criar um arquivo `file2.txt` no `/home/Administrator` diretório e o conteúdo do arquivo é `some text` . Nesse caso, você pode verificar se o arquivo foi criado no caminho especificado.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Acompanhar o status da implantação    
    
Implantação de modelo é um trabalho de longa execução. Para verificar o estado de implantação das extensões de uma determinada VM, abra outra sessão do PowerShell (executar como administrador). Execute o comando a seguir: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Aqui está um exemplo de saída: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Quando a implantação for concluída, as `ProvisioningState` alterações para `Succeeded` .

A saída de execução da extensão é registrada no seguinte arquivo: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Remover extensão de script personalizado

Para remover a extensão de script personalizado, use o seguinte comando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Aqui está um exemplo de saída:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Próximas etapas

[Azure Resource Manager cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
