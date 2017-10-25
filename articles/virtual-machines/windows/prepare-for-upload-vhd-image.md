---
title: Preparar um VHD do Windows para carregar no Azure | Microsoft Docs
description: Como preparar um VHD ou VHDX do Windows antes de carregar no Azure
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2017
ms.author: genli
ms.openlocfilehash: 8f3d6fc56b64d81d5f9b73772ddf2a4ab9adf64b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure
Antes de carregar uma VM (máquina virtual) Windows do local para o Microsoft Azure, você deve preparar o VHD (disco rígido virtual) ou VHDX. O Azure dá suporte apenas a VMs de geração 1 no formato de arquivo VHD e que têm um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1.023 GB. Você pode converter uma VM de geração 1 do sistema de arquivos VHD para VHDX e de um disco de expansão dinâmica para um disco de tamanho fixo. No entanto, não é possível alterar a geração de uma VM. Para obter mais informações, consulte [Devo criar uma VM de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Para obter mais informações sobre a política de suporte para a VM do Azure, consulte [Suporte de software para servidores da Microsoft para VMs do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> As instruções deste artigo se aplicam à versão de 64 bits do Windows Server 2008 R2 e sistema operacional Windows posterior. Para obter informações sobre como executar a versão de 32 bits do sistema operacional no Azure, consulte [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Converter o disco virtual em VHD e disco de tamanho fixo 
Se precisar converter o disco virtual em um formato necessário para o Azure, use um dos métodos desta seção. Faça backup da VM antes de executar o processo de conversão do disco virtual e verifique se o VHD do Windows funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

Depois de converter o disco, crie uma VM que use o disco convertido. Inicie e entre na VM a fim de concluir a preparação da VM para carregamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Converter disco usando o Gerenciador do Hyper-V
1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista de computadores, clique em **Ação** > **Editar Disco**.
2. Na tela **Localizar Disco Rígido Virtual**, localize e selecione o disco virtual.
3. Na tela **Escolher Ação**, selecione **Converter** e **Avançar**.
4. Se precisar converter de VHDX, selecione **VHD** e, depois, clique em **Avançar**
5. Se precisar converter de um disco de expansão dinâmica, selecione **Tamanho fixo** e, depois, clique em **Avançar**
6. Localize e selecione um caminho no qual salvar o novo arquivo VHD.
7. Clique em **Concluir**.

>[!NOTE]
>Os comandos deste artigo devem ser executados em uma sessão do PowerShell com privilégios elevados.

### <a name="convert-disk-by-using-powershell"></a>Converter disco usando o PowerShell
Você pode converter um disco virtual usando o comando [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) no Windows PowerShell. Escolha **Executar como administrador** ao iniciar o PowerShell. 

O seguinte comando de exemplo faz a conversão de um VHDX em VHD e de um disco de expansão dinâmica em um de tamanho fixo:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Nesse comando, substitua o valor de “-Path” pelo caminho do disco rígido virtual que você deseja converter e “-DestinationPath” pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem de VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a em um VHD usando o [Conversor de VM da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Para obter mais informações, consulte o artigo no blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK do VMware em um VHD do Hyper-V).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

Na VM que você pretende carregar no Azure, execute todos os comandos nas seguintes etapas em uma [janela do Prompt de Comando com privilégios elevados](https://technet.microsoft.com/library/cc947813.aspx):

1. Remova qualquer rota persistente estática na tabela de roteamento:
   
   * Para exibir a tabela de rotas, execute `route print` no prompt de comando.
   * Verifique as seções **Rotas de Persistência** . Se houver uma rota persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para removê-la.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Defina a política de SAN do disco como [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    Na janela do Prompt de Comando aberta, digite os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Defina a hora UTC (Tempo Universal Coordenado) para o Windows e o tipo de inicialização do serviço Horário do Windows (w32time) como **Automaticamente**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Defina o perfil de energia para o **Alto Desempenho**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Verificar os serviços Windows
Verifique se cada um dos seguintes serviços do Windows está definido como os **valores padrão do Windows**. Esse é o número mínimo de serviços que deve ser configurado para garantir que a VM tenha conectividade. Para redefinir as configurações de inicialização, execute os seguintes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar configurações de registro da Área de Trabalho Remota
Verifique se as seguintes configurações estão configuradas corretamente para a conexão de área de trabalho remota:

>[!Note] 
>Você poderá receber uma mensagem de erro ao executar o **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; &lt;value&gt;** nestas etapas. A mensagem de erro pode ser ignorada. Isso significa apenas que o domínio não está enviando a configuração por push por meio de um objeto de Política de Grupo.
>
>

1. O protocolo RDP está habilitado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. A porta do RDP está configurada corretamente (porta 3389 padrão):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Ao implantar uma VM, as regras padrão são criadas em relação à porta 3389. Se desejar alterar o número da porta, faça isso depois que a VM for implantada no Azure.

3. O ouvinte escuta em todos os adaptadores de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Configure o modo de Autenticação no Nível da Rede para as conexões RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Defina o valor de keep alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Reconecte:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Limite o número de conexões simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Se houver certificados autoassinados vinculados ao ouvinte do RDP, remova-os:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Isso serve para garantir que você pode se conectar no começo, durante a implantação da VM. Você também poderá examinar isso em um estágio posterior depois que a VM for implantada no Azure, se necessário.

9. Se a VM for fazer parte de um Domínio, verifique todas as configurações a seguir para garantir que as configurações anteriores não sejam revertidas. As políticas que devem ser verificadas são as seguintes:
    
    - O RDP está habilitado:

         Computer Configuration\Policies\Windows Settings\Administrative Templates\ Components\Remote Desktop Services\Remote Desktop Session Host\Connections:
         
         **Permitir que os usuários se conectem remotamente usando a Área de Trabalho Remota**

    - Política de grupo do NLA:

        Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security: 
        
        **Exigir a Autenticação de usuário para conexões remotas usando a Autenticação no Nível da Rede**
    
    - Configurações de Keep Alive:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **Configurar o intervalo de conexão keep alive**

    - Configurações de reconexão:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **Reconexão automática**

    - Limite o número de configurações de conexões:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **Limitar o número de conexões**

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
1. Ative o Firewall do Windows nos três perfis (Domínio, Padrão e Público):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Execute o seguinte comando no PowerShell para permitir o WinRM por meio dos três perfis de firewall (Domínio, Privado e Público) e habilite o serviço Remoto do PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Habilite as seguintes regras de firewall para permitir o tráfego RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Habilite a regra de Compartilhamento de Arquivo e Impressora para que a VM possa responder a um comando ping dentro da Rede Virtual:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Se a VM for fazer parte de um Domínio, verifique as configurações a seguir para garantir que as configurações anteriores não sejam revertidas. As políticas do AD que devem ser verificadas são as seguintes:

    - Habilitar os perfis do Firewall do Windows

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **Proteger todas as conexões de rede**

       Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **Proteger todas as conexões de rede**

    - Habilitar o RDP 

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **Permitir exceções de entrada da Área de Trabalho Remota**

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **Permitir exceções de entrada da Área de Trabalho Remota**

    - Habilitar o ICMP-V4

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **Permitir exceções do ICMP**

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **Permitir exceções do ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Verifique se a VM está em estado íntegro, segura e pode ser acessada com RDP 
1. Para verificar se o disco está íntegro e consistente, execute uma operação de verificação de disco na próxima reinicialização da VM:

    ```PowerShell
    Chkdsk /f
    ```
    Verifique se o relatório mostra um disco íntegro e limpo.

2. Defina as configurações de BCD (Dados de Configuração da Inicialização). 

    > [!Note]
    > Certifique-se de executar esses comandos em uma janela CMD elevada e **não** no PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Confirme se o repositório da Instrumentação de Gerenciamento do Windows é consistente. Para fazer isso, execute o seguinte comando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório estiver corrompido, consulte [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: o repositório está corrompido ou não?).

4. Verifique se outro aplicativo não está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Execute **netstat -anob** para ver quais portas estão sendo usadas na VM:

    ```PowerShell
    netstat -anob
    ```

5. Se o VHD do Windows que você deseja carregar for um controlador de domínio, siga estas etapas:

    R. Siga [estas etapas extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

    B. Verifique se você sabe a senha do DSRM, caso precise iniciar a VM no DSRM em algum momento. Talvez você deseje consultar este link para definir a [senha do DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Verifique se você sabe a conta de Administrador Interno e a senha. Talvez você deseje redefinir a senha do administrador local atual e verificar se pode usar essa conta para entrar no Windows por meio da conexão RDP. Essa permissão de acesso é controlada pelo objeto de Política de Grupo "Permitir logon pelos Serviços de Área de Trabalho Remota". Exiba esse objeto no Editor de Política de Grupo Local em:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

7. Confira as políticas de AD a seguir para ter certeza de que você não está bloqueando o acesso de RDP através de RDP e não da rede:

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar acesso a este computador pela rede

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar logon pelos Serviços de Área de Trabalho Remota


8. Reinicie a VM para garantir que o Windows ainda está íntegro e pode ser acessado usando a conexão RDP. Neste ponto, talvez você deseje criar uma VM no Hyper-V local para verificar se a VM está iniciando por completo e, em seguida, testar se ela é acessível por RDP.

9. Remova os filtros extras da Interface do Driver de Transporte, como software que analisa pacotes TCP ou firewalls extras. Você também poderá examinar isso em um estágio posterior depois que a VM for implantada no Azure, se necessário.

10. Desinstale outros softwares e drivers de terceiros relacionados a componentes físicos ou a qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
A configuração ideal é **ter o último nível de patch do computador**. Se isso não for possível, verifique se as seguintes atualizações estão instaladas:

|                       |                   |           |                                       Versão mínima do arquivo x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Componente               | Binário            | Windows 7 e Windows Server 2008 R2 | Windows 8 e Windows Server 2012             | Windows 8.1 e Windows Server 2012 R2 | Windows 10 e Windows Server 2016 RS1 | Windows 10 RS2             |
| Armazenamento                 | disk.sys          | 6.1.7601.23403 – KB3125574         | 6.2.9200.17638/6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061           | -                                    | -                          |
|                         | storport.sys      | 6.1.7601.23403 – KB3125574         | 6.2.9200.17188/6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726           | 10.0.14393.1358 – KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 – KB3125574         | 6.2.9200.17623/6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726           | 10.0.14393.1198 – KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 – KB3125574         | 6.2.9200.16384 – KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 – KB3125574         | 6.2.9200.17061/6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614           | 10.0.14393.953 – KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 – KB3125574         | 6.2.9200.17047/6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 – KB3125574         | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850           | 10.0.14393.953 – KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 – KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 – KB3125574         | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726           | 10.0.14393.1066 – KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 – KB3125574         | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726           | -                                    | -                          |
|                         | Mpio.sys          | 6.1.7601.23403 – KB3125574         | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726           | 10.0.14393.1198 – KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 – KB3125574         | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614           | 10.0.14393.576 – KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 – KB3125574         | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614           | 10.0.14393.206 – KB4022715           | -                          |
| Rede                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 – KB4022715          | 10.0.15063.250 – KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 – KB4022722         | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726           | 10.0.14393.479 – KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 – KB4022722         | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726           | 10.0.14393.953 – KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 – KB4022722         | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726           | 10.0.14393.953 – KB4022715           | 10.0.15063.0               |
|                         | tcpip.sys         | 6.1.7601.23761 – KB4022722         | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726           | 10.0.14393.1358 – KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 – KB3125574         | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726           | 10.0.14393.251 – KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 – KB4022719         | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726           | 10.0.14393.1358 – KB4022715          | 10.0.15063.138             |
| Núcleo                    | ntoskrnl.exe      | 6.1.7601.23807 – KB4022719         | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726           | 10.0.14393.1358 – KB4022715          | 10.0.15063.483             |
| Serviços da Área de Trabalho Remota | rdpcorets.dll     | 6.2.9200.21506 – KB4022719         | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726           | 10.0.14393.1198 – KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 – KB3125574         | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850           | 10.0.14393.0 – KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 – KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 – KB4022719         | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726           | 10.0.14393.594 – KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 – KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 – KB3125574         | -                                           | -                                    | -                                    | -                          |
| Segurança                | Devido ao WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Quando usar o Sysprep <a id="step23"></a>    

O Sysprep é um processo que pode ser executado em uma instalação do Windows, que redefinirá a instalação do sistema e fornecerá uma “experiência pronta para uso”, com a remoção de todos os dados pessoais e a redefinição de vários componentes. Normalmente, isso é feito se você deseja criar um modelo com base no qual você pode implantar várias outras VMs que têm uma configuração específica. Isso é chamado de uma **imagem generalizada**.

Se, em vez disso, você desejar apenas criar uma VM com base em um disco, não precisará usar o Sysprep. Nessa situação, basta criar a VM com base no que é conhecido como um **imagem especializada**.

Para obter mais informações sobre como criar uma VM com base em um disco especializado, consulte:

- [Criar uma VM com base em um disco especializado](create-vm-specialized.md)
- [Criar uma VM com base em um disco VHD](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

Se você desejar criar uma imagem generalizada, precisará executar o Sysprep. Para obter mais informações sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). 

Nem toda função ou aplicativo que é instalado em um computador baseado no Windows dá suporte a essa generalização. Portanto, antes de executar esse procedimento, consulte o artigo a seguir para verificar se há suporte para a função do computador no Sysprep. Para obter mais informações, consulte [Suporte do Sysprep em funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Etapas para generalizar um VHD

>[!NOTE]
> Após a execução do sysprep.exe, conforme especificado nas etapas a seguir, desligue a VM e não a ligue novamente até que você crie uma imagem dela no Azure.

1. Entre na VM Windows.
2. Execute o **Prompt de Comando** como administrador. 
3. Altere o diretório para **%windir%\system32\sysprep** e, em seguida, execute **sysprep.exe**.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.

    ![Ferramenta de Preparação do Sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
6. Após a conclusão do Sysprep, desligue a VM. Não use **Reiniciar** para desligar a VM.
7. Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM com base em um disco generalizado, consulte [Carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Conclua as configurações recomendadas
As seguintes configurações não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

* Instale o [Agente de VMs do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, você pode habilitar as extensões de VM. As extensões de VM implementam a maioria da funcionalidade crítica que você pode desejar usar com as VMs, como redefinição de senhas, configuração do RDP e assim por diante. Para obter mais informações, consulte:

    - [Agente de VM e extensões – parte 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agente de VM e extensões – parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* O log de Despejo pode ser útil para solucionar problemas de falha do Windows. Habilite a coleta do log de Despejo:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Se você receber erros durante uma das etapas de procedimento deste artigo, isso significa que as chaves do Registro já existem. Nessa situação, use os seguintes comandos:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Depois que a VM for criada no Azure, recomendamos colocar o arquivo de paginação no volume “Unidade temporal” para melhorar o desempenho. Configure isso da seguinte maneira:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Se houver um disco de dados anexado à VM, a letra da unidade do volume da Unidade temporal geralmente será “D”. Essa designação poderá ser diferente, dependendo do número de unidades disponíveis e das configurações feitas.

## <a name="next-steps"></a>Próximas etapas
* [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](upload-generalized-managed.md)

