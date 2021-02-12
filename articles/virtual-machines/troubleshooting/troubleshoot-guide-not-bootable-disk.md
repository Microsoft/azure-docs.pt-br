---
title: Erro de inicialização – "este não é um disco inicializável"
description: Este artigo fornece etapas para resolver problemas em que o disco não é inicializável em uma máquina virtual do Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 14da41815e177ece64c72ac27a7cb126e69fdc62
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633181"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Erro de inicialização – este não é um disco inicializável

Este artigo fornece etapas para resolver problemas em que o disco não é inicializável em uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe um prompt com a mensagem "este não é um disco inicializável. Insira um disquete inicializável e pressione qualquer tecla para tentar novamente... '.

   A figura 1

   ![A Figura 1 mostra a mensagem * "este não é um disco inicializável. Insira um disquete inicializável e pressione qualquer tecla para tentar novamente... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Essa mensagem de erro significa que o processo de inicialização do sistema operacional não pôde localizar uma partição de sistema ativa. Esse erro também pode significar que há uma referência ausente no repositório Dados de Configuração da Inicialização (BCD), impedindo que ele localize a partição do Windows.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
2. Defina status da partição como ativo.
3. Corrija a partição de disco.
4. **Recomendado**: antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados.
5. Reconstrua a VM original.

   > [!NOTE]
   > Ao encontrar esse erro de inicialização, o SO convidado não está operacional. Você deverá solucionar esse problema no modo offline.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="set-partition-status-to-active"></a>Definir status da partição como ativo

As VMs de geração 1 devem primeiro verificar se a partição do sistema operacional, que contém o repositório BCD, está marcada como *ativa*. Se você tiver uma VM de geração 2, pule para [corrigir a partição de disco](#fix-the-disk-partition), pois o sinalizador de *status* foi preterido na geração posterior.

1. Abra um prompt de comando com privilégios elevados *(cmd.exe)*.
2. Insira *diskpart* para iniciar a ferramenta DISKPART.
3. Insira o *disco de lista* para listar os discos no sistema e identificar o VHD do sistema operacional anexado.
4. Depois que o VHD do sistema operacional anexado for localizado, digite *sel disk #* para selecionar o disco.  Consulte a Figura 2, em que Disk 1 é o VHD do sistema operacional anexado.

   Figura 2

   ![A Figura 2 mostra a janela * DISKPART * mostrando a saída do comando listar disco, disco 0 e disco 1 exibidos na tabela.  Também mostra a saída do comando do SEL Disk 1, disco 1 é o disco selecionado](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Depois que o disco for selecionado, insira *list partition* para listar as partições do disco selecionado.
6. Depois que a partição de inicialização for identificada, insira *sel partition #* para selecionar a partição.  Normalmente, a partição de inicialização terá cerca de 350 MB de tamanho.  Consulte a Figura 3, em que a partição 1 é a partição de inicialização.

   Figura 3

   ![A Figura 3 mostra a janela * DISKPART * com a saída do comando * list Partition *. A partição 1 e a partição 2 são exibidas na tabela. Ele também mostra a saída do comando * SEL Partition 1 *, quando a partição 1 é o disco selecionado.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Insira ' partição de detalhes ' para verificar o status da partição. Consulte a Figura 4, em que a partição está *ativa: não* ou a Figura 5, em que a partição é ' ativa: Sim '.

   Figura 4

   ![A Figura 4 mostra a janela * DISKPART * com a saída do comando * Retail Partition *, quando a partição 1 é definida como * ativa: não *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![A Figura 5 mostra a janela * DISKPART * com a saída do comando * Retail Partition *, quando a partição 1 é definida como * ativa: Sim *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se a partição **não estiver ativa**, digite *ativo* para alterar o sinalizador *ativo* .
9. Verifique se a alteração de status foi feita corretamente digitando *partição de detalhes*.

   Figura 6

   ![A Figura 6 mostra a janela do DiskPart com a saída do comando * partição de detalhes *, quando a partição 1 é definida como * ativa: Sim *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Insira *exit* para fechar a ferramenta DISKPART e salvar as alterações de configuração.

### <a name="fix-the-disk-partition"></a>Corrigir a partição de disco

1. Abra um prompt de comandos com privilégios elevados (cmd.exe).
2. Use o comando a seguir para executar *chkdsk* nos discos e corrigir os erros:

   `chkdsk <DRIVE LETTER>: /f`

   Adicionar a opção de comando '/f ' corrigirá todos os erros no disco. Certifique-se de substituir <DRIVE LETTER> pela letra do VHD do sistema operacional anexado.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados (Executar como administrador).
2. Execute os seguintes comandos:

   Habilitar console serial

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
