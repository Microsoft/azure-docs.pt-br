---
title: Visão geral e implantação de VMs de GPU em seu dispositivo Azure Stack Edge pro
description: Descreve como criar e gerenciar VMs (máquinas virtuais) de GPU em um dispositivo Azure Stack Edge pro usando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438175"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>VMs GPU para seu dispositivo Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo fornece uma visão geral das VMs (máquinas virtuais) de GPU em seu dispositivo Azure Stack Edge pro. O artigo descreve como criar uma VM de GPU e, em seguida, instalar a extensão de driver de GPU para instalar os drivers de NVIDIA apropriados. Use os modelos de Azure Resource Manager para criar a VM de GPU e instalar a extensão de driver de GPU. 

Este artigo se aplica aos dispositivos Azure Stack Edge pro GPU e Azure Stack Edge pro R.

## <a name="about-gpu-vms"></a>Sobre VMs GPU

Seus dispositivos Azure Stack Edge pro são equipados com 1 ou 2 da GPU T4 do Tesla da NVIDIA. Para implantar cargas de trabalho de VM com aceleração de GPU nesses dispositivos, use tamanhos de VM otimizadas para GPU. Por exemplo, o NC T4 v3-Series deve ser usado para implantar cargas de trabalho de inferência com GPUs T4. 

Para obter mais informações, consulte as [VMs do NC T4 v3-Series](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Drivers de sistema operacional e GPU com suporte 

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA GPU devem ser instalados. 

A extensão de Driver Nvidia GPU instala os drivers NVIDIA CUDA ou GRID apropriados. Você pode instalar ou gerenciar a extensão usando os modelos de Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Sistema operacional com suporte para extensão de GPU para Windows

Essa extensão dá suporte aos seguintes sistemas operacionais (OSs). Outras versões podem funcionar, mas não foram testadas internamente em VMs de GPU em execução em dispositivos Azure Stack Edge pro.

| Distribuição | Versão |
|---|---|
| Windows Server 2019 | Núcleo |
| Windows Server 2016 | Núcleo |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Sistema operacional com suporte para a extensão de GPU para Linux

Essa extensão dá suporte aos seguintes distribuições de sistema operacional, dependendo do suporte de driver para a versão específica do sistema operacional. Outras versões podem funcionar, mas não foram testadas internamente em VMs de GPU em execução em dispositivos Azure Stack Edge pro.


| Distribuição | Versão |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>VMs de GPU e kubernetes

Antes de implantar VMs de GPU em seu dispositivo, examine as considerações a seguir se o kubernetes estiver configurado no dispositivo.

#### <a name="for-1-gpu-device"></a>Para um dispositivo de GPU 1: 

- **Crie uma VM GPU seguida pela configuração de kubernetes em seu dispositivo**: nesse cenário, a criação de VM de GPU e a configuração kubernetes serão bem-sucedidas. O kubernetes não terá acesso à GPU nesse caso.

- **Configure o kubernetes em seu dispositivo seguido pela criação de uma VM GPU**: nesse cenário, o kubernetes reivindicará a GPU em seu dispositivo e a criação da VM falhará, pois não há recursos de GPU disponíveis.

#### <a name="for-2-gpu-device"></a>Para o dispositivo de 2 GPU

- **Criar uma VM GPU seguida pela configuração do kubernetes em seu dispositivo**: nesse cenário, a VM GPU que você cria solicitará uma GPU em seu dispositivo e a configuração do kubernetes também será bem-sucedida e reivindicará a outra GPU. 

- **Crie duas VMs GPU seguidas pela configuração de kubernetes em seu dispositivo**: nesse cenário, as duas VMs de GPU reivindicarão as duas GPUs no dispositivo e o kubernetes será configurado com êxito sem GPUs. 

- **Configure o kubernetes em seu dispositivo seguido pela criação de uma VM GPU**: nesse cenário, o kubernetes reivindicará as GPUs em seu dispositivo e a criação da VM falhará, pois não há recursos de GPU disponíveis.

Se você tiver VMs de GPU em execução no seu dispositivo e o kubernetes também estiver configurado, em qualquer momento em que a VM for desalocada (quando você parar ou remover uma VM usando Stop-AzureRmVM ou remove-AzureRmVM), haverá um risco de que o cluster kubernetes solicite todas as GPUs disponíveis no dispositivo. Nessa instância, não será possível reiniciar as VMs GPU implantadas em seu dispositivo ou criar VMs GPU.


## <a name="create-gpu-vms"></a>Criar VMs GPU

Siga estas etapas ao implantar VMs de GPU em seu dispositivo:

1. Identifique se o dispositivo também estará executando o kubernetes. Se o dispositivo for executar o kubernetes, você precisará criar a VM GPU primeiro e, em seguida, configurar o kubernetes. Se kubernetes for configurado primeiro, ele solicitará todos os recursos de GPU disponíveis e a criação da VM de GPU falhará.

1. [Baixe os modelos de VM e os arquivos de parâmetros](https://aka.ms/ase-vm-templates) em seu computador cliente. Descompacte-o em um diretório que você usará como um diretório de trabalho.

1. Para criar VMs GPU, siga todas as etapas em [implantar VM em seu Azure Stack Edge pro usando modelos](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) , exceto pelas seguintes diferenças: 

    1. Ao configurar a rede de computação, habilite a porta que está conectada à Internet, para computação. Isso permite que você baixe os drivers de GPU necessários para as extensões de GPU para suas VMs GPU.

        Aqui está um exemplo em que a porta 2 estava conectada à Internet e foi usada para habilitar a rede de computação. Se você identificou que o kubernetes não é necessário na etapa anterior, você pode ignorar o IP do nó kubernetes e a atribuição de IP do serviço externo.

        ![Habilitar configurações de computação na porta conectada à Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Crie uma VM usando os modelos. Ao especificar tamanhos de VM de GPU, certifique-se de usar a NCasT4-v3-Series no `CreateVM.parameters.json` , pois elas têm suporte para VMs de GPU. Para obter mais informações, consulte [tamanhos de VM com suporte para VMs GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Depois que a VM GPU for criada com êxito, você poderá exibir essa VM na lista de máquinas virtuais em seu Azure Stack recurso de borda no portal do Azure.

        ![VM GPU na lista de máquinas virtuais no portal do Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Selecione a VM e faça uma busca detalhada nos detalhes. Copie o IP alocado para a VM.

    ![IP alocado para VM GPU no portal do Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Depois que a VM for criada, implante a extensão de GPU usando o modelo de extensão. Para VMs do Linux, consulte [instalar extensão de GPU para Linux](#gpu-extension-for-linux) e para VMs do Windows, consulte [instalar extensão de GPU para Windows](#gpu-extension-for-windows).

1. Para verificar a instalação da extensão da GPU, conecte-se à VM da GPU:
    1. Se estiver usando uma VM do Windows, siga as etapas em [conectar-se a uma VM do Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm). [Verifique a instalação](#verify-windows-driver-installation).
    1. Se estiver usando uma VM do Linux, siga as etapas em [conectar-se a uma VM do Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). [Verifique a instalação](#verify-linux-driver-installation).

1. Se necessário, você pode mudar a rede de computação de volta para tudo o que precisar. 


> [!NOTE]
> Ao atualizar a versão do software do dispositivo do 2012 para o mais recente, você precisará interromper manualmente as VMs da GPU.


## <a name="install-gpu-extension"></a>Instalar extensão de GPU

Dependendo do sistema operacional da sua VM, você pode instalar a extensão de GPU para Windows ou para Linux.

> [!NOTE]
> Antes de instalar a extensão GPU, certifique-se de que a porta habilitada para a rede de computação em seu dispositivo esteja conectada à Internet e tenha acesso. Os drivers de GPU são baixados por meio do acesso à Internet.

### <a name="gpu-extension-for-windows"></a>Extensão de GPU para Windows

Para implantar Drivers NVIDIA GPU para uma VM existente, edite o `addGPUExtWindowsVM.parameters.json` arquivo de parâmetros e, em seguida, implante o modelo `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O arquivo `addGPUExtWindowsVM.parameters.json` usa os seguintes parâmetros:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Aqui está um arquivo de parâmetro de exemplo que foi usado neste artigo:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Implantar modelo

Implante o modelo `addGPUextensiontoVM.json` . Este modelo implanta a extensão em uma VM existente. Execute o comando a seguir:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> A implantação de extensão é um trabalho de longa execução e leva cerca de 10 minutos para ser concluída.

Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

A saída de execução de extensão é registrada no arquivo a seguir. Consulte esse arquivo `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` para acompanhar o status da instalação. 


Uma instalação bem-sucedida é indicada por um `message` as `Enable Extension` e `status` como `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Verificar a instalação do driver do Windows

Entre na VM e execute o utilitário de linha de comando NVIDIA-SMI instalado com o driver. O `nvidia-smi.exe` está localizado em  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Se você não vir o arquivo, é possível que a instalação do driver ainda esteja em execução em segundo plano. Aguarde 10 minutos e verifique novamente.

Se o driver estiver instalado, você verá uma saída semelhante à seguinte amostra: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Para obter mais informações, consulte [extensão de Driver Nvidia GPU para Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>Extensão de GPU para Linux

Para implantar Drivers NVIDIA GPU para uma VM existente, edite o arquivo de parâmetros e, em seguida, implante o modelo `addGPUextensiontoVM.json` . Há arquivos de parâmetros específicos para Ubuntu e Red Hat Enterprise Linux (RHEL), conforme discutido nas seções a seguir.

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

Se estiver usando o Ubuntu, o `addGPUExtLinuxVM.parameters.json` arquivo usará os seguintes parâmetros:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Se estiver usando Red Hat Enterprise Linux (RHEL), o `addGPUExtensionRHELVM.parameters.json` arquivo usará os seguintes parâmetros:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Veja um exemplo de arquivo de parâmetro do Ubuntu que foi usado neste artigo:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Implantar modelo

Implante o modelo `addGPUextensiontoVM.json` . Este modelo implanta a extensão em uma VM existente. Execute o comando a seguir:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> A implantação de extensão é um trabalho de longa execução e leva cerca de 10 minutos para ser concluída.

Aqui está uma amostra de saída:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Acompanhar o status da implantação    
    
Implantação de modelo é um trabalho de longa execução. Para verificar o estado de implantação das extensões de uma determinada VM, abra outra sessão do PowerShell (executar como administrador). Execute o comando a seguir: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Aqui está uma amostra de saída: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

A saída de execução da extensão é registrada no seguinte arquivo: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Verificar a instalação do driver do Linux

Siga estas etapas para verificar a instalação do driver:

1. Conecte-se à VM da GPU. Siga as instruções em [conectar-se a uma VM do Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Aqui está uma amostra de saída:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Execute o utilitário de linha de comando NVIDIA-SMI instalado com o driver. Se o driver for instalado com êxito, você poderá executar o utilitário e ver a seguinte saída:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Para obter mais informações, consulte [extensão de Driver Nvidia GPU para Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Remover extensão de GPU

Para remover a extensão de GPU, use o seguinte comando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Aqui está uma amostra de saída:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Próximas etapas

[Azure Resource Manager cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)