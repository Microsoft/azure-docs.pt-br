---
title: Instalar o Agente de VM do Azure no modo offline | Microsoft Docs
description: Saiba como instalar o Agente de VM do Azure no modo offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c282a739ad55d6f2c5fcbe3b3a1a69541a7350cd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705778"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalar o Agente de Máquina Virtual do Azure no modo offline 

O Agente de Máquina Virtual do Azure (Agente de VM) fornece recursos úteis, tais como a redefinição de senha de administrador local e o envio de scripts por push. Este artigo mostra como instalar o Agente de VM para uma VM (máquina virtual) offline do Windows. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando usar o Agente de VM no modo offline

Instale o Agente de VM no modo offline nos seguintes cenários:

- A VM do Azure implantada não tem o Agente de VM instalado ou o agente não está funcionando.
- Você esqueceu a senha de administrador da VM ou você não pode acessar a VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Instalar o Agente de VM no modo offline

Use as etapas a seguir para instalar o Agente de VM no modo offline.

> [!NOTE]
> Você pode automatizar o processo de instalação do agente de VM no modo offline.
> Para fazer isso, use os [Scripts de Recuperação de VM do Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Se você decidiu usar os scripts de recuperação de VM do Azure, pode usar o processo a seguir:
> 1. Ignore a etapa 1 usando os scripts para anexar o disco do SO da VM afetada para uma VM de recuperação.
> 2. Siga as etapas 2 a 10 para aplicar as mitigações.
> 3. Ignore a etapa 11 usando os scripts para recompilar a VM.
> 4. Siga a etapa 12.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Etapa 1: Anexar o disco do sistema operacional da VM a outra VM como um disco de dados

1.  Exclua a VM. Ao excluir a VM, verifique se você selecionou a opção **Manter os discos**.

2.  Anexe o disco do sistema operacional como um disco de dados a outra VM (conhecida como uma VM de _solução de problemas_). Para obter mais informações, veja [Anexar um disco de dados a uma VM do Windows no Portal do Azure](../windows/attach-managed-disk-portal.md).

3.  Conecte-se à VM de solução de problemas. Abra **Gerenciamento do computador** > **Gerenciamento de disco**. Confirme que o disco do sistema operacional está online e que as letras de unidade estão atribuídas às partições de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Etapa 2: Modificar o disco do sistema operacional para instalar o Agente de VM do Azure

1.  Inicie uma conexão de área de trabalho remota para a VM de solução de problemas.

2.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config. Copie todos os arquivos nesta pasta como um backup, para o caso de necessidade de uma reversão.

3.  Inicie o **Editor do Registro** (regedit.exe).

4.  Selecione a chave **HKEY_LOCAL_MACHINE**. No menu, selecione **Arquivo** > **Carregar Hive**:

    ![Carregar o hive](./media/install-vm-agent-offline/load-hive.png)

5.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SYSTEM. Como o nome da seção, digite **BROKENSYSTEM**. O novo hive do Registro é exibido sob a chave **HKEY_LOCAL_MACHINE**.

6.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SOFTWARE. Para o nome do software de hive, digite **BROKENSOFTWARE**.

7. Se o disco do sistema operacional anexado tem o agente de VM instalado, execute um backup da configuração atual. Se ele não tem o agente de VM instalado, passe para a próxima etapa.
      
    1. Renomeie a pasta de \windowsazure para \windowsazure.old.

    2. Exporte os registros a seguir:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Use os arquivos existentes na solução de problemas de VM como um repositório para a instalação do Agente de VM. Conclua as seguintes etapas:

    1. Da VM de solução de problemas, exporte as seguintes subchaves no formato de Registro (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportar as subchaves do Registro](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os arquivos de Registro. Em cada arquivo, altere o valor de entrada **SYSTEM** para **BROKENSYSTEM** (conforme mostrado nas imagens a seguir) e salve o arquivo. Lembre-se do **ImagePath** do agente de VM atual. Precisaremos copiar a pasta correspondente para o disco do sistema operacional anexado. 

        ![Alterar os valores de subchaves do Registro](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os arquivos de Registro para o repositório clicando duas vezes em cada arquivo de Registro.

    4. Confirme se as três subchaves a seguir são importadas com êxito para o hive **BROKENSYSTEM**:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Copie a pasta de instalação do agente de VM atual para o disco do sistema operacional anexado: 

        1.  No disco do sistema operacional que você anexou, crie uma pasta chamada WindowsAzure no caminho raiz.

        2.  Acesse C:\WindowsAzure na solução de problemas da VM, procure uma pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta GuestAgent com o número de versão mais recente do C:\WindowsAzure para a pasta WindowsAzure no disco do sistema operacional anexado. Se você não tiver certeza de qual pasta deve ser copiada, copie todas as pastas GuestAgent. A imagem a seguir mostra um exemplo da pasta GuestAgent copiada para o disco do sistema operacional anexado.

             ![Copiar a pasta GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BROKENSYSTEM**. No menu, selecione **Arquivo** > **Descarregar Hive**.

10.  Selecione **BROKENSOFTWARE**. No menu, selecione **Arquivo** > **Descarregar Hive**.

11.  Desanexe o disco do sistema operacional e, em seguida, recrie a VM usando o disco do sistema operacional.

12.  Acesse a VM. Observe que o RdAgent está em execução e os logs estão sendo gerados.

Se você criou a VM usando o modelo de implantação do Resource Manager, já está tudo pronto.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Usar a propriedade ProvisionGuestAgent para VMs clássicas

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
