---
title: Preparar um VHD do Windows para carregar no Azure | Microsoft Docs
description: Saiba como preparar um VHD ou VHDX do Windows para carregá-lo no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: ad30bd4f77c5f4314956e39f26a30b72d72a208a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361166"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure

Antes de carregar uma VM (máquina virtual) do Windows do local para o Azure, você deve preparar o disco rígido virtual (VHD ou VHDX). O Azure dá suporte a VMs de geração 1 e geração 2 que estão no formato de arquivo VHD e que têm um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1.023 GB. 

Em uma VM de geração 1, você pode converter um sistema de arquivos VHDX para VHD. Você também pode converter um disco de expansão dinâmica em um disco de tamanho fixo. No entanto, não é possível alterar a geração de uma VM. Para obter mais informações, consulte [devo criar uma VM de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) e [suporte do Azure para VMs de geração 2 (versão prévia)](generation-2.md).

Para obter informações sobre a política de suporte para VMs do Azure, consulte [suporte de software de servidor da Microsoft para VMs do Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> As instruções neste artigo se aplicam a:
>1. A versão de 64 bits do Windows Server 2008 R2 e sistemas operacionais Windows Server posteriores. Para obter informações sobre como executar um sistema operacional de 32 bits no Azure, consulte [suporte para sistemas operacionais de 32 bits em VMs do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Se qualquer ferramenta de recuperação de desastre for usada para migrar a carga de trabalho, como Azure Site Recovery ou migrações para Azure, esse processo ainda precisará ser feito e seguido no SO convidado para preparar a imagem antes da migração.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Converter o disco virtual em um tamanho fixo e VHD

Se você precisar converter seu disco virtual para o formato necessário para o Azure, use um dos métodos nesta seção:

1. Faça backup da VM antes de executar o processo de conversão de disco virtual.

1. Verifique se o VHD do Windows funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

1. Sobre o tamanho do VHD:

   1. Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1 MB antes da conversão. As frações de um megabyte causarão erros ao criar imagens do VHD carregado.

   2. O tamanho máximo permitido para o VHD do sistema operacional é 2TB.


Depois de converter o disco, crie uma VM que usa o disco. Inicie e entre na VM para concluir sua preparação para o carregamento.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Usar o Gerenciador do Hyper-V para converter o disco 
1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista de computadores, selecione **ação** > **Editar disco**.
2. Na página **localizar disco rígido virtual** , selecione seu disco virtual.
3. Na página **escolher ação** , selecione **converter** > **Avançar**.
4. Se você precisar converter do VHDX, selecione **VHD** > **Avançar**.
5. Se você precisar converter de um disco de expansão dinâmica, selecione **tamanho** > fixo**Avançar**.
6. Localize e selecione um caminho no qual salvar o novo arquivo VHD.
7. Selecione **Concluir**.

> [!NOTE]
> Use uma sessão do PowerShell com privilégios elevados para executar os comandos neste artigo.

### <a name="use-powershell-to-convert-the-disk"></a>Usar o PowerShell para converter o disco 
Você pode converter um disco virtual usando o comando [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) no Windows PowerShell. Escolha **Executar como administrador** ao iniciar o PowerShell. 

O comando de exemplo a seguir converte o disco de VHDX para VHD. O comando também converte o disco de um disco de expansão dinâmica em um disco de tamanho fixo.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Nesse comando, substitua o valor de `-Path` pelo caminho para o disco rígido virtual que você deseja converter. Substitua o valor de `-DestinationPath` pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem de VM do Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), use o [conversor de máquina virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497) para convertê-la em formato VHD. Para obter mais informações, consulte [como converter um VMDK do VMware para um VHD do Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

Na VM que você planeja carregar no Azure, execute os seguintes comandos em uma janela de [prompt de comandos com privilégios elevados](https://technet.microsoft.com/library/cc947813.aspx):

1. Remova qualquer rota persistente estática na tabela de roteamento:
   
   * Para exibir a tabela de rotas, execute `route print` no prompt de comando.
   * Verifique as `Persistence Routes` seções. Se houver uma rota persistente, use o `route delete` comando para removê-la.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Se a VM precisar trabalhar com um proxy específico, adicione uma exceção de proxy ao endereço IP do Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) para que a VM possa se conectar ao Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Defina a política SAN de disco [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)como:
   
    ```PowerShell
    diskpart 
    ```
    Na janela do prompt de comando abrir, digite os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Defina a hora UTC (tempo Universal Coordenado) para o Windows. Defina também o tipo de inicialização do serviço de tempo do`w32time`Windows ( `Automatic`) para:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Defina o perfil de energia como alto desempenho:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Verifique se as variáveis `TEMP` de ambiente e `TMP` estão definidas com seus valores padrão:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Verificar os serviços Windows
Verifique se cada um dos seguintes serviços do Windows está definido com os valores padrão do Windows. Esses serviços são os mínimos que devem ser configurados para garantir a conectividade da VM. Para redefinir as configurações de inicialização, execute os seguintes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar configurações do registro da área de trabalho remota
Verifique se as seguintes configurações estão definidas corretamente para acesso remoto:

>[!NOTE] 
>Você pode receber uma mensagem de erro ao executar `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. Você pode ignorar essa mensagem com segurança. Isso significa apenas que o domínio não está enviando essa configuração por Push por meio de um objeto Política de Grupo.

1. O protocolo RDP está habilitado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. A porta RDP está configurada corretamente. A porta padrão é 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Ao implantar uma VM, as regras padrão são criadas em relação à porta 3389. Se você quiser alterar o número da porta, faça isso depois que a VM for implantada no Azure.

3. O ouvinte escuta em todos os adaptadores de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configure o modo NLA (autenticação em nível de rede) para as conexões RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Defina o valor de keep alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Reconectar-se:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limite o número de conexões simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Remova todos os certificados autoassinados vinculados ao ouvinte RDP:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Esse código garante que você possa se conectar no início ao implantar a VM. Se você precisar examinar isso mais tarde, poderá fazer isso depois que a VM for implantada no Azure.

9. Se a VM for parte de um domínio, verifique as políticas a seguir para certificar-se de que as configurações anteriores não sejam revertidas. 
    
    | Meta                                     | Política                                                                                                                                                       | Valor                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP está habilitado                           | Configuração do Computador\Diretivas\Configurações do Windows\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Conexões         | Permitir que os usuários se conectem remotamente usando a Área de Trabalho Remota                                  |
    | Diretiva de grupo do NLA                         | Configurações\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Segurança                                                    | Exigir autenticação de usuário para acesso remoto usando NLA |
    | Configurações Keep-Alive                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Configurar o intervalo de conexão keep-alive                                                 |
    | Configurações de reconexão                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Reconectar automaticamente                                                                   |
    | Número limitado de configurações de conexão | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Limitar o número de conexões                                                              |

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
1. Ative o Firewall do Windows nos três perfis (domínio, padrão e público):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Execute o seguinte comando no PowerShell para permitir o WinRM por meio dos três perfis de firewall (domínio, privado e público) e habilite o serviço remoto do PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Ative as seguintes regras de firewall para permitir o tráfego RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Habilite a regra para compartilhamento de arquivos e impressoras para que a VM possa responder a um comando ping dentro da rede virtual:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Se a VM for parte de um domínio, verifique as seguintes políticas do Azure AD para certificar-se de que as configurações anteriores não são revertidas. 

    | Meta                                 | Política                                                                                                                                                  | Valor                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Habilitar os perfis do Firewall do Windows | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Proteger todas as conexões de rede         |
    | Habilitar o RDP                           | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções de área de trabalho remota de entrada |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções de área de trabalho remota de entrada |
    | Habilitar o ICMP-V4                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções do ICMP                   |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções do ICMP                   |

## <a name="verify-the-vm"></a>Verificar a VM 

Verifique se a VM está íntegra, segura e RDP acessível: 

1. Para verificar se o disco está íntegro e consistente, verifique o disco na próxima reinicialização da VM:

    ```PowerShell
    Chkdsk /f
    ```
    Verifique se o relatório mostra um disco limpo e íntegro.

2. Defina as configurações de BCD (Dados de Configuração da Inicialização). 

    > [!NOTE]
    > Use uma janela do PowerShell com privilégios elevados para executar esses comandos.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. O log de despejo pode ser útil para solucionar problemas de falha do Windows. Habilitar a coleta de log de despejo:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Verifique se o repositório do Instrumentação de Gerenciamento do Windows (WMI) é consistente:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório estiver corrompido, consulte [WMI: O repositório está corrompido ou não](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Verifique se nenhum outro aplicativo está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Para ver quais portas são usadas na VM, execute `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Para carregar um VHD do Windows que seja um controlador de domínio:

   * Siga [estas etapas extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

   * Verifique se você conhece a senha do Modo de Restauração dos Serviços de Diretório (DSRM) caso precise iniciar a VM no DSRM em algum momento. Para obter mais informações, consulte [definir uma senha do DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Verifique se você conhece a conta de administrador interna e a senha. Talvez você queira redefinir a senha do administrador local atual e verificar se pode usar essa conta para entrar no Windows por meio da conexão RDP. Essa permissão de acesso é controlada pelo objeto de Política de Grupo "permitir logon Serviços de Área de Trabalho Remota". Exibir este objeto no Editor de Política de Grupo Local aqui:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Verifique as seguintes políticas do Azure AD para certificar-se de que você não está bloqueando o acesso RDP por meio de RDP ou da rede:

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar acesso a este computador pela rede

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar logon pelos Serviços de Área de Trabalho Remota


9. Verifique a seguinte política do Azure AD para certificar-se de que você não está removendo nenhuma das contas de acesso necessárias:

   - O computador \ \ \ \ \ \ \ \% Assignment\Access de direitos \% no computador da rede

   A política deve listar os seguintes grupos:

   - Administradores

   - Operadores de Backup

   - Todos

   - Usuários

10. Reinicie a VM para certificar-se de que o Windows ainda está íntegro e pode ser acessado por meio da conexão RDP. Neste ponto, talvez você queira criar uma VM no seu Hyper-V local para garantir que a VM seja iniciada completamente. Em seguida, teste para certificar-se de que você pode acessar a VM por meio de RDP.

11. Remova os filtros adicionais de interface do driver de transporte (TDI). Por exemplo, remova o software que analisa pacotes TCP ou firewalls extras. Se você precisar examinar isso mais tarde, poderá fazer isso depois que a VM for implantada no Azure.

12. Desinstale qualquer outro software ou driver de terceiros que esteja relacionado a componentes físicos ou a qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
O ideal é que você mantenha a máquina atualizada no *nível do patch*. Se isso não for possível, verifique se as seguintes atualizações estão instaladas:

| Componente               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Armazenamento                 | disk.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17638/6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17188/6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17623/6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 – KB3125574                | 6.2.9200.16384 – KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17061/6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614         | 10.0.14393.953 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17047/6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726         | 10.0.14393.1066 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 – KB3125574                | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726         | 10.0.14393.1198 – KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614         | 10.0.14393.576 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614         | 10.0.14393.206 – KB4022715                              | -                          | -                                               | -                                               |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 – KB4022715                             | 10.0.15063.250 – KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726         | 10.0.14393.479 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 – KB4022722                | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 – KB4022722                | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726         | 10.0.14393.251 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 – KB4022719                | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 – KB4022719                | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Serviços da Área de Trabalho Remota | rdpcorets.dll  | 6.2.9200.21506 – KB4022719                | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 – KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 – KB4022719                | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726         | 10.0.14393.594 – KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Determinar quando usar o Sysprep<a id="step23"></a>    

A ferramenta de preparação do sistema (Sysprep) é um processo que pode ser executado para redefinir uma instalação do Windows. O Sysprep fornece uma experiência "pronta para uso" removendo todos os dados pessoais e redefinindo vários componentes. 

Normalmente, você executa o Sysprep para criar um modelo no qual é possível implantar várias outras VMs que têm uma configuração específica. O modelo é chamado de *imagem generalizada*.

Se você quiser criar apenas uma VM de um disco, não precisará usar o Sysprep. Em vez disso, você pode criar a VM com base em uma *imagem especializada*. Para obter informações sobre como criar uma VM de um disco especializado, consulte:

- [Criar uma VM com base em um disco especializado](create-vm-specialized.md)
- [Criar uma VM com base em um disco VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se você quiser criar uma imagem generalizada, precisará executar o Sysprep. Para obter mais informações, [consulte como usar o Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx). 

Nem toda função ou aplicativo instalado em um computador baseado no Windows dá suporte a imagens generalizadas. Portanto, antes de executar esse procedimento, verifique se o Sysprep dá suporte à função do computador. Para obter mais informações, confira [Sysprep support for server role](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Suporte do Sysprep para funções de servidor).

### <a name="generalize-a-vhd"></a>Generalizar um VHD

>[!NOTE]
> Depois de executar `sysprep.exe` as etapas a seguir, desative a VM. Não ative-a novamente até criar uma imagem a partir dela no Azure.

1. Entre na VM Windows.
1. Execute o **Prompt de Comando** como administrador. 
1. Altere o diretório para `%windir%\system32\sysprep`. Em seguida, execute `sysprep.exe`.
1. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.

    ![Ferramenta de Preparação do Sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. Em **Opções de Desligamento**, selecione **Desligar**.
1. Selecione **OK**.
1. Quando o Sysprep for concluído, desligue a VM. Não use  reinicialização para desligar a VM.

Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM de um disco generalizado, consulte [carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](sa-upload-generalized.md).


>[!NOTE]
> Não há suporte para um arquivo *Unattend. xml* personalizado. Embora possamos dar suporte à `additionalUnattendContent` Propriedade, que fornece apenas suporte limitado para adicionar as opções [Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) no arquivo *Unattend. xml* que o agente de provisionamento do Azure usa. Você pode usar, por exemplo, [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) para adicionar FirstLogonCommands e LogonCommands. Para obter mais informações, consulte [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Concluir as configurações recomendadas
As configurações a seguir não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

* Instale o [agente de máquina virtual do Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, você pode habilitar as extensões de VM. As extensões de VM implementam a maior parte da funcionalidade crítica que você pode querer usar com suas VMs. Você precisará das extensões, por exemplo, para redefinir senhas ou configurar o RDP. Para obter mais informações, consulte [visão geral do agente de máquina virtual do Azure](../extensions/agent-windows.md).
* Depois de criar a VM no Azure, recomendamos que você coloque o arquivo de paginação no *volume da unidade temporal* para melhorar o desempenho. Você pode configurar o posicionamento do arquivo da seguinte maneira:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Se um disco de dados estiver anexado à VM, a letra do volume da unidade temporal normalmente será *D*. Essa designação pode ser diferente, dependendo de suas configurações e do número de unidades disponíveis.

## <a name="next-steps"></a>Próximas etapas
* [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](upload-generalized-managed.md)
* [Solucionar problemas de ativação de VM do Windows do Azure](troubleshoot-activation-problems.md)

