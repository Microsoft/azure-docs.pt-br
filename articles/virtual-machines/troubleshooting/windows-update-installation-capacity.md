---
title: Solucionar problemas de inicialização do sistema operacional – capacidade de instalação do Windows Update
description: Etapas para resolver problemas em que Windows Update (KB) obtém um erro e não responde em uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 0c0ec45eee86031e1533b97ccf352de0ecf70e38
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633147"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Solucionar problemas de inicialização do sistema operacional – capacidade de instalação do Windows Update

Este artigo fornece etapas para resolver problemas em uma VM (máquina virtual) do Azure em que o Windows Update (KB) obtém um erro e não responde.

## <a name="symptom"></a>Sintoma

Ao usar o Diagnóstico de inicialização para exibir a captura de tela da VM, você verá que a captura de tela exibe o Windows Update (KB) em progresso, mas não o código de erro: **C01A001D**. A imagem a seguir mostra Windows Update (KB) preso com a mensagem "Erro C01A001D ao aplicar a operação de atualização ##### de ##### (######)”:

![Windows Update (KB) está preso à mensagem, “Error C01A001D ao aplicar a operação de atualização X de Y (Z)”.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Causa

Nessa situação, o sistema operacional (SO) não consegue concluir uma instalação de Windows Update (KB), pois um arquivo principal não pode ser criado no sistema de arquivos. Com base nesse código de erro, o sistema operacional não pode gravar nenhum arquivo no disco.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
1. Liberar espaço em disco.
1. Habilitar o console serial e a coleção de despejo de memória.
1. Recompilar a VM.

> [!NOTE]
> Se encontrar esse erro, o SO convidado não está funcionando. Solucione esse problema no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Use a conexão de área de trabalho remota para conectar-se à VM de reparo.

### <a name="free-up-space-on-the-disk"></a>Liberar espaço em disco

Como resolver o problema:

- Redimensione o disco até 1 TB se ele ainda não estiver no tamanho máximo de 1 TB.
- Execute uma limpeza de disco.
- Desfragmente a unidade.

1. Verifique se o disco está cheio. Se o tamanho do disco estiver abaixo de 1 TB, expanda-o para um máximo de 1 TB [usando o PowerShell](../windows/expand-os-disk.md).
1. Se o disco já for de 1 TB, será necessário executar uma limpeza de disco.
   1. Use a [ferramenta de limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço.
1. Quando o redimensionamento e a limpeza forem concluídos, desfragmente a unidade usando o seguinte comando:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Dependendo do nível de fragmentação, a desfragmentação poderá levar várias horas.

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

    **Carregar disco do sistema operacional danificado:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Habilitar em ControlSet001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Habilitar em ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Descarregar disco do sistema operacional danificado**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.
