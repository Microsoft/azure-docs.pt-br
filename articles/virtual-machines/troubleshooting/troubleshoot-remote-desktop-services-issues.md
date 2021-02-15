---
title: Os serviços de área de trabalho remota não estão iniciando em uma VM do Azure | Microsoft Docs
description: Aprenda a solucionar problemas com os Serviços de Área de Trabalho Remota ao se conectar a uma máquina virtual | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508875"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Serviços de área de trabalho remota não estiver iniciando uma VM do Azure

Este artigo descreve como solucionar problemas quando você se conecta a uma máquina virtual (VM) do Azure e os Serviços de Área de Trabalho Remota, ou TermService, não estão sendo iniciados ou não são iniciados.


## <a name="symptoms"></a>Sintomas

Ao tentar conectar uma VM, você enfrenta os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando as credenciais.

    ![Captura de tela do status da VM](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Você visualiza remotamente os logs de eventos na VM usando o Visualizador de Eventos. Você vê que os Serviços de Área de Trabalho Remota, TermService, não estão sendo iniciados ou não são iniciados. O log a seguir está um exemplo:

    **Nome do log**: sistema </br>
    **Fonte**: Gerenciador de controle de serviço </br>
    **Data**: 12/16/2017 11:19:36 AM</br>
    **ID do evento**: 7022</br>
    **Categoria de tarefa**: nenhum</br>
    **Nível**: erro</br>
    **Palavras-chave**: clássico</br>
    **Usuário**: N/A</br>
    **Computador**: vm.contoso.com</br>
    **Descrição**: serviço The Remote Desktop Services parou ao iniciar. 

    Você também pode usar o recurso Serial Access Console para procurar esses erros executando a seguinte consulta: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Causa
 
Esse problema ocorre porque os Serviços de Área de Trabalho Remota não estão sendo executados na VM. A causa pode depender de cenários a seguir: 

- O serviço TermService é definido como **desabilitado**. 
- O serviço do TermService está falhando ou não respondendo. 
- O TermService não está iniciando devido a uma configuração incorreta.

## <a name="solution"></a>Solução

Para solucionar esse problema, use o Console Serial. Ou então, [repare a VM offline](#repair-the-vm-offline), anexando o disco do SO da VM a uma VM de recuperação.

### <a name="use-serial-console"></a>Use o Console Serial

1. Acesse o [Console Serial](serial-console-windows.md) selecionando **Support & Troubleshooting** > **Console serial**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

3. Alterne para o canal que executa a instância do CMD. Nesse caso, deve ser o canal 1:

   ```
   ch -si 1
   ```

4. Selecione **Enter** novamente e insira um nome de usuário e senha válidos, ID de domínio ou local, para a VM.

5. Consultar o status do serviço TermService:

   ```
   sc query TermService
   ```

6. Se o status do serviço mostrar **Parado**, tente iniciar o serviço:

    ```
    sc start TermService
     ``` 

7. Consulte o serviço novamente para garantir que o serviço seja iniciado com êxito:

   ```
   sc query TermService
   ```
8. Se o serviço não for iniciado, execute a solução baseada no erro recebido:

    |  Erro do |  Sugestão |
    |---|---|
    |5 - ACESSO NEGADO |Consulte o [Serviço TermService está parado devido a um erro de Acesso Negado](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Veja [Serviço TermService está desabilitado](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Ver [TermService serviço falhar ou travar](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.|
    |1067 - ERROR_PROCESS_ABORTED  |Ver [TermService serviço falhar ou travar](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.|
    |-1069 ERROR_SERVICE_LOGON_FAILED  |Veja [Serviço TermService falha por causa de falha de logon](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Ver [TermService serviço falhar ou travar](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Veja [Serviço TermService está desabilitado](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente. |
    |1753   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Serviço TermService está parado devido a um problema de acesso negado

1. Conecte-se ao [Console serial](serial-console-windows.md) e abra uma instância do PowerShell.
2. Baixe a ferramenta Process Monitor executando o seguinte script:

   ```
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

4. Reproduza o problema iniciando o serviço que fornece **Acesso negado**: 

   ```
   sc start TermService 
   ```

   Quando falhar, encerre o rastreio do Process Monitor:

   ```   
   procmon /Terminate 
   ```

5. Colete o arquivo **c:\temp\ProcMonTrace.PML**:

    1. [Anexar um disco de dados à VM](../windows/attach-managed-disk-portal.md
).
    2. Use o Console Serial, você pode copiar o arquivo para a nova unidade. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Nesse comando, F é a letra do driver do disco de dados anexado.
    3. Desanexe a unidade de dados e conecte-a em uma VM funcional que tenha o ubstakke do Process Monitor instalado.

6. Abra **ProcMonTrace.PML** usando o Process Monitor na VM funcional. Em seguida, filtrar por **resultado é acesso negado**, conforme mostrado na seguinte captura de tela:

    ![Filtrar por resultado no monitor de processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Corrigir as chaves do registro, pastas ou arquivos que estão na saída. Normalmente, esse problema é causado quando a conta de login usada no serviço não tem permissão da ACL para acessar esses objetos. Para saber a permissão correta da ACL para a conta de entrada, você pode verificar uma VM saudável. 

#### <a name="termservice-service-is-disabled"></a>Serviço TermService está desabilitado

1. Restaure o serviço para seu valor de inicialização padrão:

   ```
   sc config TermService start= demand 
   ```

2. Inicie o serviço:

   ```
   sc start TermService
   ```

3. Consulte seu status novamente para garantir que o serviço esteja em execução:

   ```
   sc query TermService 
   ```

4. Tente se conectar à VM usando a Área de Trabalho Remota.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Serviço TermService falha devido a falha de logon

1. Esse problema ocorre se a conta de inicialização deste serviço foi alterada. Mudou isso de volta para o padrão: 

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Inicie o serviço:

    ```console
    sc start TermService
    ```

3. Tente se conectar à VM usando a Área de Trabalho Remota.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService service crashes or hangs
1. Se o status do serviço estiver parado em **Iniciando** ou **Parando**, tente interromper o serviço: 

    ```console
    sc stop TermService
    ```

2. Isole o serviço em seu próprio contêiner "svchost":

    ```console
    sc config TermService type= own
    ```

3. Inicie o serviço:

    ```console
    sc start TermService
    ```

4. Se o serviço ainda não for iniciado, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. [Anexe o disco do sistema operacional a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma conexão de área de trabalho remota para a VM de recuperação. Certifique-se de que o disco conectado esteja sinalizado como **Online** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do SO anexado.
3. Abra uma instância de prompt de comando com privilégios elevados (**Executar como administrador**). Em seguida, execute o script a seguir. Supomos que a letra da unidade atribuída ao disco do sistema operacional anexado é **F**. Substitua-o pelo valor apropriado em sua VM. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Desanexe o disco do SO e recrie a VM](./troubleshoot-recovery-disks-portal-windows.md). Em seguida, verifique se o problema for resolvido.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema.
