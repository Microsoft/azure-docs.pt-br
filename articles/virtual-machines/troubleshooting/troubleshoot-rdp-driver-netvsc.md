---
title: Solucionar um problema de netvsc quando você se conectar remotamente a uma máquina virtual Windows 10 ou Windows Server 2016 no Azure | Microsoft Docs
description: Saiba como solucionar um problema de RDP netsvc.sys-related ao se conectar a uma máquina virtual Windows 10 ou Windows Server 2016 no Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 0f5a414f00ffa50114f090fc19f37b8a85428547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508994"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Não é possível se conectar remotamente a uma máquina virtual Windows Server 2016 no Azure por causa de netvsc.sys.

Este artigo explica como solucionar um problema em que não há nenhuma conexão de rede ao se conectar a uma máquina virtual (VM) de banco de dados para um Windows 10 ou Windows Server em um Host Hyper-V Server 2016.

## <a name="symptoms"></a>Sintomas

Você não pode se conectar a uma VM do Windows 10 ou Windows Server 2016 do Azure usando protocolo RDP (RDP). Em [Diagnósticos de inicialização](boot-diagnostics.md), a tela mostra uma cruz vermelha sobre a placa de interface de rede (NIC). Isso indica que a VM tem sem conectividade depois que o sistema operacional está totalmente carregado.

Normalmente, esse problema ocorre no Windows [build 14393](https://support.microsoft.com/help/4093120/) e [build 15063](https://support.microsoft.com/help/4015583/). Se a versão do seu sistema operacional é posterior a essas versões, este artigo não se aplica ao seu cenário. Para verificar a versão do sistema, abra uma sessão CMD no [recurso do Serial Access Console](serial-console-windows.md)e, em seguida, execute **Ver**.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se for a versão do arquivo do sistema instalados netvsc **10.0.14393.594** ou **10.0.15063.0**. Essas versões de netvsc podem impedir que o sistema de interagir com a plataforma do Azure.


## <a name="solution"></a>Solução

Antes de seguir estas etapas, [tire um instantâneo do disco do sistema](../windows/snapshot-copy-managed-disk.md) da VM afetada como um backup. Para solucionar esse problema, use o Console serial ou [repare a VM off-line](#repair-the-vm-offline) anexando o disco do sistema da VM a um VM de recuperação.


### <a name="use-the-serial-console"></a>Usar o Serial Console

Conectar-se ao [o Console Serial, abra uma instância do PowerShell](serial-console-windows.md)e, em seguida, siga estas etapas.

> [!NOTE]
> Se o Console Serial não estiver habilitado na sua VM, vá até a seção [reparar a VM offline](#repair-the-vm-offline).

1. Execute o seguinte comando em uma instância do PowerShell para obter a versão do arquivo (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Baixe a atualização apropriada para um disco de dados novo ou existente que está anexado a uma VM de trabalho da mesma região:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) ou uma atualização posterior
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

3. Desanexe o disco do utilitário da VM de trabalho e, em seguida, anexá-o à VM interrompida.

4. Execute o seguinte comando para instalar a máquina virtual:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Reinicie a VM.

### <a name="repair-the-vm-offline"></a>Reparar a VM Offline

1. [Anexar o disco do sistema para uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).

2. Inicie uma conexão de área de trabalho remota para a VM de recuperação.

3. Verifique se o disco está sinalizado como **online** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do sistema operacional anexado.

4. Criar uma cópia da pasta **\Windows\System32\config** caso uma reversão de alterações seja necessária.

5. Na VM de resgate, inicie o Editor do Registro (regedit.exe).

6. Selecione a chave de **HKEY_LOCAL_MACHINE** e, em seguida, selecione **arquivo**  >  **Carregar Hive** no menu.

7. Localize o arquivo SYSTEM na pasta **\Windows\System32\config**.

8. Selecione **Abrir**, digite **BROKENSYSTEM** para o nome, expanda a chave **HKEY_LOCAL_MACHINE** e, em seguida, localize a chave adicional que é nomeada **BROKENSYSTEM**.

9. Vá para o seguinte local:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Em cada subchave (como 0000), examine o valor **DriverDesc** que é exibido como **adaptador de rede do Microsoft HYPER-V**.

11. Na subchave, examine o valor **DriverVersion** que é a versão do driver do adaptador de rede da VM.

12. Faça o download da atualização apropriada:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) ou uma atualização posterior
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

13. Anexe o disco do sistema como um disco de dados em uma VM de resgate no qual você pode baixar a atualização.

14. Execute o seguinte comando para instalar a máquina virtual:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Execute o seguinte comando para desmontar os hives:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Desanexar o disco do sistema e criar VM novamente](./troubleshoot-recovery-disks-portal-windows.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda precisar de ajuda, [entre em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
