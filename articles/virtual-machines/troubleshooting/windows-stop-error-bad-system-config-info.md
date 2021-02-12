---
title: Erro de parada do Windows-informações de configuração do sistema inadequadas do 0x00000074
description: Este artigo fornece etapas para resolver problemas em que o Windows não pode inicializar e precisa ser reiniciado devido a informações de configuração de sistema incorretas em uma VM (máquina virtual) do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 8d501bcc745ef19d15564951b8c0f29f9e2678ab
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661299"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Erro de parada do Windows-informações de configuração do sistema inadequadas do 0x00000074

Este artigo fornece etapas para resolver problemas em que o Windows não pode inicializar e precisa ser reiniciado devido a informações de configuração de sistema incorretas em uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe o código de parada do Windows **#0x00000074** ou **BAD_SYSTEM_CONFIG_INFO**.

*Seu PC encontrou um problema e precisa ser reiniciado. Você pode reiniciar o.* 
 *Para obter mais informações sobre esse problema e as possíveis correções, visite http://windows.com/stopcode* 
 *Se você chamar uma pessoa de suporte, forneça estas informações:* 
 *Código de parada: BAD_SYSTEM_CONFIG_INFO*

  ![O código Stop do Windows 0x00000074, que também é mostrado como "BAD_SYSTEM_CONFIG_INFO". O Windows informa ao usuário que seu computador encontrou um problema e precisa ser reiniciado.](./media/windows-stop-error-bad-system-config-info/stop-code-0x00000074.png)

## <a name="cause"></a>Causa

O código de **BAD_SYSTEM_CONFIG_INFO** Stop ocorrerá se o hive do registro do **sistema** parecer estar corrompido. Esse erro pode ser causado por qualquer um destes motivos:

- O hive do registro não foi fechado corretamente.
- O hive do registro está corrompido.
- Faltam chaves ou valores do registro.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
1. Verifique se há danos no hive.
1. Habilitar o console serial e a coleção de despejo de memória.
1. Recompilar a VM.

   > [!NOTE]
   > Ao encontrar esse erro, o sistema operacional convidado (SO) não está operacional. Você solucionará problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Verifique se há danos no hive.
1. Use Conexão de Área de Trabalho Remota para se conectar à VM de reparo.
1. Copie a `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` pasta e salve-a em sua partição de disco íntegra ou em outro local seguro. Faça backup dessa pasta como precaução, pois você editará arquivos de registro críticos. 

   > [!NOTE]
   > Faça uma cópia da `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` pasta como um backup, caso precise reverter as alterações feitas no registro.

### <a name="check-for-hive-corruption"></a>Verificar corrupção do hive

As instruções a seguir ajudarão você a determinar se a causa foi devido à corrupção do hive ou se o hive não foi fechado corretamente. Se o hive não tiver sido fechado corretamente, você poderá desbloquear o arquivo e corrigir sua VM.

1. Na VM de reparo, abra o aplicativo **Editor do registro** . Digite "REGEDIT" na barra de pesquisa do Windows para encontrá-lo.
1. No editor do registro, selecione **HKEY_LOCAL_MACHINE** para realçá-lo e, em seguida, selecione **arquivo > Carregar Hive...**  no menu.
1. Navegue até `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` e selecione **abrir**.
1. Quando solicitado a inserir um nome, insira **BROKENSYSTEM**.

   1. Se o hive não for aberto, ou se estiver vazio, o hive será corrompido. Se o hive estiver corrompido, [abra um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

      ![Ocorre um erro informando que o editor do registro não pode carregar o hive.](./media/windows-stop-error-bad-system-config-info/cannot-load-hive-error.png)

   1. Se o hive for aberto normalmente, o hive não foi fechado corretamente. Continue na etapa 5.

1. Para corrigir um Hive que não foi fechado corretamente, realce **BROKENSYSTEM** e, em seguida, selecione **arquivo > Descarregar Hive...** para desbloquear o arquivo.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

**Recomendado**: Antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados ao executar o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados como um Administrador.
1. Execute os seguintes comandos:

   **Habilite o console serial**:
   
   ```ps
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verifique se o espaço livre no disco do sistema operacional é maior ao tamanho da memória (RAM) na VM.

   Se não houver espaço suficiente no disco do sistema operacional, altere o local no qual o arquivo de despejo de memória será criado e encaminhe esse local a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua **%SystemRoot%** pela letra da unidade, por exemplo, **F:** do disco de dados nos comandos a seguir.

   Configuração sugerida para habilitar o despejo do sistema operacional:

   **Carregar o Hive do Registro do disco do sistema operacional danificado:**

   ```ps
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Habilitar em ControlSet001:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Habilitar em ControlSet002:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Descarregar disco do sistema operacional danificado:**

   ```ps
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.