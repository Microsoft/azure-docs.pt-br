---
title: A máquina virtual não está respondendo ao aplicar a política de configuração de política de auditoria
description: Este artigo fornece etapas para resolver problemas em que a VM (máquina virtual) deixa de responder enquanto aplica a política de configuração de política de auditoria, que impede a inicialização de uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: f286542c91ba473d13595d8e8299b1bbd8c93856
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632598"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>A máquina virtual não está respondendo ao aplicar a política de configuração de política de auditoria

Este artigo fornece etapas para resolver problemas em que a VM (máquina virtual) deixa de responder enquanto aplica a política de configuração de política de auditoria, que impede a inicialização de uma VM do Azure.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe que o sistema operacional (SO) não estava respondendo durante uma inicialização com a mensagem **aplicando a política de configuração de política de auditoria**.

  ![O sistema operacional Inicializando com a mensagem: "aplicando a política de configuração de política de auditoria"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![O sistema operacional inicializando no Windows Server 2012 com a mensagem: "aplicando a política de configuração de política de auditoria"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Causa

Há bloqueios conflitantes quando a política tenta limpar perfis de usuário antigos.

> [!NOTE]
> Aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Aqui está a política problemática: *Computer configuração templates \ System/User Profiles\Delete perfis de usuário mais antigos do que um número especificado de dias durante a reinicialização do sistema.*

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
1. Desabilitar a política.
1. Habilitar o console serial e a coleção de despejo de memória.
1. Recompilar a VM.
1. Colete o arquivo de despejo de memória e envie um tíquete de suporte.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as etapas 1-3 dos [Comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Use a conexão da área de trabalho remota para conectar-se à VM de reparo.

### <a name="disable-the-policy"></a>Desabilitar a política

1. Na VM de reparo, abra o **Editor do registro**.
1. Localize a chave **HKEY_LOCAL_MACHINE** e selecione **arquivo > Carregar Hive** no menu.

   ![A navegação no editor do registro para carregar um Hive.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Você pode usar o Load Hive para carregar chaves do registro de um sistema offline. Nesse caso, o sistema é o disco danificado anexado à VM de reparo.
   - As configurações de todo o sistema são armazenadas em **HKEY_LOCAL_MACHINE** e podem ser abreviadas como **HKLM**.

1. No disco anexado, abra o `\windows\system32\config\SOFTWARE` arquivo.

   - Quando for solicitado um nome, insira **BROKENSOFTWARE**.
   - Para verificar se **BROKENSOFTWARE** foi carregado, expanda **HKEY_LOCAL_MACHINE** e procure a chave **BROKENSOFTWARE** adicionada.

1. Vá para **BROKENSOFTWARE** e verifique se a chave **CleanupProfiles** existe no hive carregado.

   - Se a chave existir, a política **CleanupProfiles** será definida. Seu valor representa a política de retenção medida em dias.
   - Se a chave não existir, a política **CleanupProfiles** não será definida. Nessa situação, pule para [enviar um tíquete de suporte com um arquivo de despejo de memória](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Exclua a chave **CleanupProfiles** usando este comando:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Descarregue o hive **BROKENSOFTWARE** usando este comando:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

**Recomendado**: Antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados ao executar o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados como um Administrador.
1. Liste os dados do repositório BCD e determine o identificador do carregador de inicialização, que será usado na próxima etapa.

   1. Para uma VM de geração 1, insira o seguinte comando e observe o identificador listado:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - No comando, substitua `<BOOT PARTITON>` pela letra da partição no disco anexado que contém a pasta de inicialização.

        ![A Figura 4 mostra a saída da listagem do repositório BCD em uma VM de geração 1, que lista no carregador de inicialização do Windows o número do identificador.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. Para uma VM de geração 2, insira o seguinte comando e observe o identificador listado:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - No comando, substitua `<LETTER OF THE EFI SYSTEM PARTITION>` pela letra da partição do sistema EFI.
      - Pode ser útil iniciar o console de gerenciamento de disco para identificar a partição de sistema apropriada rotulada como **partição do sistema EFI**.
      - O identificador pode ser um GUID exclusivo ou pode ser o **bootmgr** padrão.

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
   
### <a name="rebuild-the-virtual-machine"></a>Recriar a máquina virtual

1. Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.

1. Teste se sua VM é inicializada normalmente para ver se o problema corrigiu o problema.

   - Se o problema não tiver sido corrigido, continue a [coletar um arquivo de despejo e enviar um tíquete de suporte](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Se o problema tiver sido corrigido, nenhuma etapa adicional será necessária.

Se o problema foi corrigido, a política agora está desabilitada localmente. Para uma solução permanente, não use a política CleanupProfiles em VMs, pois ela excluirá automaticamente os perfis de usuário. Use um método diferente para executar limpezas de perfil, como uma tarefa ou script agendado.

**Não use esta política:** 
 *Machine\Admin Templates\System\User Profiles\Delete perfis de usuário mais antigos que um número especificado de dias durante a reinicialização do sistema.*

### <a name="the-issue-should-now-be-fixed"></a>O problema agora deve ser corrigido

Teste sua VM para certificar-se de que ela está funcionando como normal. Se você ainda estiver enfrentando problemas, poderá continuar na próxima seção para obter mais assistência.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Coletar o arquivo de despejo de memória e enviar um tíquete de suporte

Para resolver esse problema, você precisaria primeiro coletar o arquivo de despejo de memória para a falha e, em seguida, contatar o suporte com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Use as etapas 1-3 dos [comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma nova VM de reparo.
1. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for rotulada como *F*, você precisará ir para `F:\Windows` .
1. Localize o `memory.dmp` arquivo e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.
1. Se você estiver tendo problemas para localizar o `memory.dmp` arquivo, use [chamadas de NMI (interrupção não mascarada) no console serial](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) em vez disso. Siga o guia para [gerar um arquivo de despejo de memória usando chamadas NMI aqui](/windows/client-management/generate-kernel-or-complete-crash-dump).