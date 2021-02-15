---
title: A VM do Azure não está respondendo ao aplicar a política de segurança ao sistema
description: Este artigo fornece etapas para resolver problemas em que a tela de carga está presa quando a VM não está respondendo enquanto aplica a política de segurança ao sistema em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632632"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>A VM do Azure não está respondendo ao aplicar a política de segurança ao sistema

Este artigo fornece etapas para resolver problemas em que o sistema operacional trava e deixa de responder enquanto está aplicando uma política de segurança em uma VM do Azure.

## <a name="symptoms"></a>Sintomas

Quando você usar o [diagnóstico de inicialização](boot-diagnostics.md) para exibir a captura de tela da VM, verá que a captura de tela exibe o sistema operacional travado ao inicializar com a mensagem:

> ' Aplicando política de segurança ao sistema '.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Captura de tela de inicialização do Windows Server 2012 R2 travada.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Captura de tela de inicialização do sistema operacional travada.":::

## <a name="cause"></a>Causa

Há uma infinidade de possíveis causas desse problema. Você não poderá saber a origem até que uma análise de despejo de memória seja executada.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. [Criar e acessar uma VM de reparo](#create-and-access-a-repair-vm)
2. [Habilitar o console serial e a coleção de despejo de memória](#enable-serial-console-and-memory-dump-collection)
3. [Recompilar a VM](#rebuild-the-vm)
4. [Coletar o arquivo de despejo de memória](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1-3 dos comandos de reparo da VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar uma VM de reparo.
2. Use Conexão de Área de Trabalho Remota conectar-se à VM de reparo.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute este script:

1. Abra uma sessão de prompt de comandos com privilégios elevados (Executar como administrador).
2. Liste os dados do repositório BCD e determine o identificador do carregador de inicialização, que será usado na próxima etapa.

     1. Para uma VM de geração 1, insira o seguinte comando e observe o identificador listado:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        No comando, substitua \<BOOT PARTITON> pela letra da partição no disco anexado que contém a pasta de inicialização.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="O diagrama mostra a saída de listagem do repositório BCD em uma VM de geração 1, que lista no carregador de inicialização do Windows o número do identificador.":::

     2. Para uma VM de geração 2, insira o seguinte comando e observe o identificador listado:

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - No comando, substitua \<LETTER OF THE EFI SYSTEM PARTITION> pela letra da partição do sistema EFI.
        - Pode ser útil iniciar o console de gerenciamento de disco para identificar a partição de sistema apropriada rotulada como "partição do sistema EFI".
        - O identificador pode ser um GUID exclusivo ou pode ser o "Bootmgr" padrão.
3. Execute os seguintes comandos para habilitar o console serial:

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - No comando, substitua \<VOLUME LETTER WHERE THE BCD FOLDER IS> pela letra da pasta BCD.
    - No comando, substitua \<BOOT LOADER IDENTIFIER> pelo identificador encontrado na etapa anterior.
4. Verifique se o espaço livre no disco do sistema operacional é maior que o tamanho da memória (RAM) na VM.

    1. Se não houver espaço suficiente no disco do sistema operacional, você deverá alterar o local em que o arquivo de despejo de memória será criado. Em vez de criar o arquivo no disco do sistema operacional, você pode consultá-lo para qualquer outro disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua "% SystemRoot%" pela letra da unidade (por exemplo, "F:") do disco de dados nos comandos listados abaixo.
    2. Insira os comandos abaixo (configuração de despejo sugerida):

        Carregar disco danificado do sistema operacional:

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        Habilitar em ControlSet001:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Habilitar em ControlSet002:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Descarregar disco do sistema operacional danificado:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de reparo da VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, você precisaria primeiro coletar o arquivo de despejo de memória para a falha e contatar o suporte com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

1. Anexe o disco do sistema operacional a uma nova VM de reparo:

    - Use [as etapas 1-3 dos comandos de reparo da VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar uma nova VM de reparo.
    - Use Conexão de Área de Trabalho Remota conectar-se à VM de reparo.

2. Localize o arquivo de despejo e envie um tíquete de suporte:

    - Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra da unidade atribuída ao disco do SO anexado for `F`, você precisará ir até `F:\Windows`.
    - Localize o arquivo Memory. dmp e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.
    - Se você estiver tendo problemas para localizar o arquivo Memory. dmp, talvez queira usar [chamadas de NMI (interrupção não mascaráveis) no console serial](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Você pode seguir o guia para [gerar um arquivo de despejo de memória usando chamadas NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas ao aplicar a política de grupos e usuários locais, consulte a [VM não está respondendo ao aplicar política de grupo política de grupos e usuários locais](unresponsive-vm-apply-group-policy.md)