---
title: Conectar-se ao console de máquina virtual no dispositivo de GPU pro Azure Stack Edge
description: Descreve como se conectar ao console de máquina virtual em uma VM em execução no dispositivo Azure Stack Edge pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962474"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Conectar-se a um console de máquina virtual em um dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A solução Azure Stack Edge pro GPU executa cargas de trabalho não-contêineras por meio das máquinas virtuais. Este artigo descreve como se conectar ao console de uma máquina virtual implantada em seu dispositivo. 

O console de máquina virtual permite que você acesse suas VMs com teclado, mouse e recursos de tela usando as ferramentas de área de trabalho remota disponíveis com frequência. Você pode acessar o console e solucionar quaisquer problemas que tenham ocorrido durante a implantação de uma máquina virtual em seu dispositivo. Você pode se conectar ao console da máquina virtual, mesmo que a VM não tenha sido provisionada.


## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho de alto nível tem as seguintes etapas:

1. Conecte-se à interface do PowerShell em seu dispositivo.
1. Habilite o acesso do console à VM.
1. Conecte-se à VM usando o protocolo RDP (área de trabalho remota).
1. Revogar o acesso ao console para a VM.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu os seguintes pré-requisitos:

#### <a name="for-your-device"></a>Para seu dispositivo

Você deve ter acesso a um dispositivo de GPU pro Azure Stack Edge que está ativado. O dispositivo deve ter uma ou mais VMs implantadas nele. Você pode implantar VMs via Azure PowerShell, por meio de modelos ou por meio do portal do Azure.

#### <a name="for-client-accessing-the-device"></a>Para cliente que acessa o dispositivo

Verifique se você tem acesso a um sistema cliente que:

- Pode acessar a interface do PowerShell do dispositivo. O cliente está executando um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- O cliente está executando o PowerShell 7,0 ou posterior. Esta versão do PowerShell funciona para clientes Windows, Mac e Linux. Consulte as instruções para [instalar o PowerShell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Tem recursos de área de trabalho remota. Dependendo se você estiver usando o Windows, o macOS ou o Linux, instale um desses [clientes de área de trabalho remota](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Este artigo fornece instruções sobre o [Windows área de trabalho remota](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) e o [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Conectar ao console de VM

Siga estas etapas para se conectar ao console de máquina virtual em seu dispositivo.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Conectar-se à interface do PowerShell em seu dispositivo

A primeira etapa é [conectar-se à interface do PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo. 

### <a name="enable-console-access-to-the-vm"></a>Habilitar o acesso do console à VM

1.  Na interface do PowerShell, execute o comando a seguir para habilitar o acesso ao console da VM.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Na saída de exemplo, anote a ID da máquina virtual. Você precisará disso para uma etapa posterior.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Agora você pode usar um cliente Área de Trabalho Remota para se conectar ao console de máquina virtual.

#### <a name="use-windows-remote-desktop"></a>Usar o Windows Área de Trabalho Remota

1. Crie um novo arquivo de texto e insira o texto a seguir.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Salve o arquivo como **. rdp* no sistema cliente. Você usará esse perfil para se conectar à VM.
1. Clique duas vezes no perfil para se conectar à VM. Forneça as seguintes credenciais:

    - **Nome de usuário**: entre como EdgeARMUser.
    - **Senha**: forneça a senha de Azure Resource Manager local para seu dispositivo. Se você esqueceu a senha, [redefina Azure Resource Manager senha por meio do portal do Azure](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Usar FreeRDP

Se estiver usando FreeRDP em seu cliente Linux, execute o seguinte comando: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Revogar o acesso ao console da VM

Para revogar o acesso ao console da VM, retorne à interface do PowerShell do seu dispositivo. Execute o comando a seguir:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Veja um exemplo de saída:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> É recomendável que, depois de terminar de usar o console da VM, você revogue o acesso ou feche a janela do PowerShell para sair da sessão. 

## <a name="next-steps"></a>Próximas etapas

- Solucionar problemas [Azure Stack o Edge pro](azure-stack-edge-gpu-troubleshoot.md) em portal do Azure.