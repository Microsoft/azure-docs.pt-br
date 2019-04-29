---
title: Firewall do SO Guest do Azure VM está bloqueando o tráfego de entrada | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711538"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>O firewall do sistema operacional convidado do Azure VM está bloqueando o tráfego de entrada

Este artigo descreve como corrigir o problema RDP (Portal da Área de Trabalho Remota) que ocorre se o firewall do sistema operacional convidado bloquear o tráfego de entrada.

## <a name="symptoms"></a>Sintomas

Você não pode usar uma conexão RDP para se conectar a uma máquina virtual (VM) do Azure. Em Diagnósticos de inicialização -> Captura de tela, mostra que o sistema operacional está totalmente carregado na tela Bem-vindo (Ctrl + Alt + Del).

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

A regra RDP não está configurada para permitir o tráfego RDP.

### <a name="cause-2"></a>Causa 2

Os perfis de firewall do sistema de convidado são configurados para bloquear todas as conexões de entrada, incluindo o tráfego de RDP.

![Configuração de firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Solução

Antes de seguir estas etapas, tire um instantâneo do disco do sistema da VM afetada como um backup. Para obter mais informações, consulte  [ Snapshot de um disco ](../windows/snapshot-copy-managed-disk.md).

Para corrigir o problema, use um dos métodos em [Como usar ferramentas remotas para solucionar problemas do VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md) para se conectar à VM remotamente e edite as regras de firewall do sistema operacional convidado para **Allow** Tráfego RDP.

### <a name="online-troubleshooting"></a>Resolução de problemas online

Conecte-se ao [ Console serial e abra uma instância do PowerShell ](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se o Console Serial não estiver ativado na VM, vá para "[Reparar a VM Off-line](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Mitigação 1

1.  Se o agente do Azure está instalado e funcionando corretamente na máquina virtual, você pode usar a opção de "Redefinir somente configuração" em **suporte + solução de problemas** > **redefinição de senha** no menu da VM.

2.  Executando a opção de recuperação faz o seguinte:

    *   Permite que um componente RDP, se ele estiver desabilitado.

    *   Ativa todos os perfis de firewall do Windows.

    *   Certifique-se de que a regra RDP está ativada no Firewall do Windows.

    *   Se as etapas anteriores não funcionarem, redefina manualmente a regra de firewall. Para fazer isso, consulte todas as regras que contêm o nome "Área de trabalho remota", executando o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Se a porta RDP tiver sido definida para qualquer outra porta diferente de 3389, você deverá encontrar qualquer regra personalizada que possa ter sido criada e definida para essa porta. Para consultar todas as regras de entrada que possuem uma porta personalizada, execute o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Se você vir que a regra está desabilitada, habilitá-lo. Para abrir um grupo inteiro, como o grupo da Área de Trabalho Remota interna, execute o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Caso contrário, para abrir a regra específica da Área de Trabalho Remota (TCP-In), execute o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Para solucionar problemas, você pode transformar os perfis do firewall em OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Depois de concluir a solução de problemas e configurar o firewall corretamente, reative o firewall.

    > [!Note]
    > Você não precisa reiniciar a VM para aplicar essas alterações.

5.  Tente fazer uma conexão de RDP para acessar a VM.

#### <a name="mitigation-2"></a>Mitigação 2

1.  Consultar os perfis de firewall para determinar se a política de firewall de entrada é definida como *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > As diretrizes a seguir se aplicam à política do firewall, dependendo de como ela é configurada:
    >    * *BlockInbound*: Todo o tráfego de entrada será bloqueado, a menos que você tenha uma regra em vigor para permitir esse tráfego.
    >    * *BlockInboundAlways*: Todas as regras de firewall serão ignoradas e todo o tráfego será bloqueado.

2.  Edite  *DefaultInboundAction* para definir esses perfis para o tráfego  **Allow** . Para fazer isso, execute o seguinte comando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Consulte os perfis novamente para garantir que sua alteração foi feita com sucesso. Para fazer isso, execute o seguinte comando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Você não precisa reiniciar a VM para aplicar as alterações.

4.  Tente novamente acessar sua VM via RDP.

### <a name="offline-mitigations"></a>Redução Offline

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma conexão de área de trabalho remota para a VM de recuperação.

3.  Certifique-se de que o disco esteja sinalizado como  **Online**  no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do sistema operacional anexado.

#### <a name="mitigation-1"></a>Mitigação 1

Veja [Como habilitar/desabilitar uma regra de Firewall em um SO convidado](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Mitigação 2

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma conexão de área de trabalho remota para a VM de recuperação.

3.  Depois que o disco do sistema for conectado à VM de recuperação, verifique se o disco está sinalizado como  **On-line** no console de Gerenciamento de Disco. Anote a letra da unidade atribuída ao disco do SO anexado.

4.  Abra uma instância CMD elevada e, em seguida, execute o seguinte script:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Desanexe o disco do sistema e recrie a VM](troubleshoot-recovery-disks-portal-windows.md).

6.  Verifique se o problema foi resolvido.
