---
title: Solucionar problemas de conexão de RDP da VM do Azure por ID do Evento | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485311"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Solucionar problemas de conexão de RDP da VM do Azure por ID do Evento 

Este artigo explica como usar IDs de eventos para solucionar problemas que impedem uma conexão de protocolo RDP da área de trabalho remota para uma VM (Máquina Virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Você tenta usar uma sessão de protocolo RDP da Área de Trabalho Remota para se conectar a uma VM do Azure. Depois de você inserir suas credenciais, a conexão falhará e você receberá a seguinte mensagem de erro:

**Este computador não pode se conectar ao computador remoto. Tente se conectar novamente e, se o problema persistir, entre em contato com o proprietário do computador remoto ou o administrador da rede.**

Para solucionar esse problema, examine os logs de eventos na VM e, em seguida, veja os cenários a seguir.

## <a name="before-you-troubleshoot"></a>Antes de solucionar problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar um instantâneo de backup, siga as etapas em [Criar instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Conectar-se à VM remotamente

Para se conectar à VM remotamente, use um dos métodos em [Como usar ferramentas remotas para solucionar problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Cenário 1

### <a name="event-logs"></a>Logs de eventos

Em uma instância CMD, execute os seguintes comandos para verificar se o evento 1058 ou o evento 1057 foi registrado no log do sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID de evento:**      1058 <br />
**Categoria da Tarefa:** Nenhum <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Usuário:**          N/D <br />
**Computador:**      *computador* <br />
**Descrição:** O servidor Host da Sessão da Área de Trabalho Remota falhou em substituir o certificado autoassinado expirado usado para a autenticação do servidor Host da Sessão da Área de Trabalho Remota em conexões SSL. O código de status relevante era O acesso foi negado.

**Nome do log:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID de evento:**      1058 <br />
**Categoria da Tarefa:** Nenhum <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Usuário:**          N/D <br />
**Computador:**      *computador* <br />
**Descrição:** O servidor Host da Sessão da Área de Trabalho Remota não conseguiu criar um novo certificado autoassinado para ser usado para autenticação do servidor Host da Sessão da Área de Trabalho Remota em conexões SSL, o código de status relevante era O objeto já existe.

**Nome do log:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID de evento:**      1057 <br />
**Categoria da Tarefa:** Nenhum <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Usuário:**          N/D <br />
**Computador:**      *computador* <br />
**Descrição:** O servidor Host da Sessão da Área de Trabalho Remota falhou em criar um novo certificado autoassinado a ser usado para a autenticação do servidor Host da Sessão da Área de Trabalho Remota em conexões SSL. O código de status relevantes era O conjunto de chaves não existe

Você também pode verificar eventos de erro do SCHANNEL 36872 e 36870 executando os comandos a seguir:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Fonte:**        SChannel <br />
**Data:**          — <br />
**ID de evento:**      36870 <br />
**Categoria da Tarefa:** Nenhum <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Usuário:**          SYSTEM <br />
**Computador:**      *computador* <br />
**Descrição:** Ocorreu um erro fatal ao tentar acessar a chave privada de credencial do servidor SSL. O código de erro retornado pelo módulo de criptografia é 0x8009030D.  <br />
O estado de erro interno é 10001.

### <a name="cause"></a>Causa
Esse problema ocorre porque as chaves de criptografia RSA locais na pasta MachineKeys na VM não podem ser acessadas. Esse problema pode ocorrer por um dos seguintes motivos:

1. Configuração de permissões erradas na pasta Machinekeys ou os arquivos RSA.

2. Chave RSA corrompida ou ausente.

### <a name="resolution"></a>Resolução

Para solucionar esse problema, você precisa definir as permissões corretas no certificado RDP usando estas etapas.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Conceder permissão para a pasta MachineKeys

1. Crie um script usando o seguinte conteúdo:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Execute este script para redefinir as permissões da pasta MachineKey e redefinir os arquivos RSA para os valores padrão.

3.  Tente acessar a VM novamente.

Depois de executar o script, você pode verificar os seguintes arquivos que estão enfrentando problemas de permissões:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Renovar o certificado autoassinado do RDP

Se o problema persistir, execute o seguinte script para assegurar que o certificado autoassinado do RDP seja renovado:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se você não conseguir renovar o certificado, siga estas etapas para tentar excluir o certificado:

1. Em outra VM na mesma rede virtual, abra a caixa **Executar**, digite **mmc** e pressione **OK**. 

2. No menu **Arquivo**, selecione **Adicionar/Remover Snap-in**.

3. Na lista **Snap-Ins disponíveis**, selecione **Certificados** e, em seguida, selecione **Adicionar**.

4. Selecione **Conta de computador** e, em seguida, selecione **Avançar**.

5. Selecione **Outro computador** e, em seguida, adicione o endereço IP da VM que tem problemas.
   >[!Note]
   >Tente usar a rede interna para evitar o uso de um endereço IP virtual.

6. Selecione **Concluir** e, em seguida, **Aplicar**.

   ![Selecionar Computador](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expanda os certificados, vá para a pasta Remote Desktop\Certificates, o clique com o botão direito do mouse no certificado e selecione **Excluir**.

8. Reinicie o serviço de Configuração da Área de Trabalho Remota:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Neste ponto, se você atualizar o repositório do mmc, o certificado será exibido novamente. 

Tente acessar a VM usando RDP novamente.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Atualizar o certificado do protocolo SSL

Se você configurar a VM para usar um certificado SSL, execute o seguinte comando para obter a impressão digital. Em seguida, verifique se é a mesma impressão digital do certificado:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se não for, altere a impressão digital:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Você também pode tentar excluir a chave para que o RDP use um certificado autoassinado para RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Cenário 2

### <a name="event-log"></a>Log de eventos

Em uma instância CMD, execute os seguintes comandos para verificar se o evento de erro 36871 do SCHANNEL foi registrado no log do sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Fonte:**        SChannel <br />
**Data:**          — <br />
**ID de evento:**      36871 <br />
**Categoria da Tarefa:** Nenhum <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Usuário:**          SYSTEM <br />
**Computador:**      *computador* <br />
**Descrição:** Ocorreu um erro fatal ao criar uma credencial de servidor TLS. O estado de erro interno é 10013.
 
### <a name="cause"></a>Causa

Esse problema é causado por políticas de segurança. Quando versões mais antigas do TLS (por exemplo, 1.0) estão desabilitadas, o acesso ao RDP falha.

### <a name="resolution"></a>Resolução

RDP usa TLS 1.0 como protocolo padrão. No entanto, o protocolo pode ser alterado para TLS 1.1, que é o novo padrão.

Para solucionar esse problema, consulte [Solucionar problemas de erros de autenticação quando você usa o RDP para se conectar à VM do Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Cenário 3

Se você tiver instalado a função **Agente de Conexão de Área de Trabalho Remota** na VM, verifique se há o evento 2056 ou o evento 1296 nas últimas 24 horas. Em uma instância de CMD, execute os seguintes comandos: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *hora* <br />
**ID de evento:**      2056 <br />
**Categoria da Tarefa:** (109) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Usuário:**          SERVIÇO DE REDE <br />
**Computador:**      *fqdn do computador* <br />
**Descrição:** A descrição para o Evento de ID 2056 do Microsoft-Windows-TerminalServices-SessionBroker de origem não pode ser localizada. O componente que gera esse evento não está instalado no computador local ou a instalação está corrompida. Você pode instalar ou reparar o componente no computador local. <br />
Se o evento tiver sido originado em outro computador, as informações de exibição precisarão ser salvas com o evento. <br />
As informações a seguir foram incluídas com o evento: <br />
NULO <br />
NULO <br />
Falha no logon no banco de dados.

**Nome do log:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:**          *hora* <br />
**ID de evento:**      1296 <br />
**Categoria da Tarefa:** (104) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Usuário:**          SERVIÇO DE REDE <br />
**Computador:**      *fqdn do computador* <br />
**Descrição:** Não é possível localizar a descrição para o Evento de ID 1296 do Microsoft-Windows-TerminalServices-SessionBroker-Client de origem. O componente que gera esse evento não está instalado no computador local ou a instalação está corrompida. Você pode instalar ou reparar o componente no computador local.
Se o evento tiver sido originado em outro computador, as informações de exibição precisarão ser salvas com o evento.
As informações a seguir foram incluídas com o evento:  <br />
*text* <br />
*text* <br />
O Agente de Conexão de Área de Trabalho Remota não está pronto para comunicação de RPC.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do host do servidor do Agente de Conexão de Área de Trabalho Remota é alterado, o que não é uma alteração com suporte. 

O nome de host tem entradas e dependências do Banco de Dados Interno do Windows, o que é exigido para o farm de Serviço da Área de Trabalho Remota funcionar. Alterar o nome do host depois que o farm já estiver compilado causa muitos erros e pode fazer o servidor do agente parar de funcionar.

### <a name="resolution"></a>Resolução 

Para consertar esse problema, a função de Agente de Conexão de Área de Trabalho Remota e o Banco de Dados Interno do Windows devem ser reinstalados.

## <a name="next-steps"></a>Próximas etapas

[Eventos Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Visão geral técnica do SSP Schannel](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[O RDP falha com a o Evento de ID 1058 e o Evento 36870 com Comunicação SSL e Certificado de Host da Sessão da Área de Trabalho Remota](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 ou Schannel 36870 em um Controlador de Domínio](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[ID do evento 1058 – Autenticação e Criptografia de Serviços de Área de Trabalho Remota](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

