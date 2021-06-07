---
title: Preparar um VHD do Windows para upload no Azure
description: Saiba como preparar um VHD ou VHDX do Windows para carregá-lo no Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: a177fc7e17dc91a0d57fa6dee87b80921d7fd8f5
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043573"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure

Antes de carregar uma VM (máquina virtual) do Windows do local para o Azure, você deve preparar o disco rígido virtual (VHD ou VHDX). O Azure dá suporte a VMs de geração 1 e geração 2 que estão no formato de arquivo VHD e que têm um disco de tamanho fixo. O tamanho máximo permitido para o VHD do sistema operacional em uma VM de geração 1 é 2 TB.

Você pode converter um arquivo VHDX em VHD, converter um disco de expansão dinâmica em um disco de tamanho fixo, mas não pode alterar a geração de uma VM. Para obter mais informações, consulte [devo criar uma VM de geração 1 ou 2 no Hyper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) e [suporte para VMs de geração 2 no Azure](../generation-2.md).

Para obter informações sobre a política de suporte para VMs do Azure, consulte [suporte de software de servidor da Microsoft para VMs do Azure](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> As instruções neste artigo se aplicam a:
>
> - A versão de 64 bits do Windows Server 2008 R2 e sistemas operacionais Windows Server posteriores. Para obter informações sobre como executar um sistema operacional de 32 bits no Azure, consulte [suporte para sistemas operacionais de 32 bits em VMs do Azure](https://support.microsoft.com/help/4021388/).
> - Se qualquer ferramenta de recuperação de desastre for usada para migrar a carga de trabalho, como Azure Site Recovery ou migrações para Azure, esse processo ainda será necessário no sistema operacional convidado para preparar a imagem antes da migração.

## <a name="system-file-checker"></a> Verificador de Arquivos do Sistema

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Executar o utilitário do verificador de arquivos do sistema Windows antes da generalização da imagem do sistema operacional

O verificador de arquivos do sistema (SFC) é usado para verificar e substituir os arquivos do sistema Windows.

> [!IMPORTANT]
> Use uma sessão do PowerShell com privilégios elevados para executar os exemplos neste artigo.

Execute o comando SFC:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Após a verificação do SFC ser concluída, instale as atualizações do Windows e reinicie o computador.

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

> [!NOTE]
> A plataforma Azure monta um arquivo ISO para o DVD-ROM quando uma VM do Windows é criada a partir de uma imagem generalizada. Por esse motivo, o DVD-ROM deve ser habilitado no sistema operacional na imagem generalizada. Se estiver desabilitada, a VM do Windows ficará presa na experiência inicial do uso (OOBE).

1. Remova todas as rotas persistentes estáticas na tabela de roteamento:

   - Para exibir a tabela de roteamento, execute `route.exe print` .
   - Verifique a seção **rotas de persistência** . Se houver uma rota persistente, use o `route.exe delete` comando para removê-la.

1. Remova o proxy de WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Se a VM precisar trabalhar com um proxy específico, adicione uma exceção de proxy para o endereço IP do Azure ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) para que a VM possa se conectar ao Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Abra o DiskPart:

   ```powershell
   diskpart.exe
   ```

   Defina a política SAN de disco como [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Defina a hora UTC (tempo Universal Coordenado) para o Windows. Além disso, defina o tipo de inicialização do serviço de tempo do Windows **W32Time** como **automático**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Defina o perfil de energia como alto desempenho:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Verifique se as variáveis de ambiente **Temp** e **tmp** estão definidas com seus valores padrão:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Verificar os serviços Windows

Verifique se cada um dos seguintes serviços do Windows está definido com o valor padrão do Windows. Esses serviços são os mínimos que devem ser configurados para garantir a conectividade da VM. Para definir as configurações de inicialização, execute o seguinte exemplo:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar configurações do registro da área de trabalho remota

Verifique se as seguintes configurações estão definidas corretamente para acesso remoto:

> [!NOTE]
> Se você receber uma mensagem de erro durante a execução `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , poderá ignorá-la com segurança. Isso significa que o domínio não está definindo essa configuração por meio de um objeto Política de Grupo.

1. O protocolo RDP está habilitado:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. A porta RDP é configurada corretamente usando a porta padrão de 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Quando você implanta uma VM, as regras padrão são criadas para a porta 3389. Para alterar o número da porta, faça isso depois que a VM for implantada no Azure.

1. O ouvinte está escutando em cada interface de rede:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Configure o modo NLA (autenticação em nível de rede) para as conexões RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Defina o valor de keep alive:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Defina as opções de reconexão:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Limite o número de conexões simultâneas:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Remova todos os certificados autoassinados vinculados ao ouvinte RDP:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Esse código garante que você possa se conectar ao implantar a VM. Você também pode examinar essas configurações depois que a VM for implantada no Azure.

1. Se a VM fizer parte de um domínio, verifique as políticas a seguir para certificar-se de que as configurações anteriores não sejam revertidas.

    |                 Goal                  |                                                                            Política                                                                            |                           Valor                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP está habilitado                        | Configuração do Computador\Diretivas\Configurações do Windows\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Conexões         | Permitir que os usuários se conectem remotamente usando a Área de Trabalho Remota    |
    | Diretiva de grupo do NLA                      | Configurações\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Segurança                                                    | Exigir autenticação de usuário para acesso remoto usando NLA |
    | Configurações Keep-Alive                   | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Configurar o intervalo de conexão keep-alive                   |
    | Configurações de reconexão                    | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Reconectar automaticamente                                    |
    | Número limitado de configurações de conexão | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Limitar o número de conexões                                |

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows

1. Ative o Firewall do Windows nos três perfis (domínio, padrão e público):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Execute o exemplo a seguir para permitir o WinRM por meio dos três perfis de firewall (domínio, privado e público) e habilite o serviço remoto do PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Ative as seguintes regras de firewall para permitir o tráfego RDP:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Habilite a regra para compartilhamento de arquivos e impressoras para que a VM possa responder às solicitações de ping dentro da rede virtual:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Crie uma regra para a rede da plataforma Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Se a VM fizer parte de um domínio, verifique as seguintes políticas do Azure AD para certificar-se de que as configurações anteriores não sejam revertidas.

    |                 Goal                 |                                                                         Política                                                                          |                  Valor                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Habilitar os perfis do Firewall do Windows | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Proteger todas as conexões de rede         |
    | Habilitar o RDP                           | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções de área de trabalho remota de entrada |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções de área de trabalho remota de entrada |
    | Habilitar o ICMP-V4                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções do ICMP                   |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções do ICMP                   |

## <a name="verify-the-vm"></a>Verificar a VM

Verifique se a VM está íntegra, segura e RDP acessível:

1. Para verificar se o disco está íntegro e consistente, verifique o disco na próxima reinicialização da VM:

   ```powershell
   chkdsk.exe /f
   ```

   Verifique se o relatório mostra um disco limpo e íntegro.

1. Defina as configurações de BCD (Dados de Configuração da Inicialização).

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. O log de despejo pode ser útil para solucionar problemas de falha do Windows. Habilitar a coleta de log de despejo:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Verifique se o repositório do Instrumentação de Gerenciamento do Windows (WMI) é consistente:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Se o repositório estiver corrompido, consulte [WMI: repositório corrompido ou não](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Verifique se nenhum outro aplicativo está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Para ver quais portas são usadas na VM, execute `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Para carregar um VHD do Windows que seja um controlador de domínio:

   - Siga [estas etapas extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

   - Verifique se você conhece a senha do Modo de Restauração dos Serviços de Diretório (DSRM) caso precise iniciar a VM no DSRM. Para obter mais informações, consulte [definir uma senha do DSRM](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Verifique se você conhece a conta de administrador interna e a senha. Talvez você queira redefinir a senha do administrador local atual e verificar se pode usar essa conta para entrar no Windows por meio da conexão RDP. Essa permissão de acesso é controlada pelo objeto de Política de Grupo "permitir logon Serviços de Área de Trabalho Remota". Exibir este objeto no Editor de Política de Grupo Local:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Verifique as seguintes políticas do Azure AD para certificar-se de que não estão bloqueando o acesso RDP:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Verifique a seguinte política do Azure AD para certificar-se de que não está removendo nenhuma das contas de acesso necessárias:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   A política deve listar os seguintes grupos:

   - Administradores

   - Operadores de cópia

   - Todos

   - Usuários

1. Reinicie a VM para certificar-se de que o Windows ainda está íntegro e pode ser acessado por meio da conexão RDP. Neste ponto, considere a criação de uma VM no servidor local do Hyper-V para garantir que a VM seja iniciada completamente. Em seguida, teste para certificar-se de que você pode acessar a VM por meio de RDP.

1. Remova os filtros adicionais de interface do driver de transporte (TDI). Por exemplo, remova o software que analisa pacotes TCP ou firewalls extras.

1. Desinstale qualquer outro software ou driver de terceiros que esteja relacionado a componentes físicos ou a qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows

O ideal é que você mantenha o computador atualizado para o *nível de patch*, se isso não for possível, verifique se as atualizações a seguir estão instaladas. Para obter as atualizações mais recentes, consulte as páginas do histórico do Windows Update: [Windows 10 e Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4009470) e [Windows 7 SP1 e Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Componente        |     Binário     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Armazenamento                 | disk.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17638/6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17188/6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726          | 10.0.14393.1358 – KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17623/6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726          | 10.0.14393.1198 – KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 – KB3125574                | 6.2.9200.16384 – KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17061/6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614          | 10.0.14393.953 – KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17047/6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850          | 10.0.14393.953 – KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 – KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726          | 10.0.14393.1066 – KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 – KB3125574                | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726          | 10.0.14393.1198 – KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614          | 10.0.14393.576 – KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614          | 10.0.14393.206 – KB4022715                  | -                          | -                                           | -                                           |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 – KB4022715                 | 10.0.15063.250 – KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726          | 10.0.14393.479 – KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726          | 10.0.14393.953 – KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 – KB4022722                | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726          | 10.0.14393.953 – KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 – KB4022722                | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726          | 10.0.14393.1358 – KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726          | 10.0.14393.251 – KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 – KB4022719                | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726          | 10.0.14393.1358 – KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 – KB4022719                | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726          | 10.0.14393.1358 – KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Serviços de área de trabalho remota | rdpcorets.dll  | 6.2.9200.21506 – KB4022719                | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726          | 10.0.14393.1198 – KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850          | 10.0.14393.0 – KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 – KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 – KB4022719                | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726          | 10.0.14393.594 – KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 – KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 – KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Para evitar uma reinicialização acidental durante o provisionamento da VM, é recomendável garantir que todas as instalações de Windows Update sejam concluídas e que nenhuma atualização esteja pendente. Uma maneira de fazer isso é instalar todas as possíveis atualizações do Windows e reinicializar uma vez antes de executar o `sysprep.exe` comando.

## <a name="determine-when-to-use-sysprep"></a>Determinar quando usar o Sysprep

A ferramenta de preparação do sistema ( `sysprep.exe` ) é um processo que você pode executar para redefinir uma instalação do Windows.
O Sysprep fornece uma experiência "pronta para uso" removendo todos os dados pessoais e redefinindo vários componentes.

Normalmente, você executa `sysprep.exe` o para criar um modelo no qual é possível implantar várias outras VMs que têm uma configuração específica. O modelo é chamado de *imagem generalizada*.

Para criar apenas uma VM de um disco, você não precisa usar o Sysprep. Em vez disso, você pode criar a VM com base em uma *imagem especializada*. Para obter informações sobre como criar uma VM de um disco especializado, consulte:

- [Criar uma VM com base em um disco especializado](create-vm-specialized.md)
- [Criar uma VM com base em um disco VHD](./create-vm-specialized-portal.md)

Para criar uma imagem generalizada, você precisa executar o Sysprep. Para obter mais informações, consulte [como usar o Sysprep: uma introdução](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Nem toda função ou aplicativo instalado em um computador baseado no Windows dá suporte a imagens generalizadas. Antes de usar esse procedimento, verifique se o Sysprep dá suporte à função do computador. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

Em particular, o Sysprep exige que as unidades sejam totalmente descriptografadas antes da execução. Se você habilitou a criptografia em sua VM, desabilite-a antes de executar o Sysprep.


### <a name="generalize-a-vhd"></a>Generalizar um VHD

>[!NOTE]
> Depois de executar as `sysprep.exe` etapas a seguir, desative a VM. Não ative-a novamente até criar uma imagem a partir dela no Azure.

1. Entre na VM Windows.
1. Execute uma sessão do PowerShell como administrador.
1. Exclua o diretório Panther (C:\Windows\Panther).
1. Altere o diretório para `%windir%\system32\sysprep` . Em seguida, execute `sysprep.exe`.
1. Na caixa de diálogo **ferramenta de preparação do sistema** , selecione entrar no **OOBE (experiência inicial do sistema)** e verifique se a caixa de seleção **generalizar** está selecionada.

    ![Ferramenta de Preparação do Sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. Em **Opções de Desligamento**, selecione **Desligar**.
1. Selecione **OK**.
1. Quando o Sysprep for concluído, desligue a VM. Não use **reinicialização** para desligar a VM.

Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM de um disco generalizado, consulte [carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Não há suporte para um arquivo de *unattend.xml* personalizado. Embora possamos dar suporte à propriedade **additionalUnattendContent** , que fornece apenas suporte limitado para adicionar as opções [Microsoft-Windows-Shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) no arquivo *unattend.xml* que o agente de provisionamento do Azure usa. Você pode usar, por exemplo, [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) para adicionar FirstLogonCommands e LogonCommands. Para obter mais informações, consulte [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Converter o disco virtual em um VHD de tamanho fixo

Use um dos métodos nesta seção para converter e redimensionar seu disco virtual para o formato necessário para o Azure:

1. Faça backup da VM antes de executar o processo de conversão ou redimensionamento de disco virtual.

1. Verifique se o VHD do Windows funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

1. Converta o disco virtual para o tipo fixo.

1. Redimensione o disco virtual para atender aos requisitos do Azure:

   1. Os discos no Azure devem ter um tamanho virtual alinhado a 1 MiB. Se o VHD for uma fração de 1 MiB, você precisará redimensionar o disco para um múltiplo de 1 MiB. Os discos que são frações de uma MiB causam erros ao criar imagens do VHD carregado. Para verificar o tamanho, você pode usar o cmdlet [Get-VHD](/powershell/module/hyper-v/get-vhd) do PowerShell para mostrar "tamanho", que deve ser um múltiplo de 1 MIB no Azure e "filesize", que será igual ao "tamanho", mais 512 bytes para o rodapé do VHD.
   
   1. O tamanho máximo permitido para o VHD do sistema operacional com uma VM de geração 1 é 2.048 GiB (2 TiB), 
   1. O tamanho máximo de um disco de dados é 32.767 GiB (32 TiB).

> [!NOTE]
> - Se você estiver preparando um disco do sistema operacional Windows depois de converter para um disco fixo e redimensionar, se necessário, crie uma VM que usa o disco. Inicie o e entre na VM e continue com as seções neste artigo para concluir sua preparação para carregamento.  
> - Se você estiver preparando um disco de dados, poderá parar esta seção e prosseguir com o carregamento do disco.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Usar o Gerenciador do Hyper-V para converter o disco

1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista de computadores, selecione **ação**  >  **Editar disco**.
1. Na página **localizar disco rígido virtual** , selecione seu disco virtual.
1. Na página **escolher ação** , selecione **converter**  >  **Avançar**.
1. Para converter do VHDX, selecione **VHD**  >  **Avançar**.
1. Para converter de um disco de expansão dinâmica, selecione **tamanho fixo**  >  **Avançar**.
1. Localize e selecione um caminho para salvar o novo arquivo VHD.
1. Selecione **Concluir**.

### <a name="use-powershell-to-convert-the-disk"></a>Usar o PowerShell para converter o disco

Você pode converter um disco virtual usando o cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) no PowerShell. Se você precisar de informações sobre como instalar este cmdlet [, consulte instalar a função Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

O exemplo a seguir converte o disco de VHDX para VHD. Ele também converte o disco de um disco de expansão dinâmica em um disco de tamanho fixo.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Neste exemplo, substitua o valor de **path** pelo caminho para o disco rígido virtual que você deseja converter. Substitua o valor de **DestinationPath** pelo novo caminho e nome do disco convertido.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Usar o Gerenciador do Hyper-V para redimensionar o disco

1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista de computadores, selecione **ação**  >  **Editar disco**.
1. Na página **localizar disco rígido virtual** , selecione seu disco virtual.
1. Na página **escolher ação** , selecione **expandir**  >  **Avançar**.
1. Na página **localizar disco rígido virtual** , insira o novo tamanho em GIB > **Avançar**.
1. Selecione **Concluir**.

### <a name="use-powershell-to-resize-the-disk"></a>Usar o PowerShell para redimensionar o disco

Você pode redimensionar um disco virtual usando o cmdlet [Resize-VHD](/powershell/module/hyper-v/resize-vhd) no PowerShell. Se você precisar de informações sobre como instalar este cmdlet [, consulte instalar a função Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

O exemplo a seguir redimensiona o disco de 100,5 MiB para 101 MiB para atender ao requisito de alinhamento do Azure.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

Neste exemplo, substitua o valor de **path** pelo caminho para o disco rígido virtual que você deseja redimensionar. Substitua o valor de **SizeBytes** pelo novo tamanho em bytes para o disco.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK

Se você tiver uma imagem de VM do Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), poderá usar as [migrações para Azure](../../migrate/server-migrate-overview.md) para converter o VMDK e carregá-lo no Azure.

## <a name="complete-the-recommended-configurations"></a>Concluir as configurações recomendadas

As configurações a seguir não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

- Instale o [agente de máquina virtual do Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Em seguida, você pode habilitar as extensões de VM. As extensões de VM implementam a maior parte da funcionalidade crítica que você pode querer usar com suas VMs. Você precisará das extensões, por exemplo, para redefinir senhas ou configurar o RDP. Para obter mais informações, consulte a [visão geral do agente de máquina virtual do Azure](../extensions/agent-windows.md).
- Depois de criar a VM no Azure, recomendamos que você coloque o arquivo de paginação no *volume da unidade temporal* para melhorar o desempenho. Você pode configurar o posicionamento do arquivo da seguinte maneira:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Se um disco de dados estiver anexado à VM, a letra do volume da unidade temporal normalmente será *D*. Essa designação pode ser diferente, dependendo de suas configurações e do número de unidades disponíveis.

  - Recomendamos desabilitar os bloqueadores de script que podem ser fornecidos pelo software antivírus. Eles podem interferir e bloquear os scripts do agente de provisionamento do Windows executados quando você implanta uma nova VM a partir de sua imagem.

## <a name="next-steps"></a>Próximas etapas

- [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](upload-generalized-managed.md)
- [Solucionar problemas de ativação de VM do Windows do Azure](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)