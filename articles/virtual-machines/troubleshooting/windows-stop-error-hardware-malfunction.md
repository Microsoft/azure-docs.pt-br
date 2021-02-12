---
title: Erro de parada do Windows – Problema de hardware
description: Este artigo fornece etapas para resolver problemas em que as máquinas virtuais do Windows Server 2008 falham com uma mensagem de erro afirmando que houve um mau funcionamento do hardware.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 89faa5b29e0a972f31ad51a7354635a53176541a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661350"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Erro de parada do Windows – Problema de hardware

Este artigo fornece etapas para resolver problemas em que as máquinas virtuais do Windows Server 2008 falham com uma mensagem de erro afirmando que houve um mau funcionamento do hardware.

## <a name="symptoms"></a>Sintomas

Quando você usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, verá que a captura de telas exibirá um ecrã azul com a mensagem:

**\*\*\* Mau funcionamento do hardware**

**Ligue para o seu fornecedor para obter suporte**

**\*\*\* O sistema foi interrompido \*\*\***

#### <a name="blue-screen"></a>Tela azul

![A captura de tela mostra uma falha de mau funcionamento do hardware de ecrã azul.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Console serial

![A captura de tela mostra a mensagem "mau funcionamento do hardware" no recurso do console serial se o console serial tiver sido habilitado.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Causa

Esta tela será exibida quando o sistema operacional convidado não tiver sido configurado corretamente e uma NMI (interrupção não mascarada) tiver sido enviada. A mensagem de erro indica que um programa em modo kernel gerou uma exceção, que o manipulador não capturou. Você pode identificar qual exceção foi gerada coletando um despejo de memória.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo 

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Configurar a chave do registro de interrupção não Maskável (NMI) 
2. Criar e acessar uma VM de reparo 
3. Habilitar o console serial e a coleção de despejo de memória 
4. Recompilar a VM 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Configurar a chave do registro de interrupção não Maskável (NMI)

1. Usando o portal do Azure, reinicie a VM para que o SO convidado seja inicializado normalmente. 
2. Depois que algum acesso à VM for restaurado, abra um prompt de comando com privilégios elevados (executar como administrador). 
3. Configure a chave do registro NMI com o seguinte comando:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Exibir mais informações sobre o comando REG ADD](/windows-server/administration/windows-commands/reg-add)
4. *(Opcional)* Configurar a coleta de despejo de memória:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Opcional)* Acesso ao console serial de instalação:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Exibir mais informações sobre o comando BCDEDIT](/windows-server/administration/windows-commands/bcdedit)
6. Reinicie a VM com o seguinte comando:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Exibir informações adicionais sobre o comando de desligamento](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> O problema agora deve ser corrigido!

> [!NOTE]
> Após a reinicialização, teste sua VM para certificar-se de que ela está funcionando como normal. Se você ainda estiver enfrentando problemas, poderá continuar na próxima seção para obter mais instruções.

> [!TIP]
> É recomendável configurar a chave do registro da NMI (interrupção não maskável) na seção acima, no entanto, se a VM não for inicializada normalmente depois, as alterações pretendidas no registro do SO convidado podem não ter sido realizadas. Se esse for o caso, você poderá seguir as instruções abaixo para adicionar manualmente as configurações do registro.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as etapas 1-3 dos [Comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Conecte-se à VM de reparo usando Conexão de Área de Trabalho Remota.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

Antes de recriar a VM, é recomendável habilitar a coleta de despejo de memória e o console serial. Para fazer isso, execute o seguinte script: 

1. Abra uma sessão de prompt de comandos com privilégios elevados (executar como administrador). 
2. Liste os dados do repositório BCD e determine o identificador do carregador de inicialização que você usará na próxima etapa. 
    1. Para uma VM de geração 1, insira o seguinte comando e observe o identificador listado: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        No comando, substitua `<BOOT PARTITON>` pela letra da partição no disco anexado que contém a pasta de inicialização. 

        ![A captura de tela mostra a saída da listagem do repositório BCD em uma VM de geração 1, que lista no carregador de inicialização do Windows o número do identificador.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Para uma VM de geração 2, insira o seguinte comando e observe o identificador listado:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * No comando, substitua `<LETTER OF THE EFI SYSTEM PARTITION>` pela letra da partição do sistema EFI.
        * Pode ser útil iniciar o console de gerenciamento de disco para identificar a partição de sistema apropriada rotulada como *partição do sistema EFI*.
        * O identificador pode ser um GUID exclusivo ou pode ser o *bootmgr* padrão.
3. Execute os seguintes comandos para habilitar o console serial:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * No comando, substitua `<VOLUME LETTER WHERE THE BCD FOLDER IS>` pela letra da pasta BCD.
    * No comando, substitua `<BOOT LOADER IDENTIFIER>` pelo identificador encontrado na etapa anterior.
4. Verifique se o espaço livre no disco do sistema operacional é maior que o tamanho da memória (RAM) na VM. 
    1. Se não houver espaço suficiente no disco do sistema operacional, você deverá alterar o local em que o arquivo de despejo de memória será criado. Em vez de criar o arquivo no disco do sistema operacional, você pode consultá-lo para qualquer outro disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua **% systemroot%** pela letra da unidade (por exemplo, **F:**) do disco de dados nos comandos listados abaixo. 
    2. Insira os comandos abaixo (configuração de despejo sugerida):

    **Carregar o Hive do Registro do disco do sistema operacional danificado:**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **Habilitar em ControlSet001:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Habilitar em ControlSet002:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Descarregar disco do sistema operacional danificado:**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>Recriar a máquina virtual

* Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Solucionar erros de inicialização da máquina virtual do Azure](./boot-error-troubleshoot.md)