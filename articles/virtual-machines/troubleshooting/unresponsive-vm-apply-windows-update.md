---
title: A VM do Azure não está respondendo com o erro C01A001D ao aplicar Windows Update
description: Este artigo fornece etapas para resolver problemas em que o Windows Update gera um erro e deixa de responder em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 145d58013b259bf14c26d0840b8cd4299cfe85f0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632615"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>A VM não está respondendo com o erro "C01A001D" ao aplicar Windows Update

Este artigo fornece etapas para resolver problemas em que Windows Update (KB) gera um erro e deixa de responder em uma VM do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, a Windows Update (KB) em andamento é exibida, mas falha com o código de erro: ' C01A001D '.

![Windows Update sem resposta](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Um arquivo de núcleo não pode ser criado no sistema de arquivos. O sistema operacional não pode gravar arquivos no disco.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. [Crie e acesse uma VM de reparo](#create-and-access-a-repair-vm).
2. [Libere espaço no disco rígido](#free-up-space-on-the-hard-disk).
3. [Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Reconstrua a VM](#rebuild-the-vm).

> [!NOTE]
> Quando esse erro ocorre, o SO convidado não está operacional. Você deve solucionar problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Siga [as etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Conecte-se à VM de reparo usando Conexão de Área de Trabalho Remota.

### <a name="free-up-space-on-the-hard-disk"></a>Liberar espaço no disco rígido

Se o disco ainda não for de 1 TB, você deverá redimensioná-lo. Depois que o disco for de 1 TB, execute uma limpeza de disco e uma desfragmentação da unidade.

1. Verifique se o disco está cheio. Se o disco estiver abaixo de 1 TB, [expanda-o para um máximo de 1 TB usando o PowerShell](../windows/expand-os-disk.md).
2. Depois que o disco for de 1 TB, execute uma limpeza de disco.
    - [Desanexe o disco de dados da VM quebrada](../windows/detach-disk.md).
    - [Anexe o disco de dados a uma VM em funcionamento](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Use a [ferramenta de limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço.
3. Após o redimensionamento e a limpeza, desfragmente a unidade:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Dependendo do nível de fragmentação, isso pode levar horas.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória

1. Abra uma sessão de prompt de comandos com privilégios elevados (Executar como administrador).
2. Execute os seguintes comandos:

    Habilitar console serial:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Certifique-se de que o espaço livre no disco do sistema operacional seja pelo menos igual ao tamanho da RAM (memória da VM).

    Se não houver espaço suficiente no disco do sistema operacional, altere o local em que o arquivo de despejo de memória será criado e o informe a um disco de dados anexado à VM e com espaço livre suficiente. Para alterar o local, substitua `%SystemRoot%` pela letra da unidade (por exemplo, "F:") do disco de dados nos comandos abaixo:

    **Habilitar a configuração sugerida de despejo de so:**

    Carregar disco danificado do sistema operacional:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Habilitar em ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Habilitar em ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Descarregar disco danificado do sistema operacional:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Recompilar a VM

Use [a etapa 5 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM.
