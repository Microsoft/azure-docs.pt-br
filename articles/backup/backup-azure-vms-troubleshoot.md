---
title: "Solucionar problemas de erros de backup com máquinas virtuais do Azure | Microsoft Docs"
description: "Solucionar problemas de backup e restauração de máquinas virtuais do Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: trinadhk;markgal;jpallavi;
ms.openlocfilehash: 096c97f4cb41ff8df2e646f59dbc0bf845721ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solucionar problemas de backup de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Cofre dos serviços de recuperação](backup-azure-vms-troubleshoot.md)
> * [Cofre de backup](backup-azure-vms-troubleshoot-classic.md)
>
>

Você pode solucionar os erros encontrados enquanto usa o Backup do Azure com as informações listadas na tabela a seguir.

## <a name="backup"></a>Backup

### <a name="error-the-specified-disk-configuration-is-not-supported"></a>Erro: Não há suporte para a Configuração de Disco especificada

Atualmente, o Backup do Azure não dá suporte a tamanhos de disco [maiores que 1.023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Se você tiver discos maiores que 1 TB, [conecte novos discos](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) que sejam inferiores a 1 TB <br>
- Em seguida, copie os dados do disco maior que 1 TB para o(s) disco(s) recém-criado(s) de tamanho menor que 1 TB. <br>
- Certifique-se de que todos os dados foram copiados e remova os discos maiores que 1 TB
- Inicie o backup.

| Detalhes do erro | Solução alternativa |
| --- | --- |
| Não foi possível executar a operação, pois a VM não existe mais. - Pare a proteção da máquina virtual sem excluir os dados de backup. Mais detalhes em http://go.microsoft.com/fwlink/?LinkId=808124 |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li> Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem],<br>(OU)</li><li> Pare a proteção da máquina virtual excluindo ou não os dados de backup. [Mais detalhes:](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Houve falha na operação de instantâneo por falta de conectividade à rede na máquina virtual – verifique se a VM tem acesso à rede. Para que o instantâneo seja bem-sucedido, coloque os intervalos de IP do data center do Azure na lista de permissões ou configure um servidor proxy para acesso à rede. Para obter mais detalhes, consulte http://go.microsoft.com/fwlink/?LinkId=800034. Se já estiver usando um servidor proxy, verifique se as configurações dele foram definidas corretamente | Este erro é gerado quando você nega a conectividade com a Internet de saída na máquina virtual. A conectividade com a Internet é necessária para que a extensão de instantâneo da VM faça um instantâneo dos discos subjacentes da máquina virtual. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) sobre como resolver falhas de instantâneo decorrentes do acesso à rede bloqueado. |
| O agente de VM não consegue se comunicar com o Serviço de Backup do Azure. - Verifique se a VM tem conectividade com a rede e se o agente da VM está em execução e é o mais recente. Para obter mais informações, consulte http://go.microsoft.com/fwlink/?LinkId=800034 |Esse erro é gerado se há um problema com o agente de VM ou se o acesso à rede para a infraestrutura do Azure está bloqueado de alguma forma. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) sobre depuração de problemas de instantâneo de VM.<br> Se o agente de VM não está causando problemas, reinicie a máquina virtual. Às vezes, um estado incorreto de VM pode causar problemas e reiniciar a VM redefine esse "estado defeituoso". |
| A VM está em Estado de Falha no Provisionamento – reinicie a VM e verifique se a VM está no estado Em execução ou Desligado para backup | Isso ocorre quando uma das falhas de extensão faz com que a VM fique no estado de falha no provisionamento. Vá até a lista de extensões e veja se há uma extensão com falha, remova essa extensão e tente reiniciar a máquina virtual. Se todas as extensões estiverem em estado de execução, verifique se o serviço de agente da VM está em execução. Caso contrário, reinicie o serviço de agente da VM. | 
| Falha na operação de extensão VMSnapshot para os discos gerenciados – tente novamente a operação de backup. Se o problema se repetir, siga as instruções em “http://go.microsoft.com/fwlink/?LinkId=800034”. Se ocorrer uma nova falha, entre em contato com o suporte da Microsoft | Esse erro ocorre quando o serviço de Backup do Azure falha ao disparar um instantâneo. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) sobre como depurar problemas de instantâneo de VM. |
| Não foi possível copiar o instantâneo da máquina virtual, porque o espaço livre na conta de armazenamento é insuficiente – verifique se a conta de armazenamento tem espaço livre equivalente aos dados existentes nos discos de armazenamento premium anexados à máquina virtual | No caso de VMs premium, copiamos o instantâneo para a conta de armazenamento. Isso é feito para garantir que o tráfego de gerenciamento de backup, que funciona no instantâneo, não limite o número de IOPS disponível para o aplicativo usando discos premium. A Microsoft recomenda que você aloque apenas 50% do espaço de conta de armazenamento total, para que o serviço de Backup do Azure possa copiar o instantâneo para a conta de armazenamento e transferir dados desse local copiado na conta de armazenamento para o cofre. | 
| Não é possível executar a operação porque o agente de VM não está respondendo |Esse erro é gerado se há um problema com o agente de VM ou se o acesso à rede para a infraestrutura do Azure está bloqueado de alguma forma. Para VMs Windows, verifique o status do serviço do agente de VM em serviços e se o agente é exibido em programas no painel de controle. Tente remover o programa do painel de controle e reinstalar o agente, conforme mencionado [abaixo](#vm-agent). Depois de reinstalar o agente, dispare um backup ad hoc para confirmar. |
| Falha na operação de extensão dos serviços de recuperação. -Certifique-se de que o agente de máquina virtual mais recente esteja presente na máquina virtual e o serviço do agente esteja em execução. Tente novamente a operação de backup e, se ele falhar, entre em contato com o suporte da Microsoft. |Esse erro é disparado quando o agente da VM está desatualizado. Consulte a seção "Atualização do agente da VM" logo abaixo para atualizar o agente da VM. |
| A máquina virtual não existe. - Certifique-se de que a máquina virtual exista ou selecione uma máquina virtual diferente. |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li> Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem],<br>(OU)<br></li><li>Pare a proteção da máquina virtual sem excluir os dados de backup. [Mais detalhes:](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Falha na execução do comando. - Outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente |Está em execução um backup existente para a máquina virtual e não é possível iniciar um novo trabalho enquanto o trabalho existente estiver sendo executado. |
| A cópia de VHDs do Cofre de backup atingiu o tempo limite - tente a operação novamente dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft. | Isso ocorre se houver um erro transitório no lado de armazenamento ou se o serviço de backup não estiver obtendo IOPS suficientes da conta de armazenamento que hospeda a VM para transferir dados dentro do período de espera no cofre. Verifique se você seguiu [Práticas recomendadas](backup-azure-vms-introduction.md#best-practices) ao configurar o backup. Tente mover a VM para uma conta de armazenamento diferente que não está carregada e tente efetuar novamente a cópia de segurança.|
| Falha no backup com um erro interno - tente novamente a operação dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft |Você pode obter esse erro por 2 motivos:  <ol><li> Há um problema temporário ao acessar o armazenamento de VM. Verifique o [Status do Azure](https://azure.microsoft.com/en-us/status/) para ver se há qualquer problema ativo relacionado a computação, armazenamento ou rede na região. Repita o trabalho de backup depois que o problema for resolvido. <li>A VM original foi excluída e, portanto, o ponto de recuperação não pode ser usado. Para manter os dados de backup de uma VM excluída, mas remover os erros de backup: desproteja a VM e escolha a opção para manter os dados. Essa ação interrompe o trabalho de backup agendado e as mensagens de erro recorrentes. |
| Falha ao instalar a extensão dos Serviços de Recuperação do Azure no item selecionado - o agente da VM é um pré-requisito para a Extensão dos Serviços de Recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro |<ol> <li>Verifique se o agente de VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM tenha sido definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre como instalar o agente da VM e como validar a instalação do agente da VM. |
| Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft |Isso geralmente significa que o serviço COM+ não está em execução. Entre em contato com o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Falha na operação de instantâneo. Erro de operação do VSS "Esta unidade está bloqueada pela Criptografia de Unidade de Disco BitLocker. Você deve desbloquear esta unidade no Painel de Controle. |Desative o BitLocker para todas as unidades na VM e observe se o problema VSS é resolvido |
| A VM não está em um estado que permite que os backups. |<ul><li>Verifique se a máquina virtual está em um estado transitório entre execução e desligar, para baixo. Se for, aguarde o estado da VM seja uma delas e disparar backup novamente. <li> Se a VM for uma VM do Linux e se usar o módulo de kernel [Linux com Segurança Reforçada], você precisará excluir o caminho do Agente Linux (_/var/lib/waagent_) de política de segurança para garantir que a extensão de backup seja instalada.  |
| Máquina Virtual do Azure não encontrada. |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li>Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem], <br>(OU) <li> Desative a proteção para esta VM para que os trabalhos de backup não sejam criados. </ol> |
| O agente de máquina virtual não está presente na máquina virtual - instale qualquer pré-requisito necessário e o agente de VM e, depois, reinicie a operação. |[Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |
| Falha na operação de instantâneo devido a Gravadores VSS em estado inválido |Você precisa reiniciar os gravadores VSS (Serviço de Cópias de Sombra de Volume) que estão em estado inválido. Para fazer isso, em um prompt de comandos com privilégios elevados, execute _vssadmin list writers_. A saída contém todos os gravadores VSS e seus estados. Para cada gravador VSS cujo estado não for "[1] estável", reinicie o gravador VSS executando os comandos a seguir em um prompt de comandos com privilégios elevados:<br> _net stop serviceName_ <br> _net start serviceName_|
| Falha na operação de instantâneo devido a uma falha de análise da configuração |Isso acontece devido a permissões alteradas no diretório MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Execute o comando abaixo e verifique se as permissões no diretório MachineKeys são as permissões padrão:<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> As permissões padrão são:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Se você vir permissões no diretório MachineKeys diferentes do padrão, siga etapas a seguir para corrigir as permissões, excluir o certificado e disparar o backup.<ol><li>Corrigir as permissões no diretório MachineKeys.<br>Usando as Propriedades de Segurança do Explorer e as Configurações de Segurança Avançadas no diretório, redefina as permissões para os valores padrão, remova qualquer objeto de usuário adicional (diferente do padrão) do diretório e verifique se as permissões 'Everyone' tinham acesso especial para:<br>– Listar pastas / ler dados <br>– Atributos de leitura <br>– Atributos de leitura estendidos <br>– Criar arquivos / gravar dados <br>– Criar pastas / acrescentar dados<br>– Atributos de gravação<br>– Atributos de gravação estendidos<br>– Permissões de leitura<br><br><li>Exclua todos os certificados com o campo “Emitido Para” = “Gerenciamento de Serviços do Microsoft Azure para Extensões” ou “Gerador de Certificados CRP do Microsoft Azure”.<ul><li>[Abra o console de certificados (computador Local)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Exclua todos os certificados (em Pessoal -> Certificados) com o campo “Emitido Para” = “Gerenciamento de Serviços do Microsoft Azure para Extensões” ou “Gerador de Certificados CRP do Microsoft Azure”.</ul><li>Disparar Backup de VM. </ol>|
| Falha na validação pois a máquina virtual é criptografada somente com BEK. Os backups podem ser habilitados somente para máquinas virtuais criptografadas com BEK e KEK. |A máquina virtual deve ser criptografado usando a Chave de Criptografia BitLocker e a Chave de Criptografia. Depois disso, o backup deve ser habilitado. |
| O Serviço de Backup do Azure não tem permissões suficientes para o cofre da chave para Backup de máquinas virtuais criptografadas. |Essas permissões podem ser fornecidas pelo serviço de backup no PowerShell usando as etapas mencionadas na seção **Habilitar Backup** da [documentação do PowerShell](backup-azure-vms-automation.md). |
|Falha na instalação da extensão do instantâneo com erro – COM+ não pôde se comunicar com o Coordenador de Transações Distribuídas da Microsoft | Tente iniciar o serviço Windows "Aplicativo de Sistema COM+" (em um prompt de comandos com privilégios elevados – _net start-COMSysApp_). <br>Se ele falhar ao iniciar, siga etapas a seguir:<ol><li> Valide que a conta de Logon do serviço "Coordenador de Transações Distribuídas" é "Serviço de Rede". Se não for, altere-a para "Serviço de Rede", reinicie este serviço e, em seguida, tente iniciar o serviço "Aplicativo de Sistema COM+".<li>Se ele ainda falhar ao iniciar, desinstale/instale serviço "Coordenador de Transações Distribuídas" seguindo as etapas a seguir:<br> – Interrompa o serviço MSDTC<br> – Abra um prompt de comando (cmd) <br> – Execute o comando “msdtc -uninstall” <br> – Execute o comando “msdtc -install” <br> – Inicie o serviço MSDTC<li>Inicie o serviço Windows "Aplicativo de Sistema COM+" e, depois de iniciado, dispare o backup do portal.</ol> |
|  A operação de instantâneo falhou devido a um erro de COM+ | A ação recomendada é reiniciar o serviço Windows “Aplicativo do Sistema COM+” (em um prompt de comandos com privilégios elevados – _net start COMSysApp_). Se o problema persistir, reinicie a VM. Se a reinicialização da VM não funcionar, tente [remover a Extensão VMSnapshot](https://docs.microsoft.com/en-us/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) e disparar o backup manualmente. |
| Falha ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos | Use as seguintes etapas: <ol><li>Verifique o estado do sistema de arquivos de todos os dispositivos montados usando o comando _'tune2fs'_.<br> Por exemplo: tune2fs -l /dev/sdb1 \| grep "Filesystem state" <li>Desmonte os dispositivos para qual o estado do sistema de arquivos não é limpo usando o comando _'unmount'_ <li> Execute a verificação de FileSystemConsistency nesses dispositivos usando o comando _'fsck'_ <li> Montar os dispositivos novamente e tente fazer o backup.</ol> |
| Falha na operação de instantâneo devido a falha na criação do canal de comunicação de rede segura | <ol><Li> Abra o Editor do Registro executando regedit.exe no modo elevado. <li> Identifique todas as versões do .NetFramework presentes no sistema. Eles estão presentes na hierarquia de chave do Registro "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" <li> Para cada .NetFramework presente na chave do Registro, adicione a seguinte chave: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Falha na operação de instantâneo devido a uma falha na instalação de Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012 | Navegue até C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion and install vcredist2012_x64. Certifique-se de que o valor de chave do Registro para permitir a instalação desse serviço esteja definida com o valor correto, ou seja, que o valor da chave do Registro _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ esteja definido como 3 e não como 4. Se você ainda estiver enfrentando problemas com a instalação, reinicie o serviço de instalação executando _MSIEXEC /UNREGISTER_ seguido de _MSIEXEC /REGISTER_ em um prompt de comandos com privilégios elevados.  |


## <a name="jobs"></a>Trabalhos
| Detalhes do erro | Solução alternativa |
| --- | --- |
| Não há suporte para cancelamento deste tipo de trabalho - Aguarde até que o trabalho seja concluído. |Nenhum |
| O trabalho não está em um estado cancelável - aguarde até que o trabalho seja concluído. <br>OU<br> o trabalho selecionado não está em um estado cancelável - aguarde até que o trabalho seja concluído. |É muito provável que o trabalho esteja quase concluído. Aguarde até o trabalho ser concluído.|
| Não é possível cancelar o trabalho porque ele não está em andamento - há suporte para cancelamento apenas de trabalhos que estão em andamento. Tente cancelar um trabalho em andamento. |Isso ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| Falha ao cancelar o trabalho - Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar
| Detalhes do erro | Solução alternativa |
| --- | --- |
| A restauração falhou com erro interno de nuvem |<ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar  <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se houver um endereço configurado, isso significa que as configurações de DNS estão definidas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com ReservedIP e as VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar se um serviço de nuvem tem IP reservado usando os seguintes cmdlets do Powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem. <br>– Máquinas virtuais sob configuração do balanceador de carga (interno e externo)<br>– Máquinas virtuais com vários IPs reservados<br>– Máquinas virtuais com várias NICs<br>Selecione um novo serviço de nuvem na interface do usuário ou confira as [considerações sobre a restauração](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já existe - especifique um nome DNS diferente e tente novamente. |O nome DNS aqui se refere ao nome do serviço de nuvem (geralmente terminando em .cloudapp.net). Isso precisa ser exclusivo. Se você encontrar esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br> Este erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell terá êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| A configuração de rede virtual especificada não está correta - especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está usando um IP reservado, o que não corresponde à configuração da máquina virtual que está sendo restaurada. Especifique um serviço de nuvem diferente que não esteja usando o IP reservado ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| O serviço de nuvem atingiu o limite no número de pontos de extremidade de entrada - Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| O cofre de backup e a conta de armazenamento de destino estão em duas regiões diferentes — Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure que o cofre de backup. |Nenhum |
| Não há suporte para a conta de armazenamento especificada para a operação de restauração - As contas de armazenamento Básica/Padrão com configurações de replicação de redundância geográfica ou redundância local. Selecione uma conta de armazenamento com suporte |Nenhum |
| O tipo de conta de armazenamento especificado para a operação de restauração não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauração está online |Isso pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| A Cota do Grupo de Recursos foi alcançada - Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| A subrede selecionada não existe - selecione uma subrede que exista |Nenhum |
| O Serviço de Backup não tem autorização para acessar recursos em sua assinatura. |Para resolver o problema, primeiro restaure discos, usando as etapas mencionadas na seção **Restaurar discos de backup** em [Escolha da configuração da VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, use as etapas do PowerShell mencionadas em [Criar uma máquina virtual de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs completas de discos restaurados. |

## <a name="backup-or-restore-taking-time"></a>Backup ou restauração demorando
Se você observar que o backup (>12 horas) ou a restauração (>6 horas) está demorando:
* Entenda os [fatores que contribuem para o tempo de backup](backup-azure-vms-introduction.md#total-vm-backup-time) e os [fatores que contribuem para o tempo de restauração](backup-azure-vms-introduction.md#total-restore-time).
* Siga as [melhores práticas de Backup](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>Agente de VM
### <a name="setting-up-the-vm-agent"></a>Configurando o agente de VM
Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o Agente de VM instalado. Para essas VMs, o Agente de VM precisa ser instalado explicitamente.

Para VMs do Windows:

* Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de Administrador para concluir a instalação.
* Para máquinas virtuais Clássicas, [atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Resource Manager.

Para VMs do Linux:

* Instale o mais recente do repositório de distribuição. É **altamente recomendável** a instalação do agente somente por meio do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte o [repositório de agente do Linux](https://github.com/Azure/WALinuxAgent) 
* Para VMs clássicas, [atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Resource Manager.

### <a name="updating-the-vm-agent"></a>Atualizar o Agente de VM
Para VMs do Windows:

* Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, você precisa garantir que nenhuma operação de backup esteja em execução enquanto o Agente de VM estiver sendo atualizado.

Para VMs do Linux:

* Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
É **altamente recomendável** a atualização do agente somente por meio do repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente não estiver disponível para a sua distribuição, entre em contato com o suporte de distribuição para obter instruções sobre como instalar o agente mais recente. Você pode verificar as informações mais recentes do [agente do Linux do Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validating-vm-agent-installation"></a>Validando a instalação do Agente de VM
Como verificar a versão do Agente de VM em VMs do Windows:

1. Faça logon na máquina virtual do Azure e navegue até a pasta *C:\WindowsAzure\Packages*. Você deve encontrar o arquivo WaAppAgent.exe presente.
2. Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do produto deve ser 2.6.1198.718 ou mais recente

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar Problemas de Instantâneo de VM
O backup de VM depende da emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos na execução da tarefa do instantâneo, pode resultar na falha do trabalho de backup. O descrito a seguir pode causar falha na tarefa do instantâneo.

1. O acesso à rede para o Armazenamento é bloqueado usando NSG<br>
    Saiba mais sobre como [habilitar o acesso à rede](backup-azure-vms-prepare.md#network-connectivity) para o Armazenamento usando WhiteListing de IPs ou por meio do servidor proxy.
2. Máquinas virtuais com o backup do SQL Server configurado podem causar atraso na tarefa do instantâneo  <br>
   Por padrão, o backup de VM emite backup completo de VSS em VMs do Windows. Em VMs que estão executando SQL Servers, se o backup do SQL Server estiver configurado, isso poderá causar atraso na execução do instantâneo. Defina a chave do Registro a seguir se houver falhas de backup devido a problemas de instantâneo.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Status da VM informado incorretamente porque a VM está desligada no RDP.  <br>
   Se você desligou a máquina virtual em RDP, verifique no portal que o status da VM esteja refletido corretamente. Se não estiver, desligue a VM no portal usando a opção 'Desligar' no painel da VM.
4. Se mais de quatro VMs compartilharem o mesmo serviço de nuvem, configure várias políticas de backup para preparar os tempos de backup para que não haja mais de quatro backups de VM iniciados ao mesmo tempo. Tente distribuir os tempos de início de backup em intervalos de uma hora entre políticas.
5. A VM está executando com alta utilização de CPU/memória.<br>
   Se a máquina virtual está em execução com alta utilização de CPU (&gt;90%) ou memória, a tarefa de instantâneo é enfileirada, atrasada e eventualmente atingirá o tempo limite. Tente o backup sob demanda em tais situações.

<br>

## <a name="networking"></a>Rede
Como todas as extensões, a extensão de Backup precisa acessar a Internet pública para trabalhar. A falta de acesso à Internet Pública pode se manifestar de várias formas:

* A instalação da extensão pode falhar
* As operações de backup (como snapshot de disco) podem falhar
* A exibição do status da operação de backup pode falhar

A necessidade de resolução de endereços de Internet pública foi articulada [aqui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Você precisa verificar as configurações de DNS para o VNET e certificar-se de que os URIs do Azure possam ser resolvidos.

Após a resolução de nomes ser feita corretamente, o acesso às IPs Azure também deve ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga uma destas etapas:

1. Realizar a lista branca de intervalos de IP do datacenter do Azure.
   * Obter a lista de [IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) a colocar na lista branca.
   * Desbloquear os IPs usando o cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Execute este cmdlet na VM do Azure em uma janela do PowerShell com privilégios elevados (executar como Administrador).
   * Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso aos IPs.
2. Criar um caminho para a transmissão do tráfego HTTP
   * Se você tiver alguma restrição de rede no local (um Grupo de Segurança de Rede, por exemplo), implante um servidor proxy HTTP para encaminhar o tráfego. As etapas para implantar um servidor proxy HTTP podem ser encontradas [aqui](backup-azure-vms-prepare.md#network-connectivity).
   * Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso à INTERNET do Proxy HTTP.

> [!NOTE]
> O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione.  Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada.
> Exiba mais informações sobre [Como definir um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
