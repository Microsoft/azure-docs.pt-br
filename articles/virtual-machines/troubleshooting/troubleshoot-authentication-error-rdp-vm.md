---
title: Solucionar problemas de erros de autenticação quando você usa o RDP para se conectar à VM do Azure | Microsoft Docs
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
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594918"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Solucionar problemas de erros de autenticação quando você usa o RDP para se conectar à VM do Azure

Este artigo pode ajudá-lo a solucionar problemas de erros de autenticação que ocorrem quando você usa a conexão de protocolo RDP para se conectar a uma VM (Máquina Virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Você obtém uma captura de tela de uma VM do Azure que mostra a tela de boas-vindas e indica que o sistema operacional está em execução. No entanto, quando você tenta se conectar à VM usando a Conexão de Área de Trabalho Remota, recebe uma das seguintes mensagens de erro.

### <a name="error-message-1"></a>Mensagem de erro 1

**Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada**.

### <a name="error-message-2"></a>Mensagem de erro 2

**O computador remoto com o qual você está tentando se conectar requer NLA (Autenticação no Nível da Rede), mas seu controlador de domínio do Windows não pode ser contatado para executar a NLA. Se você for um administrador no computador remoto, poderá desabilitar NLA usando as opções na guia Remoto da caixa de diálogo Propriedades do Sistema.**

### <a name="error-message-3-generic-connection-error"></a>Mensagem de erro 3 (erro de conexão genérico)

**Este computador não pode se conectar ao computador remoto. Tente se conectar novamente e, se o problema persistir, entre em contato com o proprietário do computador remoto ou o administrador da rede.**

## <a name="cause"></a>Causa

Há várias razões para a NLA impedir o acesso do RDP a uma VM.

### <a name="cause-1"></a>Causa 1

A VM não consegue se comunicar com o DC (controlador de domínio). Esse problema pode impedir que uma sessão de RDP acesse uma VM usando as credenciais de domínio. No entanto, você ainda poderá fazer logon usando as credenciais de Administrador Local. Esse problema pode ocorrer nas seguintes situações:

1. O canal de segurança do Active Directory entre essa VM e o DC é interrompido.

2. A VM tem uma cópia antiga da senha da conta e o DC tem uma cópia mais recente.

3. O DC ao qual essa VM está se conectando não está íntegro.

### <a name="cause-2"></a>Causa 2

O nível de criptografia da VM é maior do que aquele usado pelo computador cliente.

### <a name="cause-3"></a>Causa 3

Os protocolos TLS 1.0, 1.1 ou 1.2 (servidor) estão desabilitados na VM.

### <a name="cause-4"></a>Causa 4

A VM foi definida para desabilitar logon usando as credenciais de domínio e a LSA (Autoridade de Segurança Local) está configurada incorretamente.

### <a name="cause-5"></a>Causa 5

A VM foi sido configurada para aceitar apenas conexões de algoritmo em conformidade com FIPS (Federal Information Processing Standard). Geralmente, isso é feito por meio da política do Active Directory. Essa é uma configuração rara, mas o padrão FIPS pode ser imposto apenas a conexões de Área de Trabalho Remota.

## <a name="before-you-troubleshoot"></a>Antes de solucionar problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar um instantâneo de backup, siga as etapas em [Criar instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Conectar-se à VM remotamente

Para se conectar à VM remotamente, use um dos métodos em [Como usar ferramentas remotas para solucionar problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Serviço de cliente de política de grupo

Quando se trata de uma VM ingressada no domínio, primeiro interrompa o serviço de cliente de Política de Grupo para impedir que qualquer Política do Active Directory substitua as alterações. Para fazer isso, execute o seguinte comando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Depois que o problema for corrigido, restaure a capacidade dessa VM de entrar em contato com o domínio para recuperar o GPO mais recente do domínio. Para fazer isso, execute os seguintes comandos:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Se a alteração for revertida, isso que uma política do Active Directory está causando o problema. 

### <a name="workaround"></a>Solução alternativa

Para contornar esse problema, execute os seguintes comandos na janela Comando para desabilitar a NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Em seguida, reinicie a VM.

Para habilitar novamente a NLA, execute o seguinte comando e reinicie a VM:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>solução de problemas

### <a name="for-domain-joined-vms"></a>Para VMs ingressadas em domínio

Para solucionar esse problema, verifique primeiro se a VM pode se conectar a um DC e se o DC tem um status "íntegro" e pode lidar com solicitações da VM.

>[!Note] 
>Para testar a integridade do DC, use outra VM na mesma VNET e sub-rede que compartilham o mesmo servidor de logon.

Conecte-se à VM que tem o problema usando o Console serial, CMD remoto ou PowerShell remoto, conforme as etapas na seção "Conectar-se à VM remotamente".

Para determinar a qual DC a VM está se conectando, execute o seguinte comando no console: 

```cmd
set | find /i "LOGONSERVER"
```

Em seguida, verifique a integridade do canal seguro entre a VM e o DC. Para fazer isso, execute o seguinte comando em uma instância do PowerShell com privilégios elevados. Esse comando retorna um sinalizador booliano que indica se o canal seguro está ativo:

```powershell
Test-ComputerSecureChannel -verbose
```

Se o canal for interrompido, execute o seguinte comando para repará-lo:

```powershell
Test-ComputerSecureChannel -repair
```

Verifique se a senha da conta de computador no Active Directory está atualizada na VM e no DC:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Se a comunicação entre o DC e a VM for boa, mas o DC não estiver íntegro o suficiente para abrir uma sessão do RDP, você poderá tentar reiniciar o controlador de domínio.

Se os comandos anteriores não consertar o problema de comunicação com o domínio, você poderá reingressar essa VM no domínio. Para fazer isso, siga estas etapas:

1. Crie um script chamado Unjoin.ps1 usando o seguinte conteúdo e, em seguida, implante o script como uma Extensão de Script Personalizado no portal do Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Esse script força a retirada da VM do domínio e reinicia-a depois de 10 segundos. Em seguida, você precisará limpar o objeto de computador no lado do domínio.

2.  Após a limpeza, reingresse essa VM no domínio. Para fazer isso, crie um script chamado JoinDomain.ps1 usando o seguinte conteúdo e, em seguida, implante o script como uma Extensão de Script Personalizado no portal do Azure: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Isso adiciona a VM ao domínio usando as credenciais especificadas.

Se o canal do Active Directory estiver íntegro, a senha do computador estiver atualizada e o controlador de domínio estiver funcionando conforme o esperado, tente as etapas a seguir.

Se o problema persistir, verifique se a credencial de domínio está desabilitada. Para fazer isso, abra uma janela de prompt de comandos com privilégios elevados e, em seguida, execute o seguinte comando para determinar se a VM está configurada para desabilitar contas de domínio para fazer logon na VM:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Se a chave está definida como **1**, isso significa que o servidor foi definido para não permitir credenciais de domínio. Altere essa chave para **0**.

### <a name="for-standalone-vms"></a>Para VMs autônomas

#### <a name="check-minencryptionlevel"></a>Verificar MinEncryptionLevel

Em uma instância CMD, execute o seguinte comando para consultar o valor do Registro **MinEncryptionLevel**:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Com base no valor do Registro, siga estas etapas:

* 4 (FIPS): Acesse [Verificar conexões de algoritmos compatíveis com FIPs](#fips-compliant).

* 3 (criptografia de 128 bits): Configure a gravidade para **2**, executando o comando a seguir:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Maior criptografia possível, conforme determinado pelo cliente): Você pode tentar definir a criptografia para o valor mínimo de **1**, executando o comando a seguir:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Reinicie a VM para que as alterações no Registro entrem em vigor.

#### <a name="tls-version"></a>Versão do TLS

Dependendo do sistema, o RDP usa o protocolo TLS 1.0, 1.1 ou 1.2 (servidor). Para consultar como esses protocolos são configurados na VM, abra uma instância CMD e, em seguida, execute os seguintes comandos:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Se os valores retornados não forem todos **1**, isso significa que o protocolo está desabilitado. Para habilitar esses protocolos, execute os seguintes comandos:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Para outras versões do protocolo, você pode executar os comandos a seguir:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Obtenha o SSH/TLS versão x.x dos Logs do SO convidado nos erros de SCHANNEL.

#### <a name="fips-compliant"></a> Verificar conexões de algoritmos em conformidade com FIPs

A área de trabalho remota pode ser imposta para usar somente conexões de algoritmo em conformidade com FIPs. Isso pode ser definido usando uma chave do Registro. Para fazer isso, abra uma janela de prompt de comandos com privilégios elevados e consulte as chaves a seguir:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Se o comando retornar **1**, altere o valor do Registro para **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Verifique qual é o MinEncryptionLevel atual na VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Se o comando retornar **4**, altere o valor do Registro para **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Reinicie a VM para que as alterações no Registro entrem em vigor.

## <a name="next-steps"></a>Próximas etapas

[Método SetEncryptionLevel da classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Configurar Autenticação de Servidor e Níveis de Criptografia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
