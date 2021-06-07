---
title: Solucionar problemas de recuperação de arquivos de VM do Azure
description: Solucionar problemas ao recuperar arquivos e pastas de um backup de VM do Azure.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700296"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Solucionar problemas na recuperação de arquivo de um backup de VM do Azure

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver problemas de recuperação de arquivos e pastas de um backup de VM (máquina virtual) do Azure.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Esta seção fornece etapas para solucionar problemas de mensagens de erro que você pode ver.

### <a name="exception-caught-while-connecting-to-target"></a>"Exceção detectada ao se conectar ao destino"

**Causa possível**: o script não pode acessar o ponto de recuperação.

**Ação recomendada**: para resolver esse problema, siga as etapas listadas no [script é executado, mas a conexão falhou](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"O destino já foi conectado por meio de uma sessão iSCSI"

**Causa possível**: o script já foi executado no mesmo computador e as unidades foram anexadas.

**Ação recomendada**: os volumes do ponto de recuperação já foram anexados. Eles não podem ser montados com as mesmas letras de unidade da VM original. Navegue pelos volumes disponíveis no explorador de arquivos.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Este script é inválido porque os discos foram desmontados por meio do portal/excederam o limite de 12 horas. Baixar um novo script do portal "

**Causa possível**: os discos foram desmontados do portal ou o limite de tempo de 12 horas foi excedido.

**Ação recomendada**: 12 horas depois de baixar o script, ele se torna inválido e não pode ser executado. Acesse o portal e baixe um novo script para continuar com a recuperação de arquivos.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>O módulo iscsi_tcp não pode ser carregado (ou) iscsi_tcp_module não encontrado

**Ação recomendada**: para resolver esse problema, siga as etapas em [downloads de script com êxito, mas falha na execução](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Problemas comuns

Esta seção fornece etapas para solucionar problemas comuns que podem ocorrer durante o download e a execução do script para recuperação de arquivos.

### <a name="you-cant-download-the-script"></a>Não é possível baixar o script

1. Verifique se você tem as [permissões necessárias para baixar o script](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Verifique a conexão com os IPs de destino do Azure. Execute um dos seguintes comandos em um prompt de comando elevado:

   `nslookup download.microsoft.com`

    ou

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script é baixado com êxito, mas não é executado

Quando você executa o script Python para ILR (recuperação em nível de item) no SUSE Linux Enterprise Server 12 SP4, ele falha com o erro "o módulo iscsi_tcp não pode ser carregado" ou "iscsi_tcp_module não encontrado".

**Causa possível**: o módulo ILR usa **iscsi_tcp** para estabelecer uma conexão TCP com o serviço de backup. Como parte da versão do SLES 12 SP4, o SUSE removeu **iscsi_tcp** do pacote Open-iSCSI, de modo que a operação ILR falha.

**Ação recomendada**: a execução do script de recuperação de arquivo não tem suporte em VMs do SUSE 12 SP4. Experimente a operação de restauração em uma versão mais antiga do SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>O script é executado, mas a conexão com o destino iSCSI falhou

Você pode ver uma mensagem de erro "exceção detectada ao conectar-se ao destino".

1. Verifique se o computador onde o script é executado atende aos [requisitos de acesso](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Verifique a conexão com os IPs de destino do Azure. Execute um dos seguintes comandos em um prompt de comando elevado:

   `nslookup download.microsoft.com`

   ou

   `ping download.microsoft.com`
1. Verifique o acesso à porta de saída iSCSI 3260.
1. Verifique se há um firewall ou NSG bloqueando o tráfego para as URLs de serviço de recuperação ou IPs de destino do Azure.
1. Verifique se o seu software antivírus não está bloqueando a execução do script.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Você está conectado ao ponto de recuperação, mas os discos não estavam anexados

Resolva esse problema seguindo as etapas para o seu sistema operacional.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>A recuperação de arquivos do Windows falha no servidor com pools de armazenamento

Quando você executa o script pela primeira vez no Windows Server 2012 R2 e no Windows Server 2016 (com pools de armazenamento), o pool de armazenamento pode ser anexado à VM em somente leitura.

>[!Tip]
> Verifique se você tem o [computador certo para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Para resolver esse problema, atribua manualmente o acesso de leitura/gravação ao pool de armazenamento e anexe os discos virtuais:

1. Acesse **Gerenciador do servidor**  >  **arquivos e serviços de armazenamento**  >  **volumes**  >  **pools de armazenamento**.

   ![Captura de tela mostrando opções de pools de armazenamento.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Na janela **pool de armazenamento** , clique com o botão direito do mouse no pool de armazenamento disponível e selecione **definir acesso de Read-Write**.

   ![Captura de tela mostrando opções de clique com o botão direito do mouse em um spool de armazenamento.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Depois que o pool de armazenamento recebe acesso de leitura/gravação, clique com o botão direito do mouse na seção **discos virtuais** e selecione **anexar disco virtual**.

   ![Captura de tela mostrando opções de clique com o botão direito do mouse para um disco virtual.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>A recuperação de arquivo do Linux falha na montagem automática porque o disco não contém volumes

Ao executar a recuperação de arquivos, o serviço de backup detecta volumes e montagens automáticas. No entanto, se os discos de backup tiverem partições brutas, esses discos não serão montados automaticamente e você não poderá ver o disco de dados para recuperação.

Para resolver esse problema, acesse [recuperar arquivos do backup de máquina virtual do Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>A recuperação de arquivos do Linux falha porque o so não pôde identificar o sistema de arquivos

Quando você executa o script de recuperação de arquivo, o disco de dados falha ao anexar. Você verá um erro "as partições a seguir falharam ao montar, pois o sistema operacional não pôde identificar o sistema de arquivos".

Para resolver esse problema, verifique se o volume está criptografado com um aplicativo de terceiros. Se ele estiver criptografado, o disco ou a VM não aparecerá como criptografado no Portal.

1. Entre na VM de backup e execute este comando:

   `lsblk -f`

   ![Captura de tela mostrando os resultados do comando para listar dispositivos de bloco.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Verifique o sistema de arquivos e a criptografia. Se o volume for criptografado, a recuperação de arquivos não terá suporte. Saiba mais em [matriz de suporte para backup de VM do Azure](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Os discos estão anexados, mas os volumes não estão montados

Resolva esse problema seguindo as etapas para o seu sistema operacional.

#### <a name="windows"></a>Windows

Ao executar o script de recuperação de arquivo para Windows, você verá uma mensagem "0 volumes de recuperação anexados". No entanto, os discos são descobertos no console de gerenciamento de disco.

**Possível causa**: quando você anexou volumes por meio de iSCSI, alguns volumes detectados ficaram offline. Quando o canal iSCSI se comunica entre a VM e o serviço, ele detecta esses volumes e os coloca online, mas eles não são montados.

   ![Captura de tela mostrando os 0 volumes de recuperação anexados.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esse problema, execute as seguintes etapas:

>[!Tip]
>Verifique se você tem o [computador certo para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Na janela **cmd** , execute **diskmgmt** para abrir o **Gerenciamento de disco**.
1. Procure quaisquer discos adicionais. No exemplo a seguir, o **disco 2** é um disco adicional.

   ![Captura de tela da janela de gerenciamento de disco com disco adicional.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Clique com o botão direito do mouse em **novo volume** e selecione **alterar a letra da unidade e os caminhos**.

   ![Captura de tela mostrando as opções de clique com o botão direito do mouse no disco adicional.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Na janela **alterar a letra da unidade ou o caminho** , selecione **atribuir a seguinte letra de unidade**, atribua uma unidade disponível e, em seguida, selecione **OK**.

   ![Captura de tela da janela alterar a letra da unidade ou do caminho.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Abra o explorador de arquivos para exibir a unidade escolhida e explorar os arquivos.

#### <a name="linux"></a>Linux

>[!Tip]
>Verifique se você tem o [computador certo para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Se a VM do Linux protegida usar o LVM ou matrizes RAID, siga as etapas em [recuperar arquivos do backup de máquina virtual do Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os arquivos de volumes montados

A cópia pode falhar com o erro "0x80070780: o arquivo não pode ser acessado pelo sistema". 

Verifique se o servidor de origem tem eliminação de duplicação de disco habilitada. Se tiver, verifique se o servidor de restauração também tem a eliminação de duplicação habilitada nas unidades. Você pode deixar a eliminação de duplicação desconfigurada para não eliminar a duplicação das unidades no servidor de restauração.

## <a name="next-steps"></a>Próximas etapas

- [Recuperar arquivos e pastas do backup de máquina virtual do Azure](backup-azure-restore-files-from-vm.md)