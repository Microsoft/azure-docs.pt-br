---
title: A área de trabalho remota desconecta com frequência na VM do Azure | Microsoft Docs
description: Aprenda a solucionar problemas de desconexões frequentes da área de trabalho remota na VM do Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 60be7c234a0166331c35eb6528eae11bfbbf518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074310"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>A área de trabalho remota se desconecta com frequência na VM do Azure

Este artigo explica como solucionar problemas de desconexões frequentes em uma máquina virtual do Azure (VM) por meio do RDP do Protocolo de Área de Trabalho Remota.


## <a name="symptom"></a>Sintoma

Você enfrenta problemas de conectividade RDP intermitentes durante suas sessões. Inicialmente, você pode se conectar à VM, mas, em seguida, a conexão cair.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se o RDP Listener estiver configurado incorretamente. Normalmente, esse problema ocorre em uma VM que usa uma imagem personalizada.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, [tire um instantâneo do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) da VM afetada como um backup. 

Para solucionar esse problema, use o controle Serial ou [repare a VM off-line](#repair-the-vm-offline) anexando o disco do SO da VM a uma VM de recuperação.

### <a name="serial-control"></a>Controle serial

1. Conecte-se ao [Console Serial e abra a instância CMD](./serial-console-windows.md). Em seguida, execute os seguintes comandos para redefinir as configurações do RDP. Se o console serial não estiver habilitado em sua VM, vá para a próxima etapa.
2. Reduza a Camada de Segurança RDP para 0. Nessa configuração, as comunicações entre o servidor e o cliente usam a criptografia RDP nativa.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

3. Diminua o nível de criptografia para a configuração mínima para permitir que os clientes RDP herdados se conectem.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

4. Defina o RDP para carregar a configuração do usuário do computador cliente.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

5. Ative o controle Keep-Alive do RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

6. Defina o controle de reconexão de RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

7. Defina o controle de tempo da sessão de RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

8. Defina o controle de tempo de desconexão de RDP: 

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f` `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

9. Defina o controle de tempo de Conexão de RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

10. Defina o controle de tempo de ociosidade da sessão de RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f`

11. Defina o controle de "Limite máximo de conexões simultâneas":

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f`

12. Reinicie a VM e tente novamente para se conectar a ele usando o RDP.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

1. [Anexe o disco do sistema operacional a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Depois que o disco do sistema operacional é anexado à VM de recuperação, verifique se o disco está sinalizado como **on-line** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do SO anexado.
3. No disco do SO conectado, navegue até a pasta **\windows\system32\config**. Copie todos os arquivos nesta pasta como backup, no caso de uma reversão ser necessária.
4. Inicie o Editor do registro (regedit.exe).
5. Selecione a chave **HKEY_LOCAL_MACHINE**. No menu, selecione **arquivo**  >  **Carregar Hive**:
6. Navegue até a pasta **\windows\system32\config\SYSTEM** no disco do sistema operacional que você anexou. Como o nome da seção, digite **BROKENSYSTEM**. O novo hive do Registro é exibido sob a chave **HKEY_LOCAL_MACHINE**. Em seguida, carregue a seção de software **\windows\system32\config\SOFTWARE** sob a chave **HKEY_LOCAL_MACHINE**. Para o nome do software de hive, digite **BROKENSOFTWARE**. 
7. Abra uma janela elevada do Prompt de Comando (**Executar como administrador**) e execute comandos nas etapas restantes para redefinir as configurações do RDP. 
8. Reduza a Camada de Segurança RDP para 0, para que as comunicações entre o servidor e o cliente usem a Criptografia RDP nativa:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

9. Reduza o nível de criptografia para a configuração mínima para permitir que clientes RDP herdados se conectem:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

10. Configure o RDP para carregar a configuração do usuário da máquina cliente.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

11. Ative o controle Keep-Alive do RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

12. Defina o controle de reconexão de RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

13. Defina o controle de tempo da sessão de RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

14. Defina o controle de tempo de desconexão de RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

15. Defina o controle de tempo de Conexão de RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

16. Defina o controle de tempo de ociosidade da sessão de RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

17. Defina o controle de "Limite máximo de conexões simultâneas":

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

18. Reinicie a VM e tente novamente para se conectar a ele usando o RDP.

## <a name="need-help"></a>Precisa de ajuda? 
Entre em contato com o suporte. Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
