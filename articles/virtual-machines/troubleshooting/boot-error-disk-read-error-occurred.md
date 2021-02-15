---
title: Solucionar problemas de erro de inicialização – erro de leitura de disco
description: Este artigo apresenta as etapas para resolver problemas nos quais o disco não pode ser lido em uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: 75d1cf8638f922bb0275322568eb1399db4f49e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629718"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Solucionar problemas de erro de inicialização – erro de leitura de disco

Este artigo apresenta as etapas para resolver problemas nos quais o disco não pode ser lido em uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [Diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe um prompt com a mensagem: "Erro de leitura do disco. Pressione Ctrl+Alt+Del para reiniciar".

   ![Mensagem de erro: Erro de leitura do disco. Pressione Ctrl+Alt+Del para reiniciar.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Causa

Essa mensagem de erro indica que a estrutura do disco está corrompida e ilegível. Se você está usando uma VM Geração 1, também é possível que a partição do disco que contém os Dados de Configuração da Inicialização não está definida como **Ativa**.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
1. Selecionar uma Solução:
   - [Definir status da partição como Ativo](#set-partition-status-to-active)
   - [Corrigir a partição do disco](#fix-the-disk-partition)
1. Habilitar console serial e coleção de despejo de memória.
1. Recompilar a VM.

> [!NOTE]
> Se encontrar esse erro de inicialização, o SO (sistema operacional) convidado não está funcionando. Você deverá solucionar esse problema no modo offline.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as etapas 1-3 dos [Comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="set-partition-status-to-active"></a>Definir status da partição como Ativo

Nas VMs de Geração 1 deve-se verificar primeiro se a partição do sistema operacional que contém o repositório BCD está marcada como **Ativa**. Se você tiver uma VM de Geração 2, pule para [Corrigir a partição do disco](#fix-the-disk-partition), pois o Sinalizador de status foi preterido na geração posterior.

1. Abra um prompt de comandos com privilégios elevados (cmd.exe).
1. Insira **diskpart** para iniciar a ferramenta **DISKPART**.
1. Insira **list disk** para listar os discos no sistema e identificar o VHD (disco rígido virtual) anexado do sistema operacional.
1. Depois que o VHD do sistema operacional anexado for localizado, digite **sel disk #** para selecionar o disco. Confira a imagem a seguir para obter um exemplo em que o Disco 1 é o VHD anexado do sistema operacional.

   ![A janela do diskpart com a saída do comando **list disk**, em que Disco 0 e Disco 1 são exibidos na tabela. A janela também mostra a saída do comando **sel disk 1**, em que Disco 1 é o disco selecionado](./media/disk-read-error-occurred/2.png)

1. Depois que o disco for selecionado, insira **list partition** para listar as partições do disco selecionado.
1. Depois que a partição de inicialização for identificada, insira **sel partition #** para selecionar a partição. A partição de inicialização geralmente tem tamanho aproximado de 350 MB.  Veja na imagem a seguir um exemplo em que a Partição 1 é a partição de inicialização.

   ![A janela do diskpart com a saída do comando **list partition**, em que a Partição 1 e a Partição 2 são exibidas na tabela. A janela também mostra a saída do comando **sel partition 1**, em que Partição 1 é o disco selecionado.](./media/disk-read-error-occurred/3.png)

1. Insira **detail partition** para verificar o status da partição. Veja nas seguintes capturas de tela os exemplos da partição sendo definida como **Ativa: Não** ou **Ativa: Sim**.

   **Ativa: Não**

   ![A janela do diskpart com a saída do comando **detail partition**, em que a Partição 1 está definida como **Ativa: Não**.](./media/disk-read-error-occurred/4.png)

   **Ativa: Sim**

   ![A janela do diskpart com a saída do comando **detail partition**, em que a partição 1 está definida como **Ativa: Sim**.](./media/disk-read-error-occurred/5.png)

1. Se a partição não estiver definida como **Ativa**, insira **active** para alterar o sinalizador Ativo.
1. Insira **detail partition** para verificar se a alteração de status foi concluída corretamente e verifique se a saída inclui **Ativa: Sim**. 
1. Insira **exit** para fechar a ferramenta DISKPART e salvar as alterações de configuração.

### <a name="fix-the-disk-partition"></a>Corrigir a partição do disco

1. Abra um prompt de comandos com privilégios elevados (cmd.exe).
1. Use o seguinte comando para executar **CHKDSK** nos discos e executar correções de erros:

   `chkdsk <DRIVE LETTER>: /f`

   A adição da opção de comando **/f** corrigirá todos os erros no disco. Substitua a **<LETRA DA UNIDADE>** pela letra do VHD do sistema operacional anexado.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

**Recomendado**: Antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados ao executar o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados como um Administrador.
1. Execute os seguintes comandos:

   **Habilite o console serial**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verifique se o espaço livre no disco do sistema operacional é maior ao tamanho da memória (RAM) na VM.

   Se não houver espaço suficiente no disco do sistema operacional, altere o local no qual o arquivo de despejo de memória será criado e encaminhe esse local a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua **%SystemRoot%** pela letra da unidade, por exemplo, **F:** do disco de dados nos comandos a seguir.

   Configuração sugerida para habilitar o despejo do sistema operacional:

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
   
### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.
