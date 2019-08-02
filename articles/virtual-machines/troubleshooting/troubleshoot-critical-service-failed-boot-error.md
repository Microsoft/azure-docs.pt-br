---
title: SERVIÇO CRÍTICO COM FALHA durante a inicialização de uma VM do Azure | Microsoft Docs
description: Saiba como solucionar o erro "0x0000005A – SERVIÇO CRÍTICO COM FALHA" que ocorre durante a inicialização | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: ca3fe83d7072ba774c8124c5108652ab7223041d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449808"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>O Windows mostra "SERVIÇO CRÍTICO COM FALHA" na tela azul durante a inicialização de uma VM do Azure
Este artigo descreve o erro "SERVIÇO CRÍTICO COM FALHA" que pode ocorrer quando você inicializa uma VM (máquina virtual) do Windows no Microsoft Azure. Ele fornece etapas de solução de problemas que ajudam a resolvê-los. 

> [!NOTE] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve o uso do modelo de implantação do Resource Manager, que recomendamos usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não inicia. Quando você verificar as capturas de tela de inicialização em [Diagnósticos de inicialização](./boot-diagnostics.md), você verá uma das seguintes mensagens de erro em uma tela azul:

- "Seu computador teve um problema e precisa ser reiniciado. Você pode reiniciar. Para obter mais informações sobre esse problema e as possíveis correções, visite https://windows.com/stopcode. Se você chamar um profissional de suporte, forneça estas informações: Código de interrupção: SERVIÇO CRÍTICO COM FALHA” 
- "Seu computador teve um problema e precisa ser reiniciado. Apenas coletamos algumas informações de erro e, em seguida, reiniciaremos para você. Se você quiser saber mais, poderá pesquisar online mais tarde sobre este erro: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Causa

Há várias causas para erros de interrupção. As causas mais comuns são:
- Problema com um driver
- Memória ou arquivo do sistema corrompido
- O aplicativo acessa um setor proibido da memória

## <a name="solution"></a>Solução 

Para resolver esse problema [entre em contato com o suporte e envie um arquivo de despejo](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), que nos ajudará a diagnosticar o problema mais rapidamente, ou tente a seguinte solução de autoajuda.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. Tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco de SO a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md). 
3. Estabeleça uma conexão de área de trabalho remota com a VM de recuperação.

### <a name="enable-dump-logs-and-serial-console"></a>Habilitar logs de despejo e o console serial

O log de despejo e o [console serial](./serial-console-windows.md) nos ajudarão mais com a solução de problemas.

Para habilitar logs de despejo e o console serial, execute o script a seguir.

1. Abra uma sessão de prompt de comandos com privilégios elevados (executar como administrador).
2. Execute o seguinte script:

    Nesse script, vamos supor que a letra da unidade atribuída ao disco do sistema operacional anexado seja F. Você deve substituí-la pelo valor apropriado para sua VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Substituir os drivers não assinados

1. Na VM de recuperação, execute o seguinte comando em um prompt de comandos com privilégios elevados. Esse comando define o disco do sistema operacional afetado para se iniciado no modo de segurança na próxima inicialização:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Por exemplo, se o disco do sistema operacional que você anexou for a unidade F, execute o seguinte comando:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Desanexe o disco do sistema operacional e, em seguida, anexe-o novamente na VM afetada](troubleshoot-recovery-disks-portal-windows.md). A VM será inicializada no modo de segurança. Se o erro persistir, acesse a etapa opcional.
3. Abra a caixa **Executar** e execute o **verificador** para iniciar a ferramenta Gerenciador de Verificação de Driver.
4. Escolha **Selecionar drivers não assinados automaticamente** e, em seguida, clique em **Avançar**.
5. Você obterá a lista dos arquivos de driver que não estão assinados. Lembre-se dos nomes dos arquivos.
6. Copie as mesmas versões desses arquivos de uma VM de trabalho e, em seguida, substitua esses arquivos não assinados. 

7. Remova as configurações de inicialização segura:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Reinicie a VM. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Opcional: analisar os logs de despejo no modo de despejo de memória

Para analisar os logs de despejo, siga estas etapas:

1. Anexe o disco do sistema operacional a uma VM de recuperação.
2. No disco do sistema operacional que você anexou, navegue até **\windows\system32\config**. Copie todos os arquivos como um backup para o caso de necessidade de reversão.
3. Inicie o **Editor do Registro** (regedit.exe).
4. Selecione a chave **HKEY_LOCAL_MACHINE**. No menu, selecione **Arquivo** > **Carregar Hive**.
5. Navegue até a pasta **\windows\system32\config\SYSTEM** no disco do sistema operacional que você anexou. Como o nome da seção, digite **BROKENSYSTEM**. O novo hive do Registro é exibido sob a chave **HKEY_LOCAL_MACHINE**.
6. Navegue até **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** e faça as seguintes alterações:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Selecione **BROKENSYSTEM**. No menu, selecione **Arquivo** > **Descarregar Hive**.
8.  Modifique a instalação do BCD para inicializar no modo de depuração. Execute os seguintes comandos em um prompt de comandos com privilégios elevados:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Desanexe o disco do sistema operacional e, em seguida, anexe-o novamente na VM afetada](troubleshoot-recovery-disks-portal-windows.md).
10. Inicialize a VM para ver se ela mostra a análise de despejo. Localize o arquivo que falha ao ser carregado. Você precisará substituir esse arquivo por um arquivo da VM de trabalho. 

    A seguir está um exemplo de análise de despejo. Você pode ver que a **FAILURE** está em filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Depois que a VM estiver funcionando e tiver uma reinicialização normal, remova as configurações de despejo de memória:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
