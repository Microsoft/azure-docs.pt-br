---
title: CMD e PowerShell em VMs do Windows do Azure | Microsoft Docs
description: Como usar comandos CMD e do PowerShell no SAC em VMs do Windows do Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: f286881341e527d3f01e57768cd48405c85a9a69
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710604"
---
# <a name="windows-commands---cmd-and-powershell"></a>Comandos do Windows – CMD e PowerShell

Esta seção inclui exemplos de comandos para executar tarefas comuns em cenários nos quais pode ser necessário utilizar o SAC para acessar a VM do Windows, como por exemplo, quando você precisa solucionar falhas de conexão RDP.

O SAC foi incluído em todas as versões do Windows desde o Windows Server 2003, mas é desabilitado por padrão. O SAC depende de `sacdrv.sys` driver do kernel, o `Special Administration Console Helper` serviço (`sacsvr`) e o processo `sacsess.exe`. Para obter mais informações, consulte [Ferramentas e configurações dos Serviços de Gerenciamento de Emergência](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

O SAC permite conectar-se ao SO via porta serial. Ao inicializar o CMD a partir do SAC, `sacsess.exe` inicializa `cmd.exe` no SO em execução. É possível ver que no Gerenciador de Tarefas, se o RDP para a VM for ao mesmo tempo você será conectado ao SAC via recurso de console serial. O CMD que você acessa via SAC é o mesmo `cmd.exe` usado quando conectado via RDP. Todos os mesmos comandos e ferramentas estão disponíveis, incluindo a capacidade de inicializar o PowerShell a partir dessa instância do CMD. Essa é uma grande diferença entre o SAC e o WinRE (Ambiente de Recuperação do Windows) em que o SAC permite gerenciar o SO em execução, onde o WinRE é inicializado em um SO mínimo e diferente. Embora as VMs do Azure não forneçam suporte à capacidade de acessar o WinRE, com o recurso do console serial, as VMs do Azure podem ser gerenciadas por meio do SAC.

Como o SAC está limitado a um buffer de tela de 80 x 24 sem scrollback, adicione `| more` aos comandos para exibir a saída de uma página por vez. Use `<spacebar>` para ver a próxima página ou `<enter>` para ver a próxima linha.  

`SHIFT+INSERT` é o atalho de pasta para a janela do console serial.

Por causa do buffer de tela limitado do SAC, comandos mais longos podem ser mais fáceis de serem digitados em um editor de texto local e depois colados no SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar as configurações do registro do Windows
### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A segunda chave (em \Policies) só existirá se a configuração da política de grupo relevante estiver configurada.

### <a name="enable-rdp"></a>Habilitar o RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A segunda chave (em \Policies) só seria necessária se a configuração da política de grupo relevante tivesse sido configurada. O valor será regravado na próxima atualização de política de grupo, se estiver configurado na política de grupo.

## <a name="manage-windows-services"></a>Gerenciar Serviços do Windows

### <a name="view-service-state"></a>Exibir estado do serviço
`sc query termservice`
###  <a name="view-service-logon-account"></a>Exibir conta de logon do serviço
`sc qc termservice`
### <a name="set-service-logon-account"></a>Definir conta de logon do serviço 
`sc config termservice obj= "NT Authority\NetworkService"`

Um espaço é necessário após o sinal de igual.
### <a name="set-service-start-type"></a>Definir o tipo de inicialização do serviço
`sc config termservice start= demand` 

Um espaço é necessário após o sinal de igual. Valores de inicialização possíveis incluem `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`sc config termservice depend= RPCSS`

Um espaço é necessário após o sinal de igual.
### <a name="start-service"></a>Iniciar serviço
`net start termservice`

ou

`sc start termservice`
### <a name="stop-service"></a>Parar serviço
`net stop termservice`

ou

`sc stop termservice`
## <a name="manage-networking-features"></a>Gerenciar recursos de Rede
### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Mostrar configuração do IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Habilitar NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Para obter mais informações sobre o `netsh`, [clique aqui](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

VMs do Azure sempre devem ser configuradas no SO convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa DHCP para fornecer o IP estático à VM.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Ping de porta  
Instalar o cliente Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testar a conectividade

`telnet bing.com 80`

Para remover o cliente Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Quando limitado a métodos disponíveis no Windows por padrão, o PowerShell pode ser uma abordagem melhor para testar a conectividade da porta. Veja a seção PowerShell abaixo para exemplos.
### <a name="test-dns-name-resolution"></a>Testar a resolução de nomes DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Mostrar regra de Firewall do Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Desabilitar Firewall do Windows
`netsh advfirewall set allprofiles state off`

É possível usar esse comando ao solucionar problemas para excluir temporariamente o Firewall do Windows. Ele será habilitar na próxima reinício ou quando você habilitá-la usando o comando a seguir. Não pare o serviço do Firewall do Windows (MPSSVC) ou o BFE (Mecanismo de Filtragem Base) como forma de excluir o Firewall do Windows. Parar o MPSSVC ou BFE resultará no bloqueio de toda a conectividade.
### <a name="enable-windows-firewall"></a>Habilitar Firewall do Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
### <a name="create-local-user-account"></a>Criar conta de usuário local
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Adicionar usuário local a grupo local
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Verificar se a conta do usuário está habilitada
`net user <username> | find /i "active"`

As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
### <a name="enable-user-account"></a>Habilitar conta de usuário
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Exibir propriedades da conta de usuário
`net user <username>`

Exemplo de linhas de interesse de uma conta de administrador local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Exibir grupos locais
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
### <a name="query-event-log-errors"></a>Consultar erros de log de eventos
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos correspondentes ao filtro.
### <a name="query-event-log-by-event-id"></a>Consultar log de eventos por ID do evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consultar log de eventos por ID do evento e provedor
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar log de eventos por ID do evento e provedor nas últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para voltar 7 dias em vez de 24 horas.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e dados do evento nos últimos 7 dias
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
### <a name="list-installed-applications"></a>Listar aplicativos instalados
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` classifica em ordem decrescente a data de instalação para facilitar a visualização do que foi instalado recentemente. Use `<spacebar>` para avançar para a próxima página de saída ou `<enter>` para avançar uma linha.
### <a name="uninstall-an-application"></a>Desinstalar um aplicativo
`wmic path win32_product where name="<name>" call uninstall`

Substitua `<name>` pelo nome retornado no comando acima para o aplicativo que você deseja remover.

## <a name="file-system-management"></a>Gerenciamento de sistema de arquivos
### <a name="get-file-version"></a>Obter versão do arquivo
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Este exemplo retorna a versão do arquivo do driver NIC virtual, que é netvsc.sys, netvsc63.sys ou netvsc60.syss, dependendo da versão do Windows.
### <a name="scan-for-system-file-corruption"></a>Verificar corrupção de arquivo do sistema
`sfc /scannow`

Consulte também [Reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Verificar corrupção de arquivo do sistema
`dism /online /cleanup-image /scanhealth`

Consulte também [Reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportar permissões de arquivo para o arquivo de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Salvar permissões de arquivo para arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Restaurar permissões de arquivo do arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

O caminho ao utilizar `/restore` precisa ser a pasta pai da pasta que você especificou ao utilizar `/save`. Neste exemplo, `\RSA` é o pai da pasta `\MachineKeys`especificada no exemplo `/save` acima.
### <a name="take-ntfs-ownership-of-a-folder"></a>Assumir propriedade NTFS de uma pasta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permissões NTFS para uma pasta recursivamente
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Gerenciar dispositivos
### <a name="remove-non-present-pnp-devices"></a>Remover dispositivos PNP não presentes
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Gerenciar Política de Grupo
### <a name="force-group-policy-update"></a>Forçar atualização da política de grupo
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Ferramentas diversas
### <a name="show-os-version"></a>Exibir versão do SO
`ver`

ou 

`wmic os get caption,version,buildnumber /format:list`

ou 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Exibir data de instalação do SO
`systeminfo | find /i "original"`

ou 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Exibir última hora de inicialização
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Exibir fuso horário
`systeminfo | find /i "time zone"`

ou

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Reiniciar o Windows
`shutdown /r /t 0`

Adicionar `/f` forçará o fechamento dos aplicativos em execução sem avisar os usuários.
### <a name="detect-safe-mode-boot"></a>Detectar modo de inicialização segura
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Comandos do Windows - PowerShell

Para executar o PowerShell no SAC, depois de chegar a um prompt do CMD, digite:

`powershell <enter>`

> [!CAUTION]
> Remova o módulo PSReadLine da sessão do PowerShell antes de executar qualquer outro comando do PowerShell. Há um problema conhecido em que caracteres adicionais poderão ser introduzidos no texto colado da área de transferência, se o PSReadLine estiver sendo executado em uma sessão do PowerShell no SAC.

Primeiro verifique se PSReadLine está carregado. Ele é carregado por padrão no Windows Server 2016, Windows 10 e versões mais recentes do Windows. Ele somente estará presente nas versões anteriores do Windows, se tiver sido instalado manualmente. 

Se esse comando retornar a um prompt sem saída, o módulo não foi carregado e você poderá continuar usando a sessão do PowerShell no SAC normalmente.

`get-module psreadline`

Se o comando acima retornar a versão do módulo PSReadLine, execute o comando a seguir para descarregá-lo. Esse comando não exclui nem desinstala o módulo, apenas o descarrega da sessão atual do PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar as configurações do registro do Windows
### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A segunda chave (em \Policies) só existirá se a configuração da política de grupo relevante estiver configurada.
### <a name="enable-rdp"></a>Habilitar o RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A segunda chave (em \Policies) só seria necessária se a configuração da política de grupo relevante tivesse sido configurada. O valor será regravado na próxima atualização de política de grupo, se estiver configurado na política de grupo.
## <a name="manage-windows-services"></a>Gerenciar Serviços do Windows
### <a name="view-service-details"></a>Exibir detalhes do serviço
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` pode ser usado, mas não inclui a conta de logon do serviço. `Get-WmiObject win32-service` faz.
### <a name="set-service-logon-account"></a>Definir conta de logon do serviço
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ao usar uma conta de serviço diferente de `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, ou `LocalSystem`, especifique a senha da conta como o último (oitavo) argumento após o nome da conta.
### <a name="set-service-startup-type"></a>Definir o tipo de inicialização do serviço
`set-service termservice -startuptype Manual`

`Set-service` aceita `Automatic`, `Manual` ou `Disabled` para o tipo de inicialização.
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Iniciar serviço
`start-service termservice`
### <a name="stop-service"></a>Parar serviço
`stop-service termservice`
## <a name="manage-networking-features"></a>Gerenciar recursos de Rede
### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

ou 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` está disponível em 2012+, para uso em 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Habilitar NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

ou

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` está disponível em 2012+, para uso em 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. VMs do Azure sempre devem ser configuradas no SO convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa o DHCP para fornecer o IP à VM.
### <a name="ping"></a>Ping
`test-netconnection`

ou

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sem nenhum parâmetro, tentará executar ping `internetbeacon.msedge.net`. Está disponível em 2012+. Para 2008R2 use `Get-WmiObject` como no segundo exemplo.
### <a name="port-ping"></a>Ping de porta
`test-netconnection -ComputerName bing.com -Port 80`

ou

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` está disponível em 2012+. Para 2008R2 use `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testar a resolução de nomes DNS
`resolve-dnsname bing.com` 

ou 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` está disponível em 2012+. Para 2008R2 use `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Mostrar regra de firewall do Windows por nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Mostrar regra de firewall do Windows por porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

ou

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` está disponível em 2012+. Para 2008R2, use o objeto COM `hnetcfg.fwpolicy2`. 
### <a name="disable-windows-firewall"></a>Desabilitar o firewall do Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` está disponível em 2012+. Para 2008R2 use `netsh advfirewall` como mencionado na seção CMD acima.
## <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
### <a name="create-local-user-account"></a>Criar conta de usuário local
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Verificar se a conta do usuário está habilitada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

ou 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
### <a name="add-local-user-to-local-group"></a>Adicionar usuário local a grupo local
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Habilitar conta de usuário local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
### <a name="view-user-account-properties"></a>Exibir propriedades da conta de usuário
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

ou 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Exibir grupos locais
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
### <a name="query-event-log-errors"></a>Consultar erros de log de eventos
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos correspondentes ao filtro.
### <a name="query-event-log-by-event-id"></a>Consultar log de eventos por ID do evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consultar log de eventos por ID do evento e provedor
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar log de eventos por ID do evento e provedor nas últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para voltar 7 dias em vez de 24 horas. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e dados do evento nos últimos 7 dias
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
### <a name="list-installed-software"></a>Listar software instalado
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Desinstalar software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Gerenciamento de sistema de arquivos
### <a name="get-file-version"></a>Obter versão do arquivo
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Este exemplo retorna a versão do arquivo do driver NIC virtual, que é nomeado netvsc.sys, netvsc63.sys ou netvsc60.syss, dependendo da versão do Windows.
### <a name="download-and-extract-file"></a>Baixar e extrair aplicativo
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Este exemplo cria uma pasta `c:\bin` e, em seguida, baixa e extrai o conjunto de ferramentas Sysinternals para `c:\bin`.
## <a name="miscellaneous-tasks"></a>Ferramentas diversas
### <a name="show-os-version"></a>Exibir versão do SO
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Exibir data de instalação do SO
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Exibir última hora de inicialização
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Exibir tempo de atividade do Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retorna o tempo de atividade como `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, por exemplo `49:16:48:00.00`. 
### <a name="restart-windows"></a>Reiniciar o Windows
`restart-computer`

Adicionar `-force` forçará o fechamento dos aplicativos em execução sem avisar os usuários.
## <a name="instance-metadata"></a>Metadados da instância

É possível consultar os metadados da instância do Azure na VM do Azure para exibir detalhes como osType, Local, vmSize, vmId, nome, resourceGroupName, subscriptionId, privateIpAddress e publicIpAddress.

A consulta de metadados da instância exige conectividade de rede do convidado íntegra, porque faz uma chamada REST por meio do host do Azure para o serviço de metadados da instância. Portanto, se você conseguir consultar metadados da instância, isso indica que o convidado pode comunicar-se pela rede com um serviço hospedado pelo Azure.

Para obter mais informações, consulte [Serviço de metadados da instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadados da instância
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Tipo de SO (metadados da instância)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Local (metadados da instância)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Tamanho (metadados da instância)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID da VM (metadados da instância)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nome da VM (metadados da instância)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nome do grupo de recursos (metadados da instância)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID da assinatura (metadados da instância)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Marcas (metadados da instância)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID do grupo de posicionamento (metadados da instância)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domínio de falha da plataforma (metadados da instância)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domínio de atualização da plataforma (metadados da instância)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Endereço IP privado IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Endereço IP público IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Prefixo/endereço de sub-rede IPv4 (metadados da instância)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Endereço IP IPv6 (metadados da instância)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Endereço MAC (metadados da instância)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Próximas etapas
* A página principal da documentação do console serial do Windows está localizada [aqui](serial-console-windows.md).
* O console serial também está disponível para VMs do [Linux](serial-console-linux.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).
