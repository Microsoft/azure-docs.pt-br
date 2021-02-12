---
title: A máquina virtual do Windows não pode ser inicializada devido ao Gerenciador de inicialização do Windows
description: Este artigo fornece etapas para resolver problemas em que o Gerenciador de inicialização do Windows impede a inicialização de uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5f83f4871d5cde23194ff51a90a22031b526cf91
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632556"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>A VM do Windows não pode ser inicializada devido ao Gerenciador de inicialização do Windows

Este artigo fornece etapas para resolver problemas em que o Gerenciador de inicialização do Windows impede a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

A VM está presa aguardando um prompt do usuário e não é inicializada, a menos que seja instruído manualmente.

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe o Gerenciador de inicialização do Windows com a mensagem *escolha um sistema operacional para iniciar ou pressione TAB para selecionar uma ferramenta:*.

A figura 1
 
![Gerenciador de inicialização do Windows informando "escolha um sistema operacional para iniciar ou pressione TAB para selecionar uma ferramenta:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

O erro ocorre devido a um sinalizador BCD *DISPLAYBOOTMENU* no Gerenciador de inicialização do Windows. Quando o sinalizador está habilitado, o Gerenciador de inicialização do Windows solicita o usuário, durante o processo de inicialização, para selecionar qual carregador deseja executar, causando um atraso de inicialização. No Azure, esse recurso pode ser adicionado ao tempo necessário para inicializar uma VM.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Visão geral do processo:

1. Configure o para um tempo de inicialização mais rápido usando o console serial.
2. Criar e acessar uma VM de reparo.
3. Configure o tempo de inicialização mais rápido em uma VM de reparo.
4. **Recomendado**: antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados.
5. Recompilar a VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configurar para tempo de inicialização mais rápido usando o console serial

Se você tiver acesso ao console serial, há duas maneiras de alcançar tempos de inicialização mais rápidos. Diminua o tempo de espera *DISPLAYBOOTMENU* ou remova o sinalizador completamente.

1. Siga as instruções para acessar o [console serial do Azure para Windows](./serial-console-windows.md) para obter acesso ao console baseado em texto.

   > [!NOTE]
   > Se não for possível acessar o console serial, pule para [criar e acessar uma VM de reparo](#create-and-access-a-repair-vm).

2. **Opção A**: reduzir o tempo de espera

   a. O tempo de espera é definido como 30 segundos por padrão, mas pode ser alterado para um tempo mais rápido (por exemplo, 5 segundos).

   b. Use o seguinte comando no console serial para ajustar o valor de tempo limite:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opção B**: remover o sinalizador BCD

   a. Para evitar que o prompt de menu de inicialização de vídeo seja totalmente inserido, digite o seguinte comando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se não for possível usar o console serial para configurar um tempo de inicialização mais rápido nas etapas acima, você poderá continuar com as etapas a seguir. Agora você estará Solucionando problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Use Conexão de Área de Trabalho Remota conectar-se à VM de reparo.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configurar o tempo de inicialização mais rápido em uma VM de reparo

1. Abra um prompt de comando com privilégios elevados.
2. Insira o seguinte para habilitar DisplayBootMenu:

   Use este comando para **VMs de geração 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Use este comando para **VMs de geração 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Substitua os símbolos maiores ou menores que o texto dentro deles, por exemplo, "< texto aqui >".

3. Altere o valor de tempo limite para 5 segundos:

   Use este comando para **VMs de geração 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Use este comando para **VMs de geração 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Substitua os símbolos maiores ou menores que o texto dentro deles, por exemplo, "< texto aqui >".

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
