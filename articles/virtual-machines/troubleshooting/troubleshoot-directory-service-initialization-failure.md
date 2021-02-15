---
title: Solucionar problemas de erros de parada do Windows – falha na inicialização do serviço de diretório
description: Resolva os problemas em que uma VM (máquina virtual) do controlador de domínio do Active Directory no Azure está presa em um loop, informando que precisa ser reiniciada.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028355"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Solucionar problemas de erros de parada do Windows – falha na inicialização do serviço de diretório

Este artigo mostra as etapas para resolver os problemas em que uma VM (máquina virtual) do controlador de domínio do Active Directory no Azure está presa em um loop, informando que precisa ser reiniciada.

## <a name="symptom"></a>Sintoma

Quando você usa o [Diagnóstico de inicialização](./boot-diagnostics.md) para ver a captura de tela da VM, a captura de tela mostra que a VM precisa ser reiniciada devido a um erro, exibindo o código de parada **0xC00002E1** no Windows Server 2008 R2 ou **0xC00002E2** no Windows Server 2012 ou posterior.

![A tela de inicialização do Windows Server 2012 informa "Seu PC encontrou um problema e precisa ser reiniciado. Estamos coletando algumas informações de erro e, em seguida, reiniciaremos para você".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Causa

O código de erro **0xC00002E2** representa **STATUS_DS_INIT_FAILURE**, e o código de erro **0xC00002E1** representa **STATUS_DS_CANT_START**. Os dois erros ocorrem quando há um problema com o serviço de diretório.

À medida que o sistema operacional é inicializado, ele é forçado a reiniciar automaticamente pelo servidor de autenticação de segurança local (**LSASS.exe**), que autentica logons de usuário. A autenticação não pode acontecer quando o sistema operacional na VM é um controlador de domínio que não tem acesso de leitura/gravação ao seu banco de dados do Active Directory local. Devido à falta de acesso ao **AD (Active Directory)** , o LSASS.exe não pode ser autenticado e é forçado a reiniciar o sistema operacional.

Esse erro pode ser causado por uma das seguintes condições:

- Não há acesso ao disco que contém o banco de dados do AD local (**NTDS.DIT**).
- O disco que contém o banco de dados do AD local (NTDS.DIT) ficou sem espaço livre.
- O arquivo do banco de dados do AD local (NTDS.DIT) está ausente.
- A VM tem vários discos e a política SAN (rede de área de armazenamento) está configurada incorretamente. A política de SAN não está definida como **ONLINEALL** e os discos que não são do sistema operacional são anexados no modo offline no gerenciador de discos.
- O arquivo do banco de dados do AD local (NTDS.DIT) está corrompido.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e acessar uma VM de reparo.
1. Liberar espaço em disco.
1. Verificar se a unidade que contém o banco de dados do AD está anexada.
1. Habilitar o Modo de Restauração dos Serviços de Diretório.
1. **Recomendado**: antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados.
1. Recompilar a VM.
1. Reconfigurar a política SAN.

> [!NOTE]
> Se encontrar esse erro, o SO convidado não está funcionando. Você deverá usar o modo offline para a solução de problemas.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar uma VM de reparo.
1. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="free-up-space-on-disk"></a>Liberar espaço em disco

Como o disco agora está anexado a uma VM de reparo, verifique se o disco que contém o banco de dados interno do Active Directory tem espaço suficiente para executar corretamente.

1. Verifique se o disco está cheio clicando com o botão direito do mouse na unidade e selecionando **Propriedades**.
1. Se o disco tiver menos de 300 MB de espaço livre, [expanda-o para um máximo de 1 TB usando o PowerShell](../windows/expand-os-disk.md).
1. Se o disco tiver atingido 1 TB de espaço usado, execute uma limpeza de disco.

   1. Use o PowerShell para [desanexar o disco de dados](../windows/detach-disk.md#detach-a-data-disk-using-powershell) da VM danificada.
   1. Depois de desanexado da VM danificada, [anexe o disco de dados](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm) a uma VM em funcionamento.
   1. Use a [ferramenta de limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço adicional.

1. **Opcional** – se mais espaço for necessário, abra uma instância de CMD e insira o comando `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` para executar uma desfragmentação na unidade:

  * No comando, substitua `<LETTER ASSIGNED TO THE OS DISK>` pela letra do disco do sistema operacional. Por exemplo, se a letra do disco for `F:`, o comando será `defrag F: /u /x /g`.

  * Dependendo do nível de fragmentação, a desfragmentação poderá levar horas.

Se houver espaço suficiente no disco, continue para a próxima tarefa.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Verifique se a unidade que contém o banco de dados do Active Directory está anexada

1. Abra uma instância CMD elevada e execute os seguintes comandos:

   1. Carregue o arquivo do Registro:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      A designação `f:` pressupõe que o disco é uma unidade `F:`. Use a letra da unidade que pertence à unidade que contém o disco do sistema operacional.

   1. Determine a letra da unidade e a pasta do **NTDS.DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Descarregue o arquivo do Registro:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Usando o portal do Azure, verifique se a unidade em que o NTDS.DIT está configurado foi adicionada à VM.
1. Usando o console de Gerenciamento de Disco do SO convidado, verifique se o disco que contém o NTDS.DIT está online.
   1. A ferramenta de Gerenciamento de Disco pode ser encontrada em **Ferramentas Administrativas > Gerenciamento do Computador > Armazenamento** ou pode ser acessada usando o comando `diskmgmt.msc` em uma instância do CMD.
1. Se o disco não estiver anexado à VM, anexe novamente o disco de dados para corrigir o problema.

   Se o disco tiver sido anexado normalmente, continue com a próxima tarefa.

### <a name="enable-directory-services-restore-mode"></a>Habilitar o Modo de Restauração dos Serviços de Diretório

Configure a VM para inicializar no **DSRM (Modo de Restauração dos Serviços de Diretório)** para ignorar a verificação da existência do arquivo NTDS.DIT durante a inicialização.

1. Antes de continuar, verifique se você concluiu as tarefas anteriores para anexar o disco a uma VM de reparo e se determinou em qual disco o arquivo NTDS.DIT está localizado.
1. Usando uma instância de CMD elevada, liste as informações de partição de inicialização nesse repositório para localizar o identificador da partição ativa:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Substitua `< Drive Letter >` pela letra determinada nas etapas anteriores.

   ![A captura de tela mostra uma instância de CMD elevada depois de inserir o comando 'bcdedit /store <Letra da Unidade>:\boot\bcd /enum', que exibe o Gerenciador de Inicialização do Windows com o identificador.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Habilite o sinalizador `safeboot DsRepair` na partição de inicialização:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Substitua `< Drive Letter >` e `< Identifier >` pelos valores determinados nas etapas anteriores.

1. Consulte as opções de inicialização novamente para garantir que sua alteração tenha sido definida corretamente.

   ![A captura de tela mostra uma instância de CMD elevada depois de habilitar o sinalizador DsRepair de inicialização segura.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados

Para habilitar a coleta de despejo de memória e o console serial, execute o script a seguir abrindo uma sessão de prompt de comandos com privilégios elevados (executar como administrador) e execute os comandos a seguir.

1. Habilite o console serial:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Verifique se o espaço livre no disco do sistema operacional é pelo menos igual ao tamanho da memória (RAM) na VM.

  1. Se não houver espaço suficiente no disco do sistema operacional, altere a localização no qual o arquivo de despejo de memória será criado e faça referência a qualquer disco de dados anexado à VM que tenha espaço livre suficiente.

     Para alterar a localização, substitua `%SystemRoot%` pela letra da unidade (por exemplo, `F:`) do disco de dados nos comandos a seguir.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>A seguinte configuração é sugerida para habilitar o despejo do sistema operacional:

  **Carregar disco do sistema operacional danificado**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

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

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Recompilar a VM

1. Use a [etapa 5 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar a VM.

### <a name="reconfigure-the-storage-area-network-policy"></a>Reconfigurar a política de rede de área de armazenamento

1. Ao inicializar no modo DSRM, o único usuário disponível para fazer logon é o administrador de recuperação, que foi usado quando a VM foi promovida a um controlador de domínio. Todos os outros usuários mostrarão um erro de autenticação.

   1. Se nenhum outro controlador de domínio estiver disponível, você deverá fazer logon localmente usando `.\administrator` ou `machinename\administrator` e a senha do DSRM.

1. Configure a política de SAN para que todos os discos estejam online.

   1. Abra uma instância de CMD elevada e insira `DISKPART`.
   1. Consulte a lista de discos.

      `DISKPART> list disk`

   1. Insira os seguintes comandos para selecionar o disco que precisa ser colocado online e altere a política de SAN:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Depois que o problema for corrigido, verifique se o sinalizador `DsRepair safeboot` foi removido:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Reinicie a VM.

   > [!NOTE]
   > Se a sua VM acabou de ser migrada localmente e você deseja migrar mais controladores de domínio do local para o Azure, considere seguir as etapas no artigo abaixo para evitar que esse problema ocorra em migrações futuras:
   >
   > [Como carregar controladores de domínio locais de Hyper-V existentes no Azure usando o Azure PowerShell](https://support.microsoft.com/help/2904015)
