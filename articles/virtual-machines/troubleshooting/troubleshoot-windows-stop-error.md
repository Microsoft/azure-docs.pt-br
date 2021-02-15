---
title: Erro de parada do Windows – Status sem memória
description: Este artigo fornece etapas para resolver problemas em que o Windows não consegue iniciar e exibe o status "status sem memória".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d6e738152e542617046834980d3e7c58e497093
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664674"
---
# <a name="windows-stop-error---status-no-memory"></a>Erro de parada do Windows – Status sem memória

Este artigo fornece etapas para resolver problemas em que o Windows não consegue iniciar e exibe o status ou o código de erro #0xC0000017, também conhecido como "status sem memória".

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM (máquina virtual), você verá que a captura de tela exibe o código de erro: `0xC0000017` . Dependendo da versão do Windows que você está executando, você poderá ver esse código exibido no **Gerenciador de inicialização do Windows** ou na tela de **recuperação**.

   **Gerenciador de Inicialização do Windows**

   ![Gerenciador de inicialização do Windows informando que "falha do Windows ao iniciar. Uma alteração recente de hardware ou software pode ser a causa ". Rolando para baixo, você vê o código de status "0xC0000017", bem como informações que declaram que "o aplicativo ou o sistema operacional não pôde ser carregado porque um arquivo necessário está ausente ou contém erros".](./media/troubleshoot-windows-stop-error/1.png)

   **Tela de recuperação**
 
   ![A tela de recuperação informando que "seu PC/dispositivo precisa ser reparado. Não há memória suficiente disponível para criar um dispositivo ramdisk ". Você também deve ver o código de erro "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Causa

O disco do sistema operacional está cheio, muito fragmentado ou o sistema operacional (SO) não é capaz de acessar a memória ou o arquivo de paginação ou ambos.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo
1. Liberar espaço no disco
1. Limpar a memória inadequada do repositório BCD
1. Restaurar o arquivo de paginação para seu local padrão
1. Habilitar o console serial e a coleção de despejo de memória
1. Recompilar a VM

> [!NOTE]
> Se encontrar esse erro, o SO convidado não está funcionando. Você deverá solucionar esse problema no modo offline.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>Para VMs de geração 2, atribua uma letra à partição EFI (Extensible Firmware Interface):

Se você estiver usando uma VM de geração 2, a partição EFI do disco anexado poderá não ter uma letra atribuída a ela. Você precisará seguir as etapas abaixo para atribuir uma letra à partição antes de continuar com este guia de solução de problemas.

1. No Windows Search, insira `diskmgmt` e abra o **console de gerenciamento de disco**.
1. Identifique o disco danificado anexado à VM de reparo. Normalmente, esse disco é listado por último no console e tem o valor numérico mais alto.
1. Observação se, nesse disco, houver uma partição que contém a **partição do sistema EFI**, que também não tem um valor de letra atribuído a ela (como a unidade *F:*). Se todas as partições forem atribuídas, você poderá pular para liberar espaço no disco. Caso contrário, continue a atribuir uma carta a este disco.

   ![O console de gerenciamento de disco, com o disco anexado "disco 2", bem como a partição não atribuída que é 100 MB e é a "partição do sistema EFI".](./media/troubleshoot-windows-stop-error/3.png)

1. Abra um prompt de comando com privilégios elevados como administrador e digite `diskpart` para iniciar a ferramenta **DiskPart** .
1. Digite os seguintes comandos:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - No comando, substitua `<NUMBER OF THE ATTACHED DISK>` pelo número de disco identificado na etapa 2.
   - No comando, substitua `<NUMBER OF THE SYSTEM PARTION>` pelo número de partição da partição do sistema EFI. Essa partição ainda não foi atribuída a uma carta, mas deve ser do tipo **sistema** e ter cerca de 100 MB de tamanho.

   > [!NOTE]
   > Comparar as partições no console de gerenciamento de disco com aquelas listadas na ferramenta DISKPART pode ser útil para determinar qual número de partição corresponde à partição do sistema EFI no disco anexado.

1. Feche a janela do prompt de comando.

### <a name="free-up-space-on-the-disk"></a>Liberar espaço no disco

Agora que o disco danificado está anexado à VM de reparo, você deve verificar se o sistema operacional nesse disco tem espaço suficiente para funcionar corretamente. 

1. Verifique se o disco está cheio clicando com o botão direito do mouse na unidade do disco anexado e selecionando **Propriedades**.
1. Se o disco tiver **menos de 300 MB de espaço livre**, [expanda-o para um máximo de 1 TB usando o PowerShell](../windows/expand-os-disk.md).
1. Depois que o tamanho do disco for **1 TB**, você precisará executar uma limpeza de disco. Você pode usar a [ferramenta limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço.
1. Abra uma instância de prompt de comando com privilégios elevados (executar como administrador) e execute uma desfragmentação na unidade:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - Dependendo do nível de fragmentação, a desfragmentação pode levar horas.
   - No comando, substitua `<LETTER ASSIGNED TO THE OS DISK>` pela letra do disco do sistema operacional (como a unidade *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Limpar a memória inadequada do repositório BCD (Dados de Configuração da Inicialização)

1. Abra um prompt de comando com privilégios elevados (executar como administrador).
1. Consulte o arquivo de configuração de inicialização para obter os sinalizadores de memória inválidos com o seguinte comando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - No comando, substitua `<LETTER ASSIGNED TO THE OS DISK>` pela letra do disco do sistema operacional (como a unidade *F:*).

1. Se a consulta não mostrar blocos de memória inválidos, pule para a próxima tarefa. Caso contrário, continue na etapa 4.
1. Se forem identificados blocos de memória inválidos, esses blocos estão impedindo a criação de um ramdisk e devem ser excluídos com o seguinte comando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - No comando, substitua `<LETTER ASSIGNED TO THE OS DISK>` pela letra do disco do sistema operacional (como a unidade *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Restaurar o arquivo de paginação para seu local padrão

O arquivo de paginação armazena dados que não podem ser mantidos pela RAM (memória de acesso aleatório) do seu computador como uma forma de estouro/backup. É possível que o arquivo esteja hospedado em um VHD em vez da unidade temporária, que é o local padrão do Azure. Se for true, o arquivo poderá não estar acessível e deverá ser restaurado para o local padrão.

Antes de executar qualquer etapa, você deve criar uma cópia da pasta **\Windows\System32\config** em um disco íntegro. Essa etapa garante que o pode desfazer qualquer alteração indesejada. Você estará trabalhando em arquivos de sistema importantes, portanto, essa precaução é altamente recomendável.

1. No Windows Search, digite **regedit** e abra o aplicativo editor do registro.
1. No editor do registro, realce a chave **HKEY_LOCAL_MACHINE** e selecione **arquivo > Carregar Hive...** no menu.

   ![O menu carregar hive do editor do registro.](./media/troubleshoot-windows-stop-error/4.png)

1. Na caixa de diálogo Carregar Hive, selecione **\windows\system32\config\SYSTEM** e clique em abrir.
   1. Você será solicitado a fornecer um nome, que deve ser inserido **BROKENSYSTEM**. Esse nome ajudará a diferenciar os hives afetados enquanto você estiver solucionando problemas.
   1. Expanda **HKEY_LOCAL_MACHINE** para ver a nova chave BROKENSYSTEM que você adicionou.
1. Usando o editor do registro, determine em qual controle o computador está inicializando.
   1. Navegue até **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> selecione**.
   1. Nas chaves listadas, observe o valor de dados de atual. Por exemplo, se esse valor for **1** ou **0x00000001 (1)**, o conjunto de controle será ControlSet001.
1. Verifique o local onde a criação do arquivo de paginação está configurada.
   1. Enquanto estiver em HKEY_LOCAL_MACHINE\BROKENSYSTEM, expanda o diretório que corresponde ao número de ControlSet identificado na etapa 4, como **ControlSet001**.
   1. Navegue até **controle >> Gerenciador de sessão >> gerenciamento de memória** e anote o local da chave **ExistingPageFiles** .
   1. Essa chave deve estar no local do Azure padrão da unidade temporária. Se não estiver lá e estiver em um VHD em outro local, como a unidade de disco de dados ou a unidade do sistema operacional, será necessário excluí-la.
   1. Navegue até esse local no explorador de arquivos e exclua o arquivo de **pagefile.sys** .

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

**Recomendado**: Antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados ao executar o seguinte script:

Para habilitar a coleta de despejo de memória e o console serial, execute o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados como um Administrador.
1. Liste os dados do repositório BCD e determine o identificador do carregador de inicialização que você usará na próxima etapa.

   1. Para uma VM de geração 1, insira o seguinte comando e observe o identificador listado:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - No comando, substitua `<BOOT PARTITON>` pela letra da partição no disco anexado que contém a pasta de inicialização.

      ![A saída da listagem do repositório BCD em uma VM de geração 1, que lista no carregador de inicialização do Windows o número do identificador.](./media/troubleshoot-windows-stop-error/5.png)

   1. Para uma VM de geração 2, insira o seguinte comando e observe o identificador listado:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - No comando, substitua `<LETTER OF THE EFI SYSTEM PARTITION>` pela letra da partição do sistema EFI.
   - Pode ser útil iniciar o console de gerenciamento de disco para identificar a partição de sistema apropriada rotulada como **partição do sistema EFI**.
   - O identificador pode ser um GUID exclusivo ou pode ser o **bootmgr** padrão.

1. Execute os seguintes comandos para habilitar o console serial:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - No comando, substitua `<VOLUME LETTER WHERE THE BCD FOLDER IS>` pela letra da pasta BCD.
   - No comando, substitua `<BOOT LOADER IDENTIFIER>` pelo identificador encontrado na etapa anterior.

1. Verifique se o espaço livre no disco do sistema operacional é maior ao tamanho da memória (RAM) na VM.

   Se não houver espaço suficiente no disco do sistema operacional, altere o local no qual o arquivo de despejo de memória será criado e encaminhe esse local a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua **% systemroot%** pela letra da unidade do disco de dados, como a unidade **F:**, nos comandos a seguir.

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
