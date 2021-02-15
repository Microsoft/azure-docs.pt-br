---
title: Não é possível conectar-se remotamente às máquinas virtuais do Azure porque o DHCP está desabilitado|Microsoft Docs
description: Saiba como solucionar problemas de RDP causados pelo serviço de cliente DHCP está desabilitado no Microsoft Azure. Microsoft Docs
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
ms.openlocfilehash: 945f8896a844e7a73107df44d03abc7290f4e3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999122"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Não é possível RDP para máquinas virtuais do Azure porque o serviço de cliente DHCP está desativado

Este artigo descreve um problema em que você não pode área de trabalho remota para Máquinas Virtuais do Windows do Azure (VMs) depois que o serviço de cliente DHCP é desativado na máquina virtual.


## <a name="symptoms"></a>Sintomas
Você não pode tornar uma conexão RDP uma VM no Azure porque o serviço Cliente DHCP está desabilitado na VM. Quando você verifica a captura de tela no [Diagnóstico de inicialização](../troubleshooting/boot-diagnostics.md) no portal do Azure, a VM é inicializada normalmente e aguarda as credenciais na tela de login. Você visualiza remotamente os logs de eventos na VM usando o Visualizador de Eventos. Você vê que o serviço de cliente DHCP não está iniciado ou falha ao iniciar. A seguir um exemplo de log:

**Nome de log**: sistema </br>
**Origem**: Gerenciador de controle de serviço </br>
**Data**: 12/16/2015 11:19:36 AM </br>
**ID de evento**: 7022 </br>
**Categoria de tarefa**: nenhum </br>
**Nível**: erro </br>
**Palavras-chave**: Clássico</br>
**Usuário**: N/A </br>
**Computador**: myvm.cosotos.com</br>
**Descrição**: parou ao iniciar o serviço do cliente DHCP.</br>

Para VMs do Resource Manager, você pode usar o recurso Serial Access Console para consultar os logs de eventos 7022 usando o seguinte comando:

```console
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

Para VMs clássicas, você precisará trabalhar no modo OFFLINE e coletar os logs manualmente.

## <a name="cause"></a>Causa

O serviço de cliente DHCP não está sendo executado na VM.

> [!NOTE]
> Este artigo se aplica somente ao serviço de cliente DHCP e não ao servidor DHCP.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver esse problema, use o controle serial para ativar o DHCP ou [redefinir a interface de rede](reset-network-interface.md) para a VM.

### <a name="use-serial-control"></a>Usar o Controle serial

1. Conecte-se ao [Console Serial e abra a instância CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Se o Console serial não estiver habilitado em sua VM, consulte [Redefinir a interface de rede](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface de rede:

    ```console
    sc query DHCP
    ```

3. Se o DHCP é interrompido, tente iniciar o serviço

    ```console
    sc start DHCP
    ```

4. Consulte o serviço novamente para certificar-se de que o serviço é iniciado com êxito.

    ```console
    sc query DHCP
    ```

    Tente se conectar à VM e veja se o problema foi resolvido.
5. Se o serviço não iniciar, use a seguinte solução apropriada, com base na mensagem de erro recebida:

    | Erro  |  Solução |
    |---|---|
    | 5 - ACESSO NEGADO  | Ver [serviço de cliente DHCP é interrompido devido a um erro de acesso negado](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Ver [serviço cliente DHCP falhar ou travar](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Ver [o serviço cliente DHCP está desabilitado](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.   |
    | 1067 - ERROR_PROCESS_ABORTED |Ver [serviço cliente DHCP falhar ou travar](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |-1069 ERROR_SERVICE_LOGON_FAILED   |  Consulte [serviço cliente DHCP falha devido a falha de logon](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Ver [serviço cliente DHCP falhar ou travar](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Ver [o serviço cliente DHCP está desabilitado](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |1053 | [Contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Serviço de cliente DHCP é interrompido devido a um erro de acesso negado

1. Conecte-se ao [Console serial](serial-console-windows.md) e abra uma instância do PowerShell.
2. Faça o download da ferramenta Monitor de Processos executando o seguinte script:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Agora inicie um rastreamento **procmon**:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduza o problema iniciando o serviço que está gerando a mensagem **Access Denied**:

   ```
   sc start DHCP
   ```

   Quando falhar, encerre o rastreio do Process Monitor:

   ```
   procmon /Terminate
   ```
5. Coletar o **c:\temp\ProcMonTrace.PML** arquivo:

    1. [Anexar um disco de dados à VM](../windows/attach-managed-disk-portal.md
).
    2. Use o Console Serial, você pode copiar o arquivo para a nova unidade. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Nesse comando, F é a letra do driver do disco de dados anexado. Substitua a letra conforme apropriado pelo valor correto.
    3. Desanexe a unidade de dados e conecte-a à uma VM funcional que tenha o ubstakke do Process Monitor instalado.

6. Abra **ProcMonTrace.PML** usando o Process Monitor na VM funcional. Em seguida, filtre por  **O resultado é ACCESS DENIED**, conforme mostrado na seguinte imagem:

    ![Filtrar por resultado no Process Monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Corrigir as chaves do registro, pastas ou arquivos que estão na saída. Normalmente, esse problema é causado quando a conta de login usada no serviço não tem permissão da ACL para acessar esses objetos. Para determinar a permissão correta da ACL para a conta de entrada, você pode verificar uma VM saudável.

#### <a name="dhcp-client-service-is-disabled"></a>O serviço de cliente DHCP está desativado

1. Restaure o serviço para seu valor de inicialização padrão:

   ```
   sc config DHCP start= auto
   ```

2. Inicie o serviço:

   ```
   sc start DHCP
   ```

3. Consulte o status do serviço novamente para garantir que ele esteja em execução:

   ```
   sc query DHCP
   ```

4. Tente se conectar à VM usando a Área de Trabalho Remota.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Serviço de cliente DHCP falha devido a falha de logon

1. Como esse problema ocorre se a conta de inicialização desse serviço foi alterada, reverta a conta para seu status padrão:

    ```console
    sc config DHCP obj= 'NT Authority\Localservice'
    ```

2. Inicie o serviço:

    ```console
    sc start DHCP
    ```

3. Tente se conectar à VM usando a Área de Trabalho Remota.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Serviço de cliente DHCP falha ou trava

1. Se o status do serviço estiver parado no estado **Iniciando** ou **Parando**, tente parar o serviço:

    ```console
    sc stop DHCP
    ```

2. Isole o serviço em seu próprio contêiner "svchost":

    ```console
    sc config DHCP type= own
    ```

3. Inicie o serviço:

    ```console
    sc start DHCP
    ```

4. Se o serviço não iniciar, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. [Anexe o disco do sistema operacional a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma conexão de área de trabalho remota para a VM de recuperação. Certifique-se de que o disco conectado esteja sinalizado como **Online** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do SO anexado.
3.  Abra uma instância de prompt de comando com privilégios elevados (**Executar como administrador**). Em seguida, execute o script a seguir. Esse script pressupõe que a letra da unidade atribuída ao disco do sistema operacional anexado é **F**. Substitua a letra conforme apropriado com o valor em sua VM.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Desanexe o disco do SO e recrie a VM](./troubleshoot-recovery-disks-portal-windows.md). Em seguida, verifique se o problema foi resolvido.

## <a name="next-steps"></a>Próximas etapas

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema.
