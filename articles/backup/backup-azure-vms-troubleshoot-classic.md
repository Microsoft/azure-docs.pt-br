---
title: "Solucionar problemas de erros de backup do Azure no Portal Clássico | Microsoft Docs"
description: "Solucione problemas de backup e restauração de máquinas virtuais do Azure no Portal Clássico."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
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

## <a name="discovery"></a>Descoberta
| Operação de backup | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Descoberta |Falha ao detectar novos itens - Backup do Microsoft Azure encontrado e erro interno. Aguarde alguns minutos e repita a operação. |Repita o processo de descoberta após 15 minutos. |
| Descoberta |Falha ao descobrir novos itens – outra operação de descoberta já está em andamento. Aguarde até que a operação de descoberta atual seja concluída. |Nenhum |

## <a name="register"></a>Registrar
| Operação de backup | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrar |O número de discos de dados anexados à máquina virtual excedeu o limite com suporte. Retire alguns discos de dados dos anexos nesta máquina virtual e repita a operação. O backup do Azure dá suporte a até 16 discos de dados anexados a uma máquina virtual do Azure para backup |Nenhum |
| Registrar |O Backup do Microsoft Azure encontrou um erro interno. Aguarde alguns minutos e tente a operação novamente. Se o problema persistir, contate o Suporte da Microsoft. |É possível obter esse erro devido a uma das seguintes configuração de VM sem suporte no LRS Premium. <br> pode ser feito usando o cofre de serviços de recuperação. [Saiba mais](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| Registrar |Falha no registro com o tempo limite da operação Instalar agente |Verifique se a versão do sistema operacional da máquina virtual tem suporte. |
| Registrar |A execução do comando falhou - há outra operação em andamento neste item. Aguarde até que a operação anterior seja concluída |Nenhum |
| Registrar |Não há suporte para máquinas virtuais com discos rígidos virtuais no armazenamento Premium para backup |Nenhum |
| Registrar |O agente de máquina virtual não está presente na máquina virtual - instale o pré-requisito necessário, agente de VM e reinicie a operação. |[Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |

## <a name="backup"></a>Backup
| Operação de backup | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup |Não pôde se comunicar com o agente VM para status do instantâneo. A subtarefa VM Instantânea atingiu o tempo limite. – Consulte o guia de solução de problemas para saber como resolver esse problema. |Esse erro é gerado se há um problema com o agente de VM ou se o acesso à rede para a infraestrutura do Azure está bloqueado de alguma forma. Saiba mais sobre [depuração de problemas de instantâneo de VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Se o agente de VM não está causando problemas, reinicie a máquina virtual. Às vezes um estado incorreto de VM pode causar problemas e reiniciar a VM redefine esse "estado inválido" |
| Backup |Falha no backup com um erro interno - tente novamente a operação dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft |Verifique se há um problema temporário ao acessar o armazenamento de VM. Verifique o [Status do Azure](https://azure.microsoft.com/en-us/status/) para ver se há qualquer problema ativo relacionado a computação/armazenamento/rede na região. Repita até que o problema de publicação de backup seja mitigado. |
| Backup |Não foi possível executar a operação porque a VM não existe mais. |O backup não pode ser executado porque a VM configurada para o backup foi excluída. Pare outros backups no modo de exibição de Itens protegidos, selecione o item protegido e clique em Parar Proteção. Você pode manter dados selecionando a opção de dados Reter Backup. Posteriormente, você poderá retomar a proteção para essa máquina virtual clicando em configurar proteção no modo de exibição Itens Registrados |
| Backup |Falha ao instalar a extensão dos Serviços de Recuperação do Azure no item selecionado - o agente de VM é um pré-requisito para a extensão de Serviços de Recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro |<ol> <li>Verifique se o agente de VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM esteja definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |
| Backup |A execução do comando falhou - outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente |Está em execução um backup existente ou um trabalho de restauração para a máquina virtual e não é possível iniciar um novo trabalho enquanto o trabalho existente estiver sendo executado. |
| Backup |Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft |Isso geralmente significa que o serviço COM+ não está em execução. Entre em contato com o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Backup |Falha na operação de instantâneo. Erro de operação do VSS "Esta unidade está bloqueada pela Criptografia de Unidade de Disco BitLocker. Você deve desbloquear esta unidade no Painel de Controle. |Desative o BitLocker para todas as unidades na VM e observe se o problema VSS é resolvido |
| Backup |Não há suporte para máquinas virtuais com discos rígidos virtuais no armazenamento Premium para backup |Nenhum |
| Backup |Máquina Virtual do Azure não encontrada. |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li>Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem], <br>(OU) <li> Desabilite a proteção para esta VM para que os trabalhos de backup subsequentes não sejam disparados. </ol> |
| Backup |O agente de máquina virtual não está presente na máquina virtual - instale o pré-requisito necessário, agente de VM e reinicie a operação. |[Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |

## <a name="jobs"></a>Trabalhos
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Cancelar trabalho |Não há suporte para cancelamento deste tipo de trabalho - Aguarde até que o trabalho seja concluído. |Nenhum |
| Cancelar trabalho |O trabalho não está em um estado cancelável - aguarde até que o trabalho seja concluído. <br>OU<br> o trabalho selecionado não está em um estado cancelável - aguarde até que o trabalho seja concluído. |Muito provavelmente o trabalho está quase concluído; Aguarde até que o trabalho seja concluído |
| Cancelar trabalho |Não é possível cancelar o trabalho porque ele não está em andamento - há suporte para cancelamento apenas de trabalhos que estão em andamento. Tente cancelar um trabalho em andamento. |Isso ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento |
| Cancelar trabalho |Falha ao cancelar o trabalho - aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Restaurar |A restauração falhou com erro interno de nuvem |<ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar  <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se houver um endereço configurado, isso significa que as configurações de DNS estão definidas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com ReservedIP e as VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar se um serviço de nuvem tem IP reservado usando os seguintes cmdlets do Powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem. <br>– Máquinas virtuais sob configuração do balanceador de carga (interno e externo)<br>– Máquinas virtuais com vários IPs reservados<br>– Máquinas virtuais com várias NICs<br>Selecione um novo serviço de nuvem na interface do usuário ou confira as [considerações sobre a restauração](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especiais</ol> |
| Restaurar |O nome DNS selecionado já existe - especifique um nome DNS diferente e tente novamente. |O nome DNS aqui se refere ao nome do serviço de nuvem (geralmente terminando em .cloudapp.net). Isso precisa ser exclusivo. Se você encontrar esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br> Este erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell obtém êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| Restaurar |A configuração de rede virtual especificada não está correta - especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| Restaurar |O serviço de nuvem especificado está usando um IP reservado, o que não corresponde à configuração da máquina virtual que está sendo restaurada - especifique um serviço de nuvem diferente que não está usando o IP reservado ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| Restaurar |O serviço de nuvem atingiu o limite no número de pontos de extremidade de entrada - Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| Restaurar |O cofre de backup e a conta de armazenamento de destino estão em duas regiões diferentes — Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure que o cofre de backup. |Nenhum |
| Restaurar |Não há suporte para a conta de armazenamento especificada para a operação de restauração - As contas de armazenamento Básica/Padrão com configurações de replicação de redundância geográfica ou redundância local. Selecione uma conta de armazenamento com suporte |Nenhum |
| Restaurar |O tipo de conta de armazenamento especificado para a operação de restauração não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauração está online |Isso pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| Restaurar |A Cota do Grupo de Recursos foi alcançada - Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| Restaurar |A subrede selecionada não existe - selecione uma subrede que exista |Nenhum |

## <a name="policy"></a>Política
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Criar política |Falha ao criar a política - Reduza as escolhas de retenção para continuar com a configuração da política. |Nenhum |

## <a name="vm-agent"></a>Agente de VM
### <a name="setting-up-the-vm-agent"></a>Configurando o agente de VM
Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o Agente de VM instalado. Para essas VMs, o Agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

* Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação.
* [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para VMs do Linux:

* Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do github.
* [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

### <a name="updating-the-vm-agent"></a>Atualizar o Agente de VM
Para VMs do Windows:

* Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, você precisa garantir que nenhuma operação de backup esteja em execução enquanto o Agente de VM estiver sendo atualizado.

Para VMs do Linux:

* Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>Validando a instalação do Agente de VM
Como verificar a versão do Agente de VM em VMs do Windows:

1. Faça logon na máquina virtual do Azure e navegue até a pasta *C:\WindowsAzure\Packages*. Você deve encontrar o arquivo WaAppAgent.exe presente.
2. Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do produto deve ser 2.6.1198.718 ou mais recente
