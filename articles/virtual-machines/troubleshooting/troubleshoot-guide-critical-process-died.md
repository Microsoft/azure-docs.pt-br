---
title: Erro de parada do Windows-
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 8279aee4a0df8155b23e6f984d259bf92555dc95
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632826"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Erro de parada do Windows-#0x000000EF "processo crítico morreu"

Este artigo fornece etapas para resolver problemas em que um processo crítico é desativado durante a inicialização em uma VM do Azure.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe o erro *#0x000000EF* com o *processo crítico* da mensagem morreu.

!["Seu computador teve um problema e precisa ser reiniciado. Estamos apenas coletando algumas informações sobre o erro e, em seguida, você pode reiniciá-lo. (# #% concluído) Se você quiser saber mais, poderá pesquisar online mais tarde para este erro: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

Normalmente, isso ocorre devido a um processo de sistema crítico falhar durante a inicialização. Você pode ler mais sobre problemas críticos do processo em "[verificação de bugs 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
2. Corrija qualquer dano do sistema operacional.
3. **Recomendado**: antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados.
4. Recompilar a VM.

> [!NOTE]
> Ao encontrar esse erro de inicialização, o SO convidado não está operacional. Você estará Solucionando problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="fix-any-os-corruption"></a>Corrigir qualquer dano do sistema operacional

1. Abra um prompt de comando com privilégios elevados.
2. Execute o seguinte comando do verificador de arquivos do sistema (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Onde < unidade de disco de inicialização > é o volume de inicialização da VM de reparo (normalmente "C:") e < unidade de disco quebrada > será a letra da unidade do disco anexado da VM quebrada. Substitua os símbolos maior que/menor que, bem como o texto contido neles, por exemplo, "< texto aqui >", com a letra apropriada.

3. Em seguida, use [a etapa 5 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM e ver se ela é inicializada.
4. Se a VM ainda não estiver sendo inicializada, continue a coletar o arquivo de despejo de memória.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Se o problema persistir após a execução do SFC, a análise de um arquivo de despejo de memória será necessária para determinar a causa do problema. Para coletar o arquivo de despejo de memória, siga estas etapas:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma nova VM de reparo.
2. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

3. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for *F*, você precisará ir para *F:\Windows*.
4. Localize o arquivo *Memory. dmp* e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.

   > [!NOTE]
   > Se você não encontrar o arquivo de despejo, conclua as etapas abaixo para habilitar a coleta de despejo de memória e o console serial, depois retorne a esta seção e repita as etapas na tarefa acima para coletar o arquivo de despejo de memória.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados (Executar como administrador).
2. Execute os seguintes comandos:

   Habilitar console serial

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Substitua os símbolos maiores ou menores que o texto dentro deles, por exemplo, "< texto aqui >".

3. Verifique se o espaço livre no disco do sistema operacional é tão grande quanto o tamanho da memória (RAM) na VM.

Se não houver espaço suficiente no disco do sistema operacional, você deverá alterar o local em que o arquivo de despejo de memória será criado e referir-se a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua "% SystemRoot%" pela letra da unidade (por exemplo, "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para habilitar o despejo do sistema operacional

**Carregar disco do sistema operacional danificado**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Habilitar em ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Habilitar em ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Descarregar disco do sistema operacional danificado:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Recriar a VM original

Use a [etapa 5 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM.
