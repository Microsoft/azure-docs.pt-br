---
title: Instalar o Agente de VM do Azure no modo offline | Microsoft Docs
description: Saiba como instalar o Agente de VM do Azure no modo offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002605"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalar o Agente de Máquina Virtual do Azure no modo offline 

O Agente de Máquina Virtual do Azure (Agente de VM) fornece recursos úteis, tais como a redefinição de senha de administrador local e o envio de scripts por push. Este artigo mostra como instalar o Agente de VM para uma VM (máquina virtual) offline do Windows. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando usar o Agente de VM no modo offline

Instale o Agente de VM no modo offline nos seguintes cenários:

- A VM do Azure implantada não tem o Agente de VM instalado ou o agente não está funcionando.
- Você esqueceu a senha de administrador da VM ou você não pode acessar a VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Instalar o Agente de VM no modo offline

Use as etapas a seguir para instalar o Agente de VM no modo offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Etapa 1: anexar o disco do sistema operacional da VM a uma outra VM como um disco de dados

1. Obtenha um instantâneo do disco do sistema operacional da VM afetada, crie um disco com base no instantâneo e, em seguida, anexe o disco a uma VM de solução de problemas. Para obter mais informações, confira [Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](troubleshoot-recovery-disks-portal-windows.md). Para a VM clássica, exclua a VM e mantenha o disco do sistema operacional e anexe o disco do sistema operacional à VM de solução de problemas.

2.  Conecte-se à VM de solução de problemas. Abra gerenciamento de **computador** gerenciamento de  >  **disco**. Confirme que o disco do sistema operacional está online e que as letras de unidade estão atribuídas às partições de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Etapa 2: modificar o disco do sistema operacional para instalar o Agente de VM do Azure

1.  Inicie uma conexão de área de trabalho remota para a VM de solução de problemas.

2.  Na VM do solucionador de problemas, navegue até o disco do sistema operacional que você anexou, abra a pasta \Windows\System32\config. Copie todos os arquivos nesta pasta como um backup, para o caso de necessidade de uma reversão.

3.  Inicie o **Editor do Registro** (regedit.exe).

4.  Selecione a chave **HKEY_LOCAL_MACHINE**. No menu, selecione **arquivo**  >  **Carregar Hive**:

    ![Carregar o hive](./media/install-vm-agent-offline/load-hive.png)

5.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SYSTEM. Como o nome da seção, digite **BROKENSYSTEM**. O novo hive do Registro é exibido sob a chave **HKEY_LOCAL_MACHINE**.

6.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SOFTWARE. Para o nome do software de hive, digite **BROKENSOFTWARE**.

7. Se o disco do sistema operacional anexado tem o agente de VM instalado, execute um backup da configuração atual. Se ele não tem o agente de VM instalado, passe para a próxima etapa.
      
    1. Renomeie a pasta de \windowsazure para \windowsazure.old.

    2. Exporte os registros a seguir:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Use os arquivos existentes na solução de problemas de VM como um repositório para a instalação do Agente de VM. Conclua as seguintes etapas:

    1. Da VM de solução de problemas, exporte as seguintes subchaves no formato de Registro (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportar as subchaves do Registro](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os arquivos de Registro. Em cada arquivo, altere o valor de entrada **SYSTEM** para **BROKENSYSTEM** (conforme mostrado nas imagens a seguir) e salve o arquivo. Lembre-se do **ImagePath** do agente de VM atual. Precisaremos copiar a pasta correspondente para o disco do sistema operacional anexado. 

        ![Alterar os valores de subchaves do Registro](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os arquivos de Registro para o repositório clicando duas vezes em cada arquivo de Registro.

    4. Confirme se as duas subchaves a seguir foram importadas com êxito para o hive **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - RdAgent

    5. Copie a pasta de instalação do agente de VM atual para o disco do sistema operacional anexado: 

        1.  No disco do sistema operacional que você anexou, crie uma pasta chamada WindowsAzure no caminho raiz.

        2.  Acesse C:\WindowsAzure na solução de problemas da VM, procure uma pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta GuestAgent com o número de versão mais recente do C:\WindowsAzure para a pasta WindowsAzure no disco do sistema operacional anexado. Se você não tiver certeza de qual pasta deve ser copiada, copie todas as pastas GuestAgent. A imagem a seguir mostra um exemplo da pasta GuestAgent copiada para o disco do sistema operacional anexado.

             ![Copiar a pasta GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BROKENSYSTEM**. No menu, selecione **arquivo**  >  **Descarregar Hive**.

10.  Selecione **BROKENSOFTWARE**. No menu, selecione **arquivo**  >  **Descarregar Hive**.

11.  Desanexe o disco do sistema operacional e, em seguida, [altere o disco do sistema operacional para a VM afetada](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Para a VM clássica, crie uma nova VM usando o disco do sistema operacional reparado.

12.  Acesse a VM. Observe que o RdAgent está em execução e os logs estão sendo gerados.

Se você criou a VM usando o modelo de implantação do Resource Manager, já está tudo pronto.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Usar a propriedade ProvisionGuestAgent para VMs clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Se você tiver criado a VM usando o modelo clássico, use o módulo do Azure PowerShell para atualizar a propriedade **ProvisionGuestAgent**. A propriedade informa o Azure que a VM tem o Agente de VM instalado.

Para definir a propriedade **ProvisionGuestAgent**, execute os seguintes comandos do Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Em seguida, execute o `Get-AzureVM` comando. Observe que a propriedade **GuestAgentStatus** agora é populada por dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Agente de Máquina Virtual do Azure](../extensions/agent-windows.md)
- [Recursos e extensões da máquina virtual para Windows](../extensions/features-windows.md)
