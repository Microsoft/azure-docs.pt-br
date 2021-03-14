---
title: Solucionar problemas de agente e extensão
description: Sintomas, causas e resoluções para falhas do Backup do Azure relacionados a agente, extensão e discos.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519598"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha do Backup do Azure: problemas com o agente ou a extensão

Este artigo fornece etapas de solução de problemas que podem ajudar você a resolver erros de Backup do Azure relacionados à comunicação com a extensão e o agente da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Guia passo a passo para solucionar problemas de falhas de backup

As falhas de backup mais comuns podem ser autoresolvidas seguindo as etapas de solução de problemas listadas abaixo:

### <a name="step-1-check-azure-vm-health"></a>Etapa 1: verificar a integridade da VM do Azure

- **Verifique se o estado de provisionamento da VM do Azure está ' em execução '**: se o [estado de provisionamento da VM](../virtual-machines/states-billing.md) estiver no estado **parado/desalocado/atualizando** , ele irá interferir na operação de backup. Abra *portal do Azure > VM > visão geral >* e verifique o status da VM para garantir que ela esteja **em execução**  e repita a operação de backup.
- **Revisar atualizações ou reinicializações pendentes do so**: Verifique se não há nenhuma atualização do sistema operacional pendente ou reinicializações pendentes na VM.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Etapa 2: verificar a integridade do serviço do agente convidado de VM do Azure

- **Verifique se o serviço agente convidado de VM do Azure foi iniciado e atualizado**:
  - Em uma VM do Windows:
    - Navegue até **Services. msc** e verifique se o **serviço de agente convidado de VM do Windows Azure** está em execução. Além disso, verifique se a [versão mais recente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) está instalada. Para saber mais, consulte [problemas do agente convidado de VM do Windows](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - O agente de VM do Azure é instalado por padrão em qualquer VM do Windows implantada de uma imagem do Azure Marketplace do portal, do PowerShell, da interface de linha de comando ou de um modelo de Azure Resource Manager. Uma [instalação manual do agente](../virtual-machines/extensions/agent-windows.md#manual-installation) pode ser necessária quando você cria uma imagem de VM personalizada que é implantada no Azure.
    - Examine a matriz de suporte para verificar se a VM é executada no [sistema operacional Windows com suporte](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Na VM do Linux,
    - Verifique se o serviço do agente convidado de VM do Azure está em execução executando o comando `ps-e` . Além disso, verifique se a [versão mais recente](../virtual-machines/extensions/update-linux-agent.md) está instalada. Para saber mais, consulte [problemas do agente convidado de VM do Linux](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Verifique se as [dependências do agente de VM do Linux em pacotes do sistema](../virtual-machines/extensions/agent-linux.md#requirements) têm a configuração com suporte. Por exemplo: a versão do Python com suporte é 2,6 e superior.
    - Examine a matriz de suporte para verificar se a VM é executada no [sistema operacional Linux com suporte.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Etapa 3: verificar a integridade da extensão de VM do Azure

- **Verifique se todas as extensões de VM do Azure estão no estado ' provisionamento bem-sucedido '**: se alguma extensão estiver em um estado de falha, ela poderá interferir no backup.
- *Abra portal do Azure > VM > configurações > extensões > status de extensões* e verifique se todas as extensões estão em estado de **provisionamento bem-sucedido** .
- Verifique se todos os [problemas de extensão](../virtual-machines/extensions/overview.md#troubleshoot-extensions) foram resolvidos e repita a operação de backup.
- **Verifique se o aplicativo de sistema com+** está em execução. Além disso, o **serviço de coordenador de transações distribuídas** deve ser executado como uma conta de serviço de **rede**. Siga as etapas neste artigo para [solucionar problemas de com+ e MSDTC](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Etapa 4: verificar a integridade da extensão de VM do backup do Azure

O backup do Azure usa a extensão de instantâneo da VM para obter um backup consistente com o aplicativo da máquina virtual do Azure. O backup do Azure instalará a extensão como parte do primeiro backup agendado disparado após habilitar o backup.

- **Verifique se a extensão VMSnapshot não está em um estado de falha**: siga as etapas listadas nesta [seção](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para verificar e garantir que a extensão de backup do Azure esteja íntegra.

- **Verifique se o antivírus está bloqueando a extensão**: determinados softwares antivírus podem impedir a execução de extensões.
  
  No momento da falha do backup, verifique se há entradas de log em ***Visualizador de eventos logs de aplicativo** _ com _ *_nome do aplicativo com falha: IaaSBcdrExtension.exe_* *. Se você vir entradas, pode ser que o antivírus configurado na VM esteja restringindo a execução da extensão de backup. Teste excluindo os seguintes diretórios na configuração de antivírus e repita a operação de backup.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Verifique se o acesso à rede é necessário**: os pacotes de extensão são baixados do repositório de extensões de armazenamento do Azure e os carregamentos de status de extensão são postados no armazenamento do Azure. [Saiba mais](../virtual-machines/extensions/features-windows.md#network-access).
  - Se você estiver em uma versão sem suporte do agente, precisará permitir o acesso de saída ao armazenamento do Azure nessa região da VM.
  - Se você tiver bloqueado o acesso ao `168.63.129.16` usando o firewall convidado ou com um proxy, as extensões falharão independentemente das anteriores. As portas 80, 443 e 32526 são necessárias, [saiba mais](../virtual-machines/extensions/features-windows.md#network-access).

- **Verifique se o DHCP está habilitado na VM convidada**: isso é necessário para obter o endereço de host ou de malha do DHCP para que o backup da VM IaaS funcione. Se você precisar de um IP privado estático, deverá configurá-lo por meio do portal do Azure ou do PowerShell e certificar-se de que a opção DHCP dentro da VM esteja habilitada, [saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Verifique se o Serviço gravador VSS está em execução**: Siga estas etapas para [solucionar problemas do gravador VSS](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Siga as diretrizes de práticas recomendadas de backup**: examine as [práticas recomendadas para habilitar o backup de VM do Azure](backup-azure-vms-introduction.md#best-practices).
- **Examinar as diretrizes para discos criptografados**: se você estiver habilitando o backup para VMs com disco criptografado, certifique-se de ter fornecido todas as permissões necessárias. Para saber mais, confira [fazer backup e restaurar a VM do Azure criptografada](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - o agente de VM não pode se comunicar com o Backup do Azure

**Código de erro**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: o agente de VM não pode se comunicar com o backup do Azure<br>

O agente de VM do Azure pode ser interrompido, desatualizado, em um estado inconsistente ou não está instalado. Esses Estados impedem que o serviço de backup do Azure acione instantâneos.

- **Abra portal do Azure > VM > configurações > painel propriedades** > Verifique se o **status** da VM está **em execução** e se o **status do agente** está **pronto**. Se o agente de VM for interrompido ou estiver em um estado inconsistente, reinicie o agente<br>
  - Para VMs do Windows, siga estas [etapas](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) para reiniciar o agente convidado.<br>
  - Para VMs do Linux, siga estas [etapas](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) para reiniciar o agente convidado.
- **Abra portal do Azure > VM > configurações > extensões** > Verifique se todas as extensões estão em estado de **provisionamento bem-sucedido** . Caso contrário, siga estas [etapas](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para resolver o problema.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – não foi possível se comunicar com o agente de VM para o status do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível se comunicar com o agente VM para status do instantâneo <br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  

**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: [as opções de configuração do agente de VM não estão definidas (para VMs do Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Causa 5: a [solução de controle de aplicativo está bloqueando IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - a VM está em estado de provisionamento com falha

**Código de erro**: UserErrorVmProvisioningStateFailed<br>
**Mensagem de erro**: a VM está em estado de provisionamento com falha<br>

Esse erro ocorre quando uma das falhas de extensão coloca a VM em estado de falha no provisionamento.<br>**Abra portal do Azure > VM > configurações > extensões > status de extensões** e verifique se todas as extensões estão em estado de **provisionamento bem-sucedido** . Para saber mais, confira [Estados de provisionamento](../virtual-machines/states-billing.md).

- Se qualquer extensão estiver em um estado de falha, ela poderá interferir no backup. Verifique se esses problemas de extensão foram resolvidos e repita a operação de backup.
- Se o estado de provisionamento da VM estiver em um estado de atualização, ele poderá interferir no backup. Verifique se ele está íntegro e repita a operação de backup.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – o limite máximo da coleção de pontos de restauração foi atingido

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: o limite máximo da coleção de pontos de restauração foi atingido. <br>

- Esse problema pode ocorrer se houver um bloqueio no grupo de recursos do ponto de recuperação, impedindo a limpeza automática de pontos de recuperação.
- Esse problema também pode ocorrer se vários backups são disparados por dia. Atualmente, é recomendável apenas um backup por dia, pois os pontos de restauração instantâneas são mantidos por 1-5 dias de acordo com a retenção de instantâneo configurada e somente 18 RPs podem ser associados a uma VM em um determinado momento. <br>
- O número de pontos de restauração em coleções de pontos de restauração e grupos de recursos para uma VM não pode exceder 18. Para criar um novo ponto de restauração, exclua os pontos de restauração existentes.

Ação recomendada:<br>
Para resolver esse problema, remova o bloqueio do grupo de recursos da VM e repita a operação para disparar a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado que o grupo de recursos da VM para armazenar a coleção de pontos de restauração. Você é aconselhado a não bloquear o grupo de recursos criado para uso pelo serviço de backup. O formato de nomenclatura do grupo de recursos criado pelo serviço de backup é: AzureBackupRG_ `<Geo>` _ `<number>` . Por exemplo: *AzureBackupRG_northeurope_1*

**Etapa 1: [ Remover bloqueio do grupo de recursos do ponto de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Etapa 2: [limpar a coleção de pontos de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – o backup não tem permissões suficientes no cofre de chaves para o backup de VMs criptografadas

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: o backup não tem permissões suficientes do cofre de chaves para fazer backup de VMs criptografadas. <br>

Para que uma operação de backup tenha sucesso em VMs criptografadas, ela deve ter permissões para acessar o cofre de chaves. As permissões podem ser definidas por meio do [portal do Azure](./backup-azure-vms-encryption.md) ou por meio do [PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork: falha na operação do instantâneo por falta de conectividade à rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: falha na operação de instantâneo por falta de conectividade à rede na máquina virtual<br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua a seguinte etapa de solução de problemas e repita a operação:

**[O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - falha na operação da extensão VMSnapshot

**Código de erro**: ExtensionOperationFailedForManagedDisks <br>
**Mensagem de erro**: falha na operação da extensão VMSnapshot<br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – falha no backup, com um erro interno

**Código de erro**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Mensagem de erro**: falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [o serviço de backup não tem permissão para excluir os pontos de restauração antigos devido a um bloqueio de grupo de recursos](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-os tamanhos de disco configurados não têm suporte no momento no backup do Azure

**código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: o backup do Azure não dá suporte para os tamanhos de disco configurados no momento. <br>

Sua operação de backup pode falhar ao fazer backup de uma VM com um tamanho de disco maior que 32 TB. Além disso, o backup de discos criptografados com mais de 4 TB não tem suporte no momento. Verifique se os tamanhos de disco são menores ou iguais ao limite com suporte, dividindo os discos.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – não é possível iniciar o backup porque outra operação de backup já está em andamento

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: não é possível iniciar o backup, pois outra operação de backup está em andamento<br>

O trabalho de backup recente falhou porque há um trabalho de backup existente em andamento. Você não pode iniciar um novo trabalho de backup antes que o trabalho atual seja concluído. Verifique se a operação de backup em andamento foi concluída antes de iniciar ou agendar outras operações de backup. Para verificar o status dos trabalhos de backup, execute as seguintes etapas:

1. Entre no portal do Azure, selecione **todos os serviços**. Digite serviços de recuperação e selecione **cofres dos serviços de recuperação**. A lista de cofres de Serviços de Recuperação aparecerá.
2. Na lista de cofres dos serviços de recuperação, selecione um cofre no qual o backup está configurado.
3. No menu do painel do cofre, selecione **trabalhos de backup** ele exibe todos os trabalhos de backup.
   - Se um trabalho de backup estiver em andamento, aguarde a sua conclusão ou cancele-o.
     - Para cancelar o trabalho de backup, clique com o botão direito do mouse no trabalho de backup e selecione **Cancelar** ou usar o [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Se você reconfigurou o backup em um cofre diferente, certifique-se de que não há nenhum trabalho de backup em execução no cofre antigo. Se existir, cancele o trabalho de backup.
     - Para cancelar o trabalho de backup, clique com o botão direito do mouse no trabalho de backup e selecione **Cancelar** ou usar o [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)
4. Tente novamente a operação de backup.

Se a operação de backup agendado estiver demorando mais, em conflito com a próxima configuração de backup, revise as [práticas recomendadas](backup-azure-vms-introduction.md#best-practices), [desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e [consideração de restauração](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - falha no backup devido a um erro. Para obter detalhes, consulte Detalhes da mensagem de erro do trabalho

**Código de erro**: UserErrorCrpReportedUserError <br>
**Mensagem de erro**: falha no backup devido a um erro. Para obter detalhes, consulte detalhes da mensagem de erro do trabalho.

Esse erro é relatado da VM IaaS. Para identificar a causa raiz do problema, vá para as configurações do cofre dos serviços de recuperação. Na seção **monitoramento** , selecione **trabalhos de backup** para filtrar e exibir o status. Selecione **falhas** para examinar os detalhes da mensagem de erro subjacente. Execute outras ações de acordo com as recomendações na página de detalhes do erro.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-falha no backup: esta máquina virtual não está protegida (ativamente) pelo backup do Azure

**Código de erro**: UserErrorBcmDatasourceNotPresent <br>
**Mensagem de erro**: falha no backup: esta máquina virtual não está protegida (ativamente) pelo backup do Azure.

Verifique se a máquina virtual fornecida está ativamente (não no estado de pausa) protegida pelo backup do Azure. Para resolver esse problema, verifique se a máquina virtual está ativa e repita a operação.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente é instalado na VM, mas não responde (para VMs do Windows)

#### <a name="solution-for-this-error"></a>Solução para este erro

O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Microsoft Azure está executando nos serviços de VM (services.msc). Tente reiniciar o serviço de agente convidado do Microsoft Azure e inicie o backup.
2. Se o serviço de agente convidado do Microsoft Azures não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço de agente convidado do Microsoft Azure está instalado.
3. Se o serviço de agente convidado do Microsoft Azures aparecer em **Programas e Recursos**, desinstale o serviço de agente convidado do Microsoft Azure.
4. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
5. Verifique se os serviços de agente convidado do Microsoft Azures aparecem nos serviços.
6. Execute um backup sob demanda:
   - No portal, selecione **Fazer Backup Agora**.

Verifique também se o [Microsoft .NET 4.5 está instalado](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4.5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, colete os seguintes logs da VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se você precisar de log detalhado para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **habilitar o log detalhado (y | n)**
2. Altere o valor **Logs.Verbose** de *n* para *s*.
3. Salve a alteração e, em seguida, reinicie o waagent, completando as etapas descritas anteriormente nesta seção.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-Agent opções de configuração não estão definidas (para VMs do Linux)

Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. Extensões de opções de arquivo de configuração **. habilitar** deve ser definido como **s** e **provisionamento. o agente** deve ser definido como **auto** para que o backup funcione.
Para obter uma lista completa das opções de arquivo de configuração VM-Agent, consulte <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>A solução de controle de aplicativo está bloqueando IaaSBcdrExtension.exe

Se você estiver executando o [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (ou outra solução de controle de aplicativo) e as regras forem baseadas no Publicador ou no caminho, elas poderão impedir a execução do **IaaSBcdrExtension.exe** executável.

#### <a name="solution-to-this-issue"></a>Solução para esse problema

Exclua o `/var/lib` caminho ou o **IaaSBcdrExtension.exe** executável do AppLocker (ou de outro software de controle de aplicativo).

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo

O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution-for-this-issue"></a>Solução para esse problema

As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O status da VM foi informado incorretamente porque a VM foi desligada no protocolo RDP. | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **desligamento** no painel da VM. |
| A VM não pode obter o endereço do host ou da malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione. Se a VM não puder obter o endereço do host ou da malha por meio da resposta 245 do DHCP, ela não poderá baixar ou executar nenhuma extensão. Se você precisar de um IP privado estático, deverá configurá-lo por meio do **portal do Azure** ou do **PowerShell** e certificar-se de que a opção DHCP dentro da VM esteja habilitada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos de ponto de recuperação

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Opção ir para **todos os recursos**, selecione o grupo de recursos coleção de pontos de restauração no seguinte formato AzureBackupRG_ `<Geo>` _ `<number>` .
3. Na seção **Configurações**, selecione **Bloqueios** para exibir os bloqueios.
4. Para remover o bloqueio, selecione as reticências e selecione **excluir**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Limpar coleção de pontos de restauração

Depois de remover o bloqueio, os pontos de restauração precisam ser limpos.

Se você excluir o grupo de recursos da VM ou a própria VM, os instantâneos de restauração instantânea dos discos gerenciados permanecerão ativos e expirarão de acordo com o conjunto de retenção. Para excluir os instantâneos de restauração instantânea (se você não precisar mais deles) armazenados na coleção de pontos de restauração, limpe a coleção de pontos de restauração de acordo com as etapas fornecidas abaixo.

Para limpar os pontos de restauração, siga qualquer um dos seguintes métodos:<br>

- [Limpar a coleta do ponto de restauração executando backup sob demanda](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Limpar a coleção de pontos de restauração do portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Limpar a coleta do ponto de restauração executando backup sob demanda

Depois de remover o bloqueio, dispare um backup sob demanda. Essa ação garantirá que os pontos de restauração sejam limpos automaticamente. Espere que essa operação sob demanda falhe na primeira vez. No entanto, ele garantirá a limpeza automática em vez da exclusão manual de pontos de restauração. Após a limpeza, seu próximo backup agendado deverá ter sucesso.

> [!NOTE]
> A limpeza automática ocorrerá após algumas horas de acionamento do backup sob demanda. Se o backup agendado ainda falhar, tente excluir manualmente a coleção de pontos de restauração usando as etapas listadas [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar a coleção de pontos de restauração do portal do Azure <br>

Para limpar manualmente a coleção de pontos de restauração, que não é limpa devido ao bloqueio no grupo de recursos, tente as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub** , selecione **todos os recursos**, selecione o grupo de recursos com o seguinte formato AZUREBACKUPRG_ `<Geo>` _ `<number>` onde sua VM está localizada.

    ![Selecionar o grupo de recursos](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Selecione Grupo de recursos, o painel **visão geral** é exibido.
4. Selecione a opção **Mostrar tipos ocultos** opção para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato: AzureBackupRG_`<VMName>`_`<number>`.

    ![Selecionar a coleção de pontos de restauração](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Selecione **excluir** para limpar a coleção de pontos de restauração.
6. Tente a operação de backup novamente.

> [!NOTE]
 >Se o recurso (coleção de RP) tiver um grande número de pontos de restauração, a exclusão deles do portal poderá expirar e falhar. Trata-se de um problema de CRP conhecido, em que todos os pontos de restauração não são excluídos no tempo estipulado e a operação expira. No entanto, a operação de exclusão geralmente é realizada com sucesso após duas ou três tentativas.
