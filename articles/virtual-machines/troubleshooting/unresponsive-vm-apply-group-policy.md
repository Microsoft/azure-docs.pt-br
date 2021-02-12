---
title: A máquina virtual do Azure não está respondendo ao aplicar a política
description: Este artigo fornece etapas para resolver problemas em que a tela de carga não responde ao aplicar uma política durante a inicialização em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 7160ec9564ede21eab0a205b2d66a7d566639506
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632649"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>A VM não está respondendo ao aplicar Política de Grupo política de grupos e usuários locais

Este artigo fornece etapas para resolver problemas em que a tela de carga não responde quando uma VM (máquina virtual) do Azure aplica uma política durante a inicialização.

## <a name="symptoms"></a>Sintomas

Quando você estiver usando o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir uma captura de tela da VM, ela ficará presa no carregamento com a mensagem: "aplicando política de grupo política de grupos e usuários locais".

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Captura da tela de carregamento “Aplicando política de grupos e usuários locais da política de grupo” (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Captura da tela de carregamento “Aplicando política de grupos e usuários locais da política de grupo” (Windows Server 2012).":::

## <a name="cause"></a>Causa

Há bloqueios conflitantes quando a política tenta limpar perfis de usuário antigos.

> [!NOTE]
> Aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Veja a política com problema:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. [Criar e acessar uma VM de reparo](#step-1-create-and-access-a-repair-vm)
1. [Desabilitar a política](#step-2-disable-the-policy)
1. [Habilitar o console serial e a coleção de despejo de memória](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Recompilar a VM](#step-4-rebuild-the-vm)

> [!NOTE]
> Se você encontrar esse erro de inicialização, o SO convidado não estará operacional. Você precisa solucionar problemas no modo offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Etapa 1: Criar e acessar uma VM de reparo

1. Use as [etapas de 1a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar uma VM de reparo.
2. Use Conexão de Área de Trabalho Remota para se conectar à VM de reparo.

### <a name="step-2-disable-the-policy"></a>Etapa 2: Desabilitar a política

1. Na VM de reparo, abra o Editor do Registro.
1. Localize a chave **HKEY_LOCAL_MACHINE** e selecione **arquivo**  >  **Carregar Hive** no menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="A captura de tela mostra o HKEY_LOCAL_MACHINE realçado e o menu que contém Carregar Hive.":::

    - Você pode usar o Load Hive para carregar chaves do registro de um sistema offline. Nesse caso, o sistema é o disco danificado anexado à VM de reparo.
    - As configurações de todo o sistema são armazenadas em `HKEY_LOCAL_MACHINE` e podem ser abreviadas como "HKLM".
1. No disco anexado, acesse o arquivo `\windows\system32\config\SOFTWARE` e abra-o.

    1. Quando for solicitado um nome, insira BROKENSOFTWARE.
    1. Para verificar se BROKENSOFTWARE foi carregado, expanda **HKEY_LOCAL_MACHINE** e procure a chave BROKENSOFTWARE adicionada.
1. Vá para BROKENSOFTWARE e verifique se a chave CleanupProfile existe no hive carregado.

    1. Se a chave existir, a política CleanupProfile será definida. Seu valor representa a política de retenção medida em dias. Prossiga para excluir a chave.
    1. Se a chave não existir, a política CleanupProfile não está definida. [Envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), incluindo o arquivo memory.dmp que está no diretório do Windows do disco do sistema operacional anexado.

1. Exclua a chave CleanupProfiles usando este comando:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  Descarregue o hive BROKENSOFTWARE usando este comando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Etapa 3: habilitar o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute este script:

1. Abra uma sessão de prompt de comandos com privilégios elevados. (Executar como administrador.)
1. Execute estes comandos para habilitar o console serial:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Verifique se o espaço livre no disco do sistema operacional é pelo menos igual ao RAM (tamanho da memória) da VM.

    Se não houver espaço suficiente no disco do sistema operacional, altere o local do despejo de memória e refira-o a um disco de dados anexado com espaço livre suficiente. Para alterar o local, substitua "% SystemRoot%" pela letra da unidade (por exemplo, "F:") do disco de dados nos comandos a seguir.

    **Configuração sugerida para habilitar o despejo do sistema operacional**

    Carregar disco do so quebrado:

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

### <a name="step-4-rebuild-the-vm"></a>Etapa 4: Recompilar a VM

Use [a etapa 5 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM.

Se o problema for corrigido, a política agora será desabilitada localmente. Para uma solução permanente, não use a política CleanupProfiles em VMs. Use um método diferente para executar limpezas de perfil.

Não use esta política:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas ao aplicar Windows Update, consulte [a VM não está respondendo com o erro "C01A001D" ao aplicar Windows Update](./unresponsive-vm-apply-windows-update.md).
