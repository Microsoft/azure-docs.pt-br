---
title: Não é possível conectar-se às máquinas virtuais do Azure remotamente porque a VM é inicializada no modo de segurança|Microsoft Docs
description: Saiba como solucionar um problema no qual não é possível RDP para uma VM porque a VM é inicializada no modo de segurança. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: d424cccc0a50198f3ca8c6c040afb87f44282d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508892"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Não é possível RDP para uma VM porque a VM é inicializada no modo de segurança

Este artigo mostra como resolver um problema no qual você não pode se conectar ao Windows Virtual Machines (VMs) do Azure porque a VM está configurada para inicializar no Modo de Segurança.


## <a name="symptoms"></a>Sintomas

Você não pode fazer uma conexão RDP ou outras conexões (como HTTP) para uma VM no Azure porque a VM está configurada para inicializar no Modo de Segurança. Quando você verifica a captura de tela no [Diagnóstico de inicialização](../troubleshooting/boot-diagnostics.md) no portal do Azure, talvez veja que a VM inicializa normalmente, mas a interface de rede não está disponível:

![Imagem sobre inferce de rede no modo de segurança](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Causa

O serviço RDP não está disponível no modo de segurança. Somente programas e serviços essenciais do sistema são carregados quando a VM é inicializada no Modo de segurança. Isso se aplica às duas versões diferentes do Modo de segurança, que são "Inicialização mínima segura" e "Inicialização segura com conectividade".


## <a name="solution"></a>Solução

Antes de seguir essas etapas, tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver esse problema, use o controle Serial para configurar a VM para inicializar no modo normal ou para [reparar a VM offline](#repair-the-vm-offline) usando uma VM de recuperação.

### <a name="use-serial-control"></a>Usar o Controle serial

1. Conecte-se ao [Console Serial e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Se o Console Serial não estiver habilitado em sua VM, consulte [reparar a VM off-line](#repair-the-vm-offline).
2. Verifique os dados de configuração de inicialização:

    ```console
    bcdedit /enum
    ```

    Se a VM estiver configurada para inicializar no Modo de Segurança, você verá um sinalizador extra na seção do **Carregador de Inicialização do Windows** chamada **safeboot**. Se você não vir o sinalizador **safeboot**, a VM não está no modo de segurança. Este artigo não se aplica ao seu cenário.

    O **safeboot** sinalizador pode aparecer com os seguintes valores:
   - Mínimo
   - Rede

     Em qualquer um desses dois modos, o RDP não será iniciado. Portanto, a correção permanece a mesma.

     ![Imagem sobre o sinalizador de modo de segurança](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Exclua o sinalizador **safemoade** para que a VM inicialize no modo normal:

    ```console
    bcdedit /deletevalue {current} safeboot
    ```

4. Verifique os dados de configuração de inicialização para certificar-se de que o sinalizador **safeboot** seja removido:

    ```console
    bcdedit /enum
    ```

5. Reinicie a VM e verifique se o problema foi resolvido.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. [Anexe o disco do sistema operacional a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma conexão de área de trabalho remota para a VM de recuperação.
3. Verifique se o disco está sinalizado como **online** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do SO anexado.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Ativar log de despejo e console serial (opcional)

O log de despejo e o Console Serial nos ajudarão a solucionar mais problemas se o problema não puder ser resolvido pela solução neste artigo.

Para habilitar o log de despejo e o Console Serial, execute o script a seguir.

1. Abra uma sessão de prompt de comando com privilégios elevados (**Executar como administrador**).
2. Execute o seguinte script:

    Nesse script, presumimos que a letra da unidade atribuída ao disco do SO anexado é F. Substitua essa letra da unidade pelo valor apropriado para a VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Configurar o Windows para ser inicializado no modo normal

1. Abra uma sessão de prompt de comando com privilégios elevados (**Executar como administrador**).
2. Verifique os dados de configuração da inicialização. Nos comandos a seguir, presumimos que a letra da unidade atribuída ao disco do sistema operacional anexado seja F. Substitua essa letra da unidade pelo valor apropriado para a VM.

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

    Anote o nome do Identificador da partição que tem a pasta **\windows**. Por padrão, o nome do Identificador é "Default".

    Se a VM estiver configurada para inicializar no Modo de Segurança, você verá um sinalizador extra na seção do **Carregador de Inicialização do Windows** chamada **safeboot**. Caso você não veja o sinalizador **safeboot**, este artigo não se aplicará ao seu cenário.

    ![A imagem do Identificador de inicialização](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remova o sinalizador **safeboot**, de modo que a VM seja inicializada no modo normal:

    ```console
    bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
    ```

4. Verifique os dados de configuração de inicialização para certificar-se de que o sinalizador **safeboot** seja removido:

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

5. [Desanexe o disco do SO e recrie a VM](./troubleshoot-recovery-disks-portal-windows.md). Em seguida, verifique se o problema for resolvido.
