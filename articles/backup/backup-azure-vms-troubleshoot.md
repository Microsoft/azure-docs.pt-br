---
title: Solucionar problemas de erros de backup com máquinas virtuais do Azure
description: Solucionar problemas de backup e restauração de máquinas virtuais do Azure
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 4f95192aa2b3c5890a3cafbb442f9f15ebee9280
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465211"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solucionar problemas de backup de máquinas virtuais do Azure
Você pode solucionar os erros encontrados ao usar o backup do Azure com as informações listadas abaixo:

## <a name="backup"></a>Backup
Esta seção aborda a falha da operação de backup da máquina virtual do Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-a cópia dos dados de backup do cofre atingiu o tempo limite

Código de Erro: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: A cópia dos dados de backup do cofre atingiu o tempo limite

Isso pode ocorrer devido a erros de armazenamento transitórios ou IOPS de conta de armazenamento insuficiente para o serviço de backup transferir dados para o cofre dentro do período de tempo limite. Configure o backup de VM usando essas [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e repita a operação de backup.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-a VM não está em um estado que permita backups.

Código de Erro: UserErrorVmNotInDesirableState <br/>
Mensagem de erro: A VM não está em um estado que permite backups.<br/>

A operação de backup falhou porque a VM está em estado de falha. Para backup bem-sucedido, o estado da VM deve estar em execução, parado ou parado (desalocado).

* Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup.
*  Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed-falha ao congelar um ou mais pontos de montagem da VM para obter um instantâneo consistente do sistema de arquivos

Código de Erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Falha ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos.

* Verifique o estado do sistema de arquivos de todos os dispositivos montados usando o comando **tune2fs** , por exemplo,\| **tune2fs-l \\/dev/sdb1** . grep **FileSystem State**.
* Desmonte os dispositivos para os quais o estado do sistema de arquivos não foi limpo, usando o comando **umount** .
* Execute uma verificação de consistência do sistema de arquivos nesses dispositivos usando o comando **fsck** .
* Monte os dispositivos novamente e repita a operação de backup.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>Falha na instalação/operação de ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-Extension devido a um erro COM+

Código de Erro: ExtensionSnapshotFailedCOM <br/>
Mensagem de erro: A operação de instantâneo falhou devido a um erro de COM+

Código de Erro: ExtensionInstallationFailedCOM  <br/>
Mensagem de erro: Falha na instalação/operação da extensão devido a um erro COM+

Código de Erro: ExtensionInstallationFailedMDTC <br/>
Mensagem de erro: Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft <br/>

A operação de backup falhou devido a um problema com o aplicativo de **sistema com+** do serviço do Windows.  Para resolver esse problema, siga estas etapas:

* Tente iniciar/reiniciar o **aplicativo do sistema com+** do serviço do Windows (em um prompt de comando com privilégios elevados **– net start COMSysApp**).
* Verifique se os serviços de **Coordenador de transações distribuídas** estão sendo executados como conta de **serviço de rede** . Caso contrário, altere-o para executar como conta de **serviço de rede** e reinicie o **aplicativo de sistema com+** .
* Se não for possível reiniciar o serviço, reinstale **Coordenador de transações distribuídas** serviço seguindo as etapas abaixo:
    * Interrompa o serviço MSDTC
    * Abra um prompt de comando (cmd)
    * Execute o comando "MSDTC-Uninstall"
    * desfazer comando "MSDTC-install"
    * Inicie o serviço MSDTC
* Inicie o serviço Windows **COM+ System Application**. Depois que o **Aplicativo do Sistema COM+** for iniciado, dispare um trabalho de backup no portal do Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>Falha na operação ExtensionFailedVssWriterInBadState-snapshot porque os gravadores VSS estavam em um estado inadequado

Código de Erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: Falha na operação de instantâneo porque os gravadores VSS estavam em um estado inadequado.

Reinicie os gravadores VSS que estão em um estado inválido. Em um prompt de comandos com privilégios elevados, execute ```vssadmin list writers```. A saída contém todos os gravadores VSS e seus estados. Para cada gravador VSS com um estado que não seja **[1] Estável**, para reiniciar o gravador VSS, execute os seguintes comandos em um prompt de comando elevado:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-falha ao analisar a configuração da extensão de backup

Código de Erro: ExtensionConfigParsingFailure<br/>
Mensagem de erro: Falha ao analisar a configuração da extensão de backup.

Esse erro acontece por causa das permissões alteradas no diretório **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Execute o comando a seguir e verifique se as permissões no diretório MachineKeys são padrão:**icacls%systemdrive%\ProgramData\Microsoft\Crypto\RSA\MachineKeys**.

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
    * [Abra os certificados em um console de computador local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Em **Pessoal** > **Certificados**, exclua todos os certificados em que **Emitido Para** é o modelo de implantação clássico ou **Gerador de Certificado CRP do Microsoft Azure**.
3. Dispare um trabalho de backup da VM.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-o estado da extensão não é de apoio para a operação de backup

Código de Erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: O estado da extensão não é de apoio à operação de backup

A operação de backup falhou devido ao estado inconsistente da extensão de backup. Para resolver esse problema, siga estas etapas:

* Verifique se o Agente Convidado está instalado e respondendo
* No portal do Azure, vá para **Máquina Virtual** > **Todas as Configurações** > **Extensões**
* Selecione a extensão de backup VmSnapshot ou VmSnapshotLinux e clique em **Desinstalar**
* Depois de excluir a extensão de backup, repita a operação de backup
* A operação de backup subsequente instalará a nova extensão no estado desejado

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>Falha na operação ExtensionFailedSnapshotLimitReachedError-snapshot porque o limite de instantâneos foi excedido para alguns dos discos anexados

Código de Erro: ExtensionFailedSnapshotLimitReachedError <br/>
Mensagem de erro: A operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos anexados

A operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos anexados. Conclua as etapas de solução de problemas abaixo e repita a operação.

* Exclua o blob de disco-instantâneos que não são necessários. Tenha cuidado para não excluir o blob de disco, somente os blobs de instantâneo devem ser excluídos.
* Se a exclusão reversível estiver habilitada no armazenamento em disco da VM, configure a retenção de exclusão reversível de modo que os instantâneos existentes sejam menores do que o máximo permitido a qualquer momento.
* Se Azure Site Recovery estiver habilitado na VM de backup, execute o seguinte:

    * Verifique se o valor de **isanysnapshotfailed** está definido como false em/etc/Azure/VMBackup.conf
    * Agendar Azure Site Recovery em um momento diferente, de modo que ele não entra em conflito com a operação de backup.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>Falha na operação ExtensionFailedTimeoutVMNetworkUnresponsive-snapshot devido a recursos inadequados da VM.

Código de Erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensagem de erro: Falha na operação de instantâneo devido a recursos inadequados da VM.

Falha na operação de backup na VM devido a atraso em chamadas de rede ao executar a operação de instantâneo. Para resolver esse problema, siga a Etapa 1. Se o problema persistir, tente as etapas 2 e 3.

**Etapa 1**: Criar o instantâneo por meio do host

Em um prompt de comando com privilégios elevados (administrador), execute o comando abaixo:

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Isso garantirá que os instantâneos são executados por meio do host em vez do convidado. Tente a operação de backup novamente.

**Etapa 2**: Tente alterar o agendamento de backup para uma hora em que a VM esteja sob menos carga (menos CPU/IOps, etc.)

**Etapa 3**: Tente [aumentar o tamanho da VM](https://azure.microsoft.com/blog/resize-virtual-machines/) e repita a operação

## <a name="common-vm-backup-errors"></a>Erros comuns de backup da VM

| Detalhes do erro | Solução alternativa |
| ------ | --- |
| Código de Erro: 320001<br/> Mensagem de erro: Não foi possível executar a operação, pois a VM não existe mais. <br/> <br/> Código de Erro: 400094 <br/> Mensagem de erro: A máquina virtual não existe <br/> <br/>  Uma máquina virtual do Azure não foi encontrada.  |Esse erro ocorre quando a VM primária é excluída, mas a política de backup ainda parece para uma VM fazer backup. Para corrigir esse erro, use as etapas a seguir: <ol><li> Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos **nome do serviço de nuvem**,<br>**or**</li><li> Pare a proteção da máquina virtual excluindo ou não os dados de backup. Para obter mais informações, consulte [Interromper a proteção de máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| A VM está em Estado de Provisionamento com Falha: <br>Reinicie a VM e certifique-se de que a VM está em execução ou desligada. | Esse erro ocorre quando uma das falhas de extensão coloca a VM no estado de provisionamento com falha. Vá até a lista de extensões e veja se há uma extensão com falha, remova essa extensão e tente reiniciar a máquina virtual. Se todas as extensões estiverem em estado de execução, verifique se o serviço de agente da VM está em execução. Caso contrário, reinicie o serviço de agente da VM. |
|Código de Erro: UserErrorBCMPremiumStorageQuotaError<br/> Mensagem de erro: Não foi possível copiar o instantâneo da máquina virtual devido a espaço livre insuficiente na conta de armazenamento | No caso de VMs premium na pilha de backup de VM V1, copiamos o instantâneo para a conta de armazenamento. Essa etapa garante que o tráfego de gerenciamento de backup, que funciona no instantâneo, não limite o número de IOPS disponível para o aplicativo usando discos premium. <br><br>É recomendável que você aloque apenas 50 por cento, 17,5 TB, do espaço de conta de armazenamento total. Então, o serviço de Backup do Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados desse local copiado na conta de armazenamento para o cofre. |
| Falha ao instalar a extensão dos serviços de recuperação da Microsoft porque a máquina virtual não está em execução <br>O agente de VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro. |<ol> <li>Verifique se o agente da VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM esteja definido corretamente.</ol> Leia mais sobre como instalar o agente da VM e como validar a instalação do agente da VM. |
| A operação de instantâneo falhou com o erro de operação do Serviço de Cópias de Sombra de Volume (VSS) **Essa unidade essa bloqueada pela Criptografia de Unidade de Disco BitLocker. Você deve desbloquear esta unidade no Painel de Controle.** |Desative o BitLocker para todas as unidades na VM e verifique se o problema VSS é resolvido. |
| A VM não está em um estado que permite todos os backups. |<ul><li>Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup. <li> Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.  |
| O agente de VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito necessário e o agente de VM. Em seguida, reinicie a operação. |Leia mais sobre [Instalação do Agente da VM e como validar a Instalação do Agente da VM](#vm-agent). |
| Falha do backup ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos. | Siga estas etapas: <ul><li>Verifique o estado do sistema de arquivos de todos os dispositivos montados usando o comando **'tune2fs'** . Um exemplo é **tune2fs-l/dev/sdb1 \\** .\| grep **FileSystem State**. <li>Desmonte os dispositivos para os quais o estado do sistema de arquivos não é limpo usando o comando **'unmount'** . <li> Executar uma verificação de consistência do sistema de arquivos nesses dispositivos usando o comando **'fsck'** . <li> Montar os dispositivos novamente e tente fazer o backup.</ol> |
| A operação de instantâneo falhou devido a falha ao criar um canal de comunicação de rede segura. | <ol><li> Abra o Editor do Registro executando **regedit.exe** no modo elevado. <li> Identificar todas as versões do .NET Framework presente no seu sistema. Eles estão presentes na hierarquia de chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada .NET Framework presente na chave do registro, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| A operação de instantâneo falhou devido a falha ao instalar os Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012. | Navegue até C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion and install vcredist2012_x64.<br/>Verifique se o valor da chave do registro que permite a instalação do serviço está definido com o valor correto. Ou seja, defina o valor **inicial** em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** como **3** e não **4**. <br><br>Se você ainda estiver enfrentando problemas com a instalação, reinicie o serviço de instalação executando **MSIEXEC /UNREGISTER** seguido de **MSIEXEC /REGISTER** em um prompt de comandos com privilégios elevados.  |


## <a name="jobs"></a>Tarefas (Jobs)

| Detalhes do erro | Solução alternativa |
| --- | --- |
| Não há suporte para cancelamento para este tipo de trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |
| O trabalho não está em um estado cancelável: <br>Aguarde até que o trabalho seja concluído. <br>**or**<br> O trabalho selecionado não está em um estado cancelável: <br>Aguarde a conclusão do trabalho. |É provável que o trabalho esteja quase terminando. Aguarde até o trabalho ser concluído.|
| Backup não pode cancelar o trabalho porque ele não está em andamento: <br>O cancelamento tem suporte somente para trabalhos em andamento. Tente cancelar um trabalho em andamento. |Esse erro ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| Falha de backup para cancelar o trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar

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

#### <a name="windows-vms"></a>VMs do Windows

* Baixe e instale o [agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador para concluir a instalação.
* Para máquinas virtuais criadas usando o modelo de implantação clássico, [atualizar a propriedade de VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Azure Resource Manager.

#### <a name="linux-vms"></a>VMs do Linux

* Instale a versão mais recente do agente do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte o [Repositório do agente Linux](https://github.com/Azure/WALinuxAgent).
* Para VMs criadas usando o modelo de implantação clássico [use este blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para atualizar a propriedade de VM e verificar se o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Resource Manager.

### <a name="update-the-vm-agent"></a>Atualizar o agente de VM
#### <a name="windows-vms"></a>VMs do Windows

* Para atualizar o Agente da VM, reinstale os [Binários do agente da VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não ocorram operações de backup durante a atualização do Agente da VM.

#### <a name="linux-vms"></a>VMs do Linux

* Para atualizar o Agente da VM do Linux, siga as instruções no artigo, [Atualizar o Agente da VM do Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Sempre use o repositório de distribuição para atualizar o agente.

    Não baixe o código do agente do GitHub. Se o agente mais recente não estiver disponível para a sua distribuição, entre em contato com o suporte de distribuição para obter instruções para adquirir o agente mais recente. Também é possível verificar as informações mais recentes do [Agente do Linux do Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do agente de VM

Verifique a versão do agente da VM em VMs do Windows:

1. Entre na máquina virtual do Azure e navegue até a pasta **C:\WindowsAzure\Packages**. Você deve encontrar o arquivo **WaAppAgent.exe**.
2. Clique com o botão direito do mouse no arquivo e acesse **Propriedades**. Em seguida, selecione a guia **Detalhes**. O campo **Versão do Produto** deve ser 2.6.1198.718 ou mais recente.

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar Problemas de Instantâneo de VM
O backup de VM depende da emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos na execução da tarefa do instantâneo, pode resultar na falha do trabalho de backup. As condições a seguir podem causar a falha da tarefa do instantâneo:

- **O acesso à rede para o armazenamento está bloqueado usando NSG**. Saiba mais sobre como [estabelecer o acesso à rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity) para armazenamento usando a lista de IPs permitidos ou por meio de um servidor proxy.
- **VMs com backup do SQL Server configurado podem causar atraso na tarefa de instantâneo**. Por padrão, o backup da VM cria um backup completo do VSS em VMs do Windows. As VMs que executam o SQL Server, com backup do SQL Server configurado, podem sofrer atrasos de instantâneos. Se os atrasos de instantâneos causarem falhas de backup, defina a seguinte chave do registro:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **O status da VM é informado incorretamente porque a VM está desligada em RDP**. Se você usou a área de trabalho remota para desligar a máquina virtual, verifique se o status da VM no portal está correto. Se o status não estiver correto, use a opção **Desligar** no painel da VM do portal para desligar a VM.
- **Se mais de quatro VMs compartilharem o mesmo serviço de nuvem, distribua as VMs por várias políticas de backup**. Escalone os horários do backup para que não tenha mais de quatro backups de VM iniciados ao mesmo tempo. Tente separar as horas de início nas políticas por pelo menos uma hora.
- **A VM é executada no alto da CPU ou memória**. Se a máquina virtual é executada em alta utilização de memória ou uso da CPU, mais de 90 por cento, a tarefa de instantâneo será enfieirada e postergada. Eventualmente, atingirá o tempo limite. Se isso acontecer, tente um backup sob demanda.

## <a name="networking"></a>Rede
Como todas as extensões, a extensão de Backup precisa acessar a Internet pública para trabalhar. A falta de acesso à Internet Pública pode se manifestar de várias formas:

* A instalação da extensão pode falhar.
* As operações de backup (como snapshot de disco) podem falhar.
* A exibição do status da operação de backup pode falhar.

A necessidade de resolver endereços de internet pública é discutida [neste blog de Suporte do Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Verifique as configurações de DNS para a rede virtual e verifique se os URIs do Azure podem ser resolvidos.

Após a resolução de nomes ser feita corretamente, o acesso às IPs Azure também deve ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga uma destas etapas:

- Lista de permissões de intervalos de IP do datacenter do Azure:
   1. Obtenha a lista de [IPS do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para estar na lista de permissões.
   1. Desbloqueie os IPs usando o cmdlet [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute). Execute este cmdlet na VM do Azure em uma janela do PowerShell. Execute como administrador.
   1. Adicione regras ao NSG, se você tiver uma em vigor, para permitir o acesso aos IPs.
- Crie um caminho para a transmissão do tráfego HTTP:
   1. Se você tiver alguma restrição de rede no local, implante um servidor proxy HTTP para encaminhar o tráfego. Um exemplo é um grupo de segurança de rede. Consulte as etapas para implantar um servidor de proxy HTTP em [Estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Adicione regras ao NSG, se você tiver uma em vigor, para permitir o acesso à INTERNET do Proxy HTTP.

> [!NOTE]
> O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione. Se você precisar de um endereço IP privado estático, configure-o usando a plataforma do portal do Microsoft Azure ou o PowerShell. Verifique se a opção DHCP na VM está habilitada.
> Obtenha mais informações sobre como configurar um endereço IP estático por meio do PowerShell:
> - [Como adicionar um IP interno estático a uma VM existente](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
