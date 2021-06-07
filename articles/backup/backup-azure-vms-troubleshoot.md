---
title: Solucionar problemas de erros de backup com as VMs do Azure
description: Neste artigo, saiba como solucionar problemas encontrados com backup e restauração de máquinas virtuais do Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2d09081533cdb2de5ee97cb000e9844b41a85ac3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559359"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Solucionando problemas de falhas de backup em máquinas virtuais do Azure

Você pode solucionar os erros encontrados ao usar o Backup do Azure com as informações listadas abaixo:

## <a name="backup"></a>Backup

Esta seção aborda a falha na operação de backup da máquina virtual do Azure.

### <a name="basic-troubleshooting"></a>Solução básica de problemas

* Verifique se o agente de VM (agente de WA) é o [versão mais recente](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Verifique se a versão do sistema operacional da VM do Windows ou Linux tem suporte, consulte a [matriz de suporte de backup de VM IaaS](./backup-support-matrix-iaas.md).
* Verifique se outro serviço de backup não está em execução.
  * Para garantir que não haja problemas de extensão de captura instantânea, [desinstale as extensões para forçar o recarregamento e tente novamente o backup](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Verifique se a VM tem conectividade com a Internet.
  * Verifique se outro serviço de backup não está em execução.
* Em `Services.msc`, verifique se o serviço **Agente do Windows Azure Guest** está **em execução**. Se o serviço de **do agente convidado do Windows Azure** estiver ausente, instale-o de [fazer backup de VMs do Azure em um cofre dos serviços de recuperação](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* O **log de eventos** pode mostrar falhas de backup que são de outros produtos de backup, por exemplo, backup do Windows Server e não são devidos ao backup do Azure. Use as etapas a seguir para determinar se o problema é com o backup do Azure:
  * Se houver um erro com o **backup** de entrada na mensagem ou origem do evento, verifique se os backups de backup da VM IaaS do Azure foram bem-sucedidos e se um ponto de restauração foi criado com o tipo de instantâneo desejado.
  * Se o backup do Azure estiver funcionando, é provável que o problema tenha outra solução de backup.
  * Aqui está um exemplo de um erro Visualizador de Eventos 517 em que o backup do Azure estava funcionando bem, mas "Backup do Windows Server" estava falhando: ![ backup do Windows Server falhando](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Se o backup do Azure estiver falhando, procure o código de erro correspondente na seção erros comuns de backup da VM neste artigo.

## <a name="common-issues"></a>Problemas comuns

A seguir estão problemas comuns com falhas de backup em máquinas virtuais do Azure.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>O VMRestorePointInternalError-antivírus configurado na VM está restringindo a execução da extensão de backup

Código de erro: VMRestorePointInternalError

Se no momento do backup, o **Visualizador de eventos logs de aplicativo** exibirá o **nome do aplicativo com falha de mensagem: IaaSBcdrExtension.exe** em seguida, será confirmado que o antivírus configurado na VM está restringindo a execução da extensão de backup.
Para resolver esse problema, exclua os diretórios abaixo na configuração de antivírus e repita a operação de backup.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-a cópia dos dados de backup do cofre atingiu o tempo limite

Código de erro: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: A cópia dos dados de backup do cofre atingiu o tempo limite

Isso pode ocorrer devido a erros de armazenamento temporário ou IOPS da conta de armazenamento insuficiente para o serviço de backup transferir dados para o cofre dentro do período de tempo limite. Configure o backup da VM usando essas [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e tente novamente a operação de backup.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-a VM não está em um estado que permita backups

Código de erro: UserErrorVmNotInDesirableState <br/>
Mensagem de erro: A VM não está em um estado que permite que os backups.<br/>

A operação de backup falhou porque a VM está em estado de falha. Para ter um backup bem-sucedido, o estado da VM deve ser em execução, parado ou interrompido (desalocado).

* Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup.
* Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Falha ao congelar um ou mais pontos de montagem da VM para obter uma captura instantânea consistente do sistema de arquivos

Código de erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Falha ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos.

* Desmonte os dispositivos para os quais o estado do sistema de arquivos não foi limpo, usando o comando **umount** .
* Execute uma verificação de consistência do sistema de arquivos nesses dispositivos usando o comando **fsck**.
* Monte os dispositivos novamente e tente novamente a operação de backup.</ol>

Se não for possível desmontar os dispositivos, você poderá atualizar a configuração de backup da VM para ignorar determinados pontos de montagem. Por exemplo, se o ponto de montagem '/mnt/Resource ' não puder ser desmontado e causar falhas de backup da VM, você poderá atualizar os arquivos de configuração de backup da VM com a ```MountsToSkip``` propriedade da seguinte maneira.

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>Falha na instalação/operação de ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-Extension devido a um erro COM+

Código de erro: ExtensionSnapshotFailedCOM <br/>
Mensagem de erro: A operação de instantâneo falhou devido a um erro de COM+

Código de erro: ExtensionInstallationFailedCOM  <br/>
Mensagem de erro: Falha da instalação/operação da extensão devido a um erro de COM+.

Código de erro: ExtensionInstallationFailedMDTC <br/>
Mensagem de erro: Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft <br/>

A operação de backup falhou devido a um problema com o serviço Windows **aplicativo COM+** do sistema.  Para resolver esse problema, siga estas etapas:

* Tente iniciar/reiniciar o serviço do Windows **Aplicativo do sistema COM +**  (em um prompt de comando elevado  **- net start COMSysApp**).
* Verifique se **Coordenador de Transações Distribuídas serviço de** está sendo executado como **conta de** de serviço de rede. Caso contrário, altere-o para executar como **serviço de rede** conta e reinicie **aplicativo de sistema COM+** .
* Se não for possível reiniciar o serviço, reinstale **Coordenador de transações distribuídas** serviço seguindo as etapas abaixo:
  * Interrompa o serviço MSDTC
  * Abra um prompt de comando (cmd)
  * Executar o comando `msdtc -uninstall`
  * Executar o comando `msdtc -install`
  * Inicie o serviço MSDTC
* Inicie o serviço Windows **COM+ System Application**. Depois que o **Aplicativo do Sistema COM+** for iniciado, dispare um trabalho de backup no portal do Azure.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Falha na operação de captura instantânea porque os gravadores VSS estavam em um estado incorreto

Código de erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: A operação de captura instantânea falhou porque os gravadores VSS estavam em um estado incorreto.

Esse erro ocorre porque os gravadores VSS estavam em um estado inadequado. As extensões de backup do Azure interagem com os gravadores VSS para tirar instantâneos dos discos. Para resolver esse problema, siga estas etapas:

Etapa 1: Reinicie os gravadores VSS que estão em um estado inválido.

* Em um prompt de comandos com privilégios elevados, execute ```vssadmin list writers```.
* A saída contém todos os gravadores VSS e seus estados. Para cada gravador VSS com um estado que não seja **[1] Estável**, reinicie o serviço do gravador VSS respectivo.
* Para reiniciar o serviço, execute os seguintes comandos em um prompt de comando com privilégios elevados: 

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Reiniciar alguns serviços pode afetar o ambiente de produção. Certifique-se de que o processo de aprovação seja seguido e o serviço seja reiniciado no tempo de inatividade agendado.

Etapa 2: se a reinicialização dos gravadores VSS não resolver o problema, execute o comando a seguir em um prompt de comando elevado (como administrador) para impedir que os threads sejam criados para instantâneos de BLOB.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Etapa 3: se as etapas 1 e 2 não resolverem o problema, a falha poderá ser devido ao tempo limite dos gravadores VSS expirarem devido a um IOPS limitado.<br>

Para verificar, navegue até ***sistema e visualizador de eventos logs de aplicativo*** e verifique a seguinte mensagem de erro:<br>
*O tempo limite do provedor de cópia de sombra foi atingido ao reter gravações no volume sendo copiado em sombra. Isso provavelmente é devido à atividade excessiva no volume por um aplicativo ou um serviço do sistema. Tente novamente mais tarde quando a atividade no volume for reduzida.*<br>

Solução:

* Verifique se há possibilidades para distribuir a carga entre os discos de VM. Isso reduzirá a carga em discos únicos. Você pode [verificar a limitação de IOPS habilitando as métricas de diagnóstico no nível de armazenamento](/troubleshoot/azure/virtual-machines/performance-diagnostics#install-and-run-performance-diagnostics-on-your-vm).
* Altere a política de backup para executar backups fora do horário de pico, quando a carga na VM for a mais baixa.
* Atualize os discos do Azure para dar suporte a IOPs mais altos. [Saiba mais aqui](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – Falha na operação de instantâneo devido ao serviço VSS (cópia de sombra de volume) em estado inadequado

Código de erro: ExtensionFailedVssServiceInBadState <br/>
Mensagem de erro: falha na operação de instantâneo devido ao serviço VSS (cópia de sombra de volume) em estado inadequado.

Esse erro ocorre porque o serviço VSS estava em um estado inadequado. As extensões de backup do Azure interagem com o serviço VSS para tirar instantâneos dos discos. Para resolver esse problema, siga estas etapas:

Reinicie o serviço VSS (cópia de sombra de volume).

* Navegue até Services.msc e reinicie o "Serviço de cópia de sombra de volume".<br>
(ou)<br>
* Execute os seguintes comandos em um prompt de comandos com privilégios elevados:

 ```net stop VSS``` <br>
 ```net start VSS```

Se o problema persistir, reinicie a VM no tempo de inatividade agendado.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable-falha na criação da VM porque o tamanho da VM selecionado não está disponível

Código de erro: mensagem de erro UserErrorSkuNotAvailable: falha na criação da VM porque o tamanho da VM selecionado não está disponível.

Esse erro ocorre porque o tamanho da VM selecionado durante a operação de restauração é um tamanho sem suporte. <br>

Para resolver esse problema, use a opção [restaurar discos](./backup-azure-arm-restore-vms.md#restore-disks) durante a operação de restauração. Use esses discos para criar uma VM na lista de [tamanhos de VM com suporte disponíveis](./backup-support-matrix-iaas.md#vm-compute-support) usando [cmdlets do PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported-falha na criação da VM porque a solicitação de compra do Market Place não está presente

Código de erro: mensagem de erro UserErrorMarketPlaceVMNotSupported: falha na criação da VM porque a solicitação de compra do Market Place não está presente.

O backup do Azure dá suporte ao backup e à restauração de VMs que estão disponíveis no Azure Marketplace. Esse erro ocorre quando você está tentando restaurar uma VM (com uma configuração de plano/editor específica) que não está mais disponível no Azure Marketplace, [saiba mais aqui](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Para resolver esse problema, use a opção [restaurar discos](./backup-azure-arm-restore-vms.md#restore-disks) durante a operação de restauração e, em seguida, use o [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) ou [CLI do Azure](./tutorial-restore-disk.md) cmdlets para criar a VM com as informações mais recentes do Marketplace correspondentes à VM.
* Se o Publicador não tiver nenhuma informação do Marketplace, você poderá usar os discos de dados para recuperar seus dados e anexá-los a uma VM existente.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure- Falha na análise da configuração da extensão de backup

Código de erro: ExtensionConfigParsingFailure<br/>
Mensagem de erro: Falha na análise da configuração da extensão de backup.

Esse erro acontece por causa das permissões alteradas no diretório **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Execute o comando a seguir e verifique se as permissões no diretório **MachineKeys** são padrão: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

As permissões padrão são:

* Todos: (R,W)
* BUILTIN\Administratores: (F)

Se você vir as permissões no diretório **MachineKeys** que são diferentes dos padrões, siga estas etapas para corrigir as permissões, exclua o certificado e dispare o backup:

1. Corrija as permissões no diretório **MachineKeys**. Usando as Propriedades de Segurança do Explorer e as Configurações Avançadas de Segurança no diretório, redefina as permissões de volta para os valores padrão. Remova todos os objetos de usuário (exceto padrão) do diretório e assegure-se de que a permissão **Todos** tenha acesso especial para:

   * Listar pastas / ler dados
   * Atributos de leitura
   * Atributos de leitura estendidos
   * Criar arquivos/gravar dados
   * Criar pastas/anexar dados
   * Atributos de gravação
   * Atributos de gravação estendidos
   * Permissões de leitura
2. Exclua todos os certificados em que **Emitido Para** é o modelo de implantação clássico ou **Gerador de Certificado CRP do Microsoft Azure**:

   * [Abra os certificados em um console de computador local](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Em **Pessoal** > **Certificados**, exclua todos os certificados em que **Emitido Para** é o modelo de implantação clássico ou **Gerador de Certificado CRP do Microsoft Azure**.
3. Dispare um trabalho de backup da VM.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - O estado da extensão não dá suporte à operação de backup

Código de erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: O estado da extensão não dá suporte à operação de backup

A operação de backup falhou devido ao estado inconsistente da extensão de backup. Para resolver esse problema, siga estas etapas:

* Verifique se o Agente Convidado está instalado e respondendo
* No portal do Azure, vá para **Máquina Virtual** > **Todas as Configurações** > **Extensões**
* Selecione a extensão de backup VmSnapshot ou VmSnapshotLinux e selecione **desinstalar**.
* Depois de excluir a extensão de backup, repita a operação de backup
* A operação de backup subsequente instalará a nova extensão no estado desejado

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - A operação de captura instantânea falhou porque o limite de captura instantânea foi excedido para alguns dos discos conectados

Código de erro: ExtensionFailedSnapshotLimitReachedError  <br/>
Mensagem de erro: Falha na operação de instantâneo porque o limite de instantâneos foi excedido em alguns dos discos anexados

A operação de instantâneo falhou porque o limite de instantâneos foi excedido em alguns dos discos anexados. Conclua as seguintes etapas de solução de problemas e repita a operação.

* Exclua o blob de disco-instantâneos que não são necessários. Tenha cuidado para não excluir blobs de disco. Somente blobs de instantâneo devem ser excluídos.
* Se a exclusão reversível estiver habilitada no armazenamento em disco da VM, configure a retenção de exclusão reversível para que os instantâneos existentes sejam menores do que o máximo permitido a qualquer momento.
* Se Azure Site Recovery estiver habilitado na VM de backup, execute as etapas abaixo:

  * Verifique se o valor de **isanysnapshotfailed** está definido como false em /etc/azure/vmbackup.conf
  * Agendar Azure Site Recovery em um momento diferente, portanto, ele não entra em conflito com a operação de backup.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>Falha na operação ExtensionFailedTimeoutVMNetworkUnresponsive-snapshot devido a recursos inadequados da VM

Código de erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensagem de erro: Falha na operação de instantâneo devido a recursos inadequados da VM.

A operação de backup na VM falhou devido ao atraso nas chamadas de rede ao executar a operação de captura instantânea. Para resolver esse problema, siga a Etapa 1. Se o problema persistir, tente as etapas 2 e 3.

**Etapa 1**: Criar o instantâneo por meio do host

Em um prompt de comando elevado (administrador), execute o seguinte comando:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Isso garantirá que os instantâneos são executados por meio do host em vez do convidado. Tente a operação de backup novamente.

**Etapa 2**: Tente alterar o agendamento de backup para uma hora em que a VM está sob menos carga (como menos CPU ou IOPS)

**Etapa 3**: Tente [aumentar o tamanho da VM](../virtual-machines/windows/resize-vm.md) e repita a operação

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound-não foi possível executar a operação porque a VM não existe mais/400094, BCMV2VMNotFound-a máquina virtual não existe/uma máquina virtual do Azure não foi encontrada

Erro de código: 320001, ResourceNotFound <br/> Mensagem de erro: Não foi possível executar a operação, pois a VM não existe mais. <br/> <br/> Erro de código: 400094, BCMV2VMNotFound <br/> Mensagem de erro: A máquina virtual não existe <br/>
Uma máquina virtual do Azure não foi encontrada.

Esse erro ocorre quando a VM primária é excluída, mas a política de backup ainda parece para uma VM fazer backup. Para corrigir esse erro, use as etapas a seguir:

* Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos **nome do serviço de nuvem**,<br>ou
* Pare a proteção da máquina virtual excluindo ou não os dados de backup. Para obter mais informações, consulte [Interromper a proteção de máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError-não foi possível copiar o instantâneo da máquina virtual devido a espaço livre insuficiente na conta de armazenamento

Erro de código: UserErrorBCMPremiumStorageQuotaError<br/> Mensagem de erro: Não foi possível copiar o instantâneo da máquina virtual porque o espaço livre era insuficiente na conta de armazenamento

 No caso de VMs premium na pilha de backup de VM V1, copiamos o instantâneo para a conta de armazenamento. Essa etapa garante que o tráfego de gerenciamento de backup, que funciona no instantâneo, não limite o número de IOPS disponível para o aplicativo usando discos premium. <br><br>É recomendável que você aloque apenas 50 por cento, 17,5 TB, do espaço de conta de armazenamento total. Então, o serviço de Backup do Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados desse local copiado na conta de armazenamento para o cofre.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline-falha ao instalar a extensão dos serviços de recuperação da Microsoft porque a máquina virtual não está em execução

Erro de código: 380008, AzureVmOffline <br/> Mensagem de erro: Falha ao instalar a extensão dos Serviços de Recuperação da Microsoft, pois uma máquina virtual não está em execução

O agente de VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro. <br> <ol> <li>Verifique se o agente da VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM esteja definido corretamente.</ol> Leia mais sobre como instalar o agente da VM e como validar a instalação do agente da VM.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError-falha na operação de instantâneo com o erro de operação de Serviço de Cópias de Sombra de Volume (VSS)

Erro de código: ExtensionSnapshotBitlockerError <br/> Mensagem de erro: falha na operação de instantâneo com o erro de operação de Serviço de Cópias de Sombra de Volume (VSS) **esta unidade está bloqueada pelo criptografia de unidade de disco BitLocker. Você deve desbloquear esta unidade no painel de controle.**

Desative o BitLocker para todas as unidades na VM e verifique se o problema VSS é resolvido.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState-a VM não está em um estado que permite backups

Erro de código: VmNotInDesirableState <br/> Mensagem de erro:  A VM não está em um estado que permite todos os backups.

* Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup.
* Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

* O agente de VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito necessário e o agente de VM. Em seguida, reinicie a operação. | Leia mais sobre [a instalação do agente de VM e como validar a instalação do agente de VM](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork-falha na operação de instantâneo devido à falha ao criar um canal de comunicação de rede segura

Erro de código: ExtensionSnapshotFailedNoSecureNetwork <br/> Mensagem de erro: A operação de instantâneo falhou devido a falha ao criar um canal de comunicação de rede segura.

* Abra o Editor do Registro executando **regedit.exe** no modo elevado.
* Identificar todas as versões do .NET Framework presente no seu sistema. Eles estão presentes na hierarquia de chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**.
* Para cada .NET Framework presente na chave do registro, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure-falha na operação de instantâneo devido à falha na instalação do Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012

Erro de código: ExtensionVCRedistInstallationFailure <br/> Mensagem de erro: A operação de instantâneo falhou devido a falha ao instalar os Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012.

* Navegue até `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` e instale vcredist2013_x64.<br/>Certifique-se de que o valor da chave do registro permite que a instalação desse serviço seja definida como o valor correto. Ou seja, defina o valor **Iniciar** em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** como **3** e não **4**. <br><br>Se você ainda estiver enfrentando problemas com a instalação, reinicie o serviço de instalação executando **MSIEXEC /UNREGISTER** seguido de **MSIEXEC /REGISTER** em um prompt de comandos com privilégios elevados.
* Verifique o log de eventos para verificar se você está percebendo problemas relacionados ao acesso. Por exemplo:  *Produto: Tempo de C++ execução mínimo do Microsoft Visual 2013 x64-12.0.21005--Error 1401. não foi possível criar a chave: Software\Classes  Erro do sistema 5.  Verifique se você tem acesso suficiente a essa chave ou entre em contato com a equipe de suporte.* <br><br> Verifique se a conta de administrador ou de usuário tem permissões suficientes para atualizar a chave do registro **HKEY_LOCAL_MACHINE \SOFTWARE\Classes**. Forneça permissões suficientes e reinicie o agente convidado do Windows Azure.<br><br> <li> Se você tiver produtos antivírus em vigor, verifique se eles têm as regras de exclusão corretas para permitir a instalação.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy - uma política inválida foi configurada na VM impedindo a operação de Instantâneo

Erro de código:  UserErrorRequestDisallowedByPolicy <BR> Mensagem de erro: Uma política inválida foi configurada na VM impedindo a operação de Instantâneo.

Se você tiver um Azure Policy que [governa as marcas em seu ambiente](../governance/policy/tutorials/govern-tags.md), considere alterar a política de um [efeito de negação de](../governance/policy/concepts/effects.md#deny) para um [de efeito modificar](../governance/policy/concepts/effects.md#modify)ou crie o grupo de recursos manualmente de acordo com o esquema de nomenclatura do [exigido pelo](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)de backup do Azure.

## <a name="jobs"></a>Trabalhos

| Detalhes do erro | Solução alternativa |
| --- | --- |
| Não há suporte para cancelamento para este tipo de trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |
| O trabalho não está em um estado cancelável: <br>Aguarde até que o trabalho seja concluído. <br>**or**<br> O trabalho selecionado não está em um estado cancelável: <br>Aguarde a conclusão do trabalho. |É provável que o trabalho esteja quase terminando. Aguarde até o trabalho ser concluído.|
| Backup não pode cancelar o trabalho porque ele não está em andamento: <br>O cancelamento tem suporte somente para trabalhos em andamento. Tente cancelar um trabalho em andamento. |Esse erro ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| Falha de backup para cancelar o trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar

### <a name="disks-appear-offline-after-file-restore"></a>Os discos aparecem offline após a restauração do arquivo

Se, após a restauração, você observar que os discos estão offline, então:

* Verifique se o computador onde o script é executado atende aos requisitos do sistema operacional. [Saiba mais](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Verifique se você não está restaurando para a mesma fonte, [saiba mais](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound-Restore falhou porque o instantâneo da VM não foi encontrado

Código de erro: UserErrorInstantRpNotFound <br>
Mensagem de erro: falha na restauração porque o instantâneo da VM não foi encontrado. O instantâneo pode ter sido excluído, verifique.<br>

Esse erro ocorre quando você está tentando restaurar de um ponto de recuperação que não foi transferido para o cofre e foi excluído na fase de instantâneo. 
<br>
Para resolver esse problema, tente restaurar a VM de um ponto de restauração diferente.<br>

#### <a name="common-errors"></a>Erros comuns 
| Detalhes do erro | Solução alternativa |
| --- | --- |
| A restauração falhou com erro interno de nuvem. |<ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Se **Endereço** estiver configurado, as configurações de DNS são configuradas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com **ReservedIP** e as VMs existentes no serviço de nuvem estão no estado parado. Você pode verificar que um serviço de nuvem tem um IP reservado usando os seguintes cmdlets do PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem: <ul><li>Máquinas virtuais sob configuração do balanceador de carga, interno e externo.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com várias NICs. </ul><li>Selecione um novo serviço de nuvem na interface do usuário ou veja [Considerações de restauração](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já está em uso: <br>Especifique um nome DNS diferente e tente novamente. |O nome DNS aqui se refere ao nome do serviço de nuvem, geralmente terminando em **.cloudapp.net**. Esse nome precisa ser exclusivo. Se você obter esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br> Este erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell obtém êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| A configuração da rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está usando um IP reservado que não corresponde à configuração da máquina virtual que está sendo restaurada: <br>Especifique um serviço de nuvem diferente que não está usando um IP reservado. Ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| O serviço de nuvem atingiu seu limite no número de pontos de extremidade de entrada: <br>Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| O cofre dos Serviços de Recuperação e a conta de armazenamento de destino estão em duas regiões diferentes: <br>Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure como seu cofre do Recovery Services. |Nenhum |
| Não há suporte para a conta de armazenamento especificada para a operação de restauração: <br>Há suporte para apenas para contas Basic ou Standard com configurações de replicação geográfica redundante localmente. Selecione uma conta de armazenamento com suporte. |Nenhum |
| O tipo de conta de armazenamento especificada para a operação de restauração não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauração está online. |Esse erro pode ocorrer devido a um erro transitório no Armazenamento do Microsoft Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| A cota do grupo de recursos foi atingida: <br>Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede que existe. |Nenhum |
| O serviço de Backup não tem autorização para acessar recursos em sua assinatura. |Para resolver esse erro, primeiro restaure discos, usando as etapas em [Restaurar discos com backup](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, use as etapas do PowerShell em [Criar uma VM de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Backup ou restauração demoram

Se o backup levar mais de 12 horas ou a restauração levar mais de 6 horas, examine as [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e as [considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente de VM

### <a name="set-up-the-vm-agent"></a>Configuração do agente de VM

Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente.

#### <a name="windows-vms---set-up-the-agent"></a>VMs do Windows – configurar o agente

* Baixe e instale o [agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador para concluir a instalação.
* Para máquinas virtuais criadas usando o modelo de implantação clássico, [atualizar a propriedade de VM](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) para indicar que o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Azure Resource Manager.

#### <a name="linux-vms---set-up-the-agent"></a>VMs do Linux – configurar o agente

* Instale a versão mais recente do agente do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte o [Repositório do agente Linux](https://github.com/Azure/WALinuxAgent).
* Para VMs criadas usando o modelo de implantação clássico, [atualize a propriedade da VM](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) e verifique se o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Resource Manager.

### <a name="update-the-vm-agent"></a>Atualizar o agente de VM

#### <a name="windows-vms---update-the-agent"></a>VMs do Windows – atualizar o agente

* Para atualizar o Agente da VM, reinstale os [Binários do agente da VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não ocorram operações de backup durante a atualização do Agente da VM.

#### <a name="linux-vms---update-the-agent"></a>VMs do Linux – atualizar o agente

* Para atualizar o Agente da VM do Linux, siga as instruções no artigo, [Atualizar o Agente da VM do Linux](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Sempre use o repositório de distribuição para atualizar o agente.

    Não baixe o código do agente do GitHub. Se o agente mais recente não estiver disponível para a sua distribuição, entre em contato com o suporte de distribuição para obter instruções para adquirir o agente mais recente. Também é possível verificar as informações mais recentes do [Agente do Linux do Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do agente de VM

Verifique a versão do agente da VM em VMs do Windows:

1. Entre na máquina virtual do Azure e navegue até a pasta **C:\WindowsAzure\Packages**. Você deve encontrar o arquivo **WaAppAgent.exe**.
2. Clique com o botão direito do mouse no arquivo e acesse **Propriedades**. Em seguida, selecione a guia **Detalhes**. O campo **Versão do Produto** deve ser 2.6.1198.718 ou mais recente.

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar Problemas de Instantâneo de VM

O backup de VM depende da emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos na execução da tarefa do instantâneo, pode resultar na falha do trabalho de backup. As condições a seguir podem causar a falha da tarefa do instantâneo:

* **VMs com backup do SQL Server configurado podem causar atraso na tarefa de instantâneo**. Por padrão, o backup da VM cria um backup completo do VSS em VMs do Windows. As VMs que executam o SQL Server, com backup do SQL Server configurado, podem sofrer atrasos de instantâneos. Se os atrasos de instantâneos causarem falhas de backup, defina a seguinte chave do registro:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **O status da VM é informado incorretamente porque a VM está desligada em RDP**. Se você usou a área de trabalho remota para desligar a máquina virtual, verifique se o status da VM no portal está correto. Se o status não estiver correto, use a opção **Desligar** no painel da VM do portal para desligar a VM.
* **Se mais de quatro VMs compartilharem o mesmo serviço de nuvem, distribua as VMs por várias políticas de backup**. Escalone os horários do backup para que não tenha mais de quatro backups de VM iniciados ao mesmo tempo. Tente separar as horas de início nas políticas por pelo menos uma hora.
* **A VM é executada no alto da CPU ou memória**. Se a máquina virtual é executada em alta utilização de memória ou uso da CPU, mais de 90 por cento, a tarefa de instantâneo será enfieirada e postergada. Eventualmente, atingirá o tempo limite. Se isso acontecer, tente um backup sob demanda.

## <a name="networking"></a>Rede

O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione. Se você precisar de um endereço IP privado estático, configure-o usando a plataforma do portal do Microsoft Azure ou o PowerShell. Verifique se a opção DHCP na VM está habilitada.
Obtenha mais informações sobre como configurar um endereço IP estático por meio do PowerShell:

* [Como adicionar um IP interno estático a uma VM existente](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)