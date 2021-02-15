---
title: Dimensionar a recuperação de desastres VMware/física com Azure Site Recovery
description: Saiba como configurar a recuperação de desastres para o Azure para grandes números de VMs VMware locais ou servidores físicos com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 101e42263e46c5a21f26b0fa9cdeed798525fee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89047062"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurar a recuperação de desastre em escala para VMs VMware/servidores físicos

Este artigo descreve como configurar a recuperação de desastres para o Azure para números grandes (> 1000) de VMs VMware locais ou servidores físicos em seu ambiente de produção, usando o serviço [Azure site Recovery](site-recovery-overview.md) .


## <a name="define-your-bcdr-strategy"></a>Definir sua estratégia de BCDR

Como parte de sua estratégia de BCDR (continuidade de negócios e recuperação de desastre), você define RPOs (objetivos de ponto de recuperação) e RTOs (objetivos de tempo de recuperação) para seus aplicativos de negócios e cargas de trabalho. O RTO mede a duração do tempo e o nível de serviço dentro dos quais um processo ou aplicativo de negócios deve ser restaurado e estar disponível para evitar problemas de continuidade.
- O Site Recovery fornece replicação contínua para VMs VMware e servidores físicos e um [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) para RTO.
- Ao planejar a recuperação de desastre em larga escala para VMs VMware e descobrir os recursos do Azure de que você precisa, você pode especificar um valor de RTO que será usado para cálculos de capacidade.


## <a name="best-practices"></a>Práticas recomendadas

Algumas práticas recomendadas gerais para a recuperação de desastre em larga escala. Essas práticas recomendadas são discutidas mais detalhadamente nas próximas seções do documento.

- **Identificar requisitos de destino**: estimar as necessidades de capacidade e recursos no Azure antes de configurar a recuperação de desastre.
- **Planejar site Recovery componentes**: descubra quais site Recovery componentes (servidor de configuração, servidores de processo) você precisa para atender à sua capacidade estimada.
- **Configurar um ou mais servidores de processo de escalabilidade horizontal**: não use o servidor de processo que está sendo executado por padrão no servidor de configuração. 
- **Execute as atualizações mais recentes**: a equipe de site Recovery lança novas versões de componentes do site Recovery regularmente, e você deve verificar se está executando as versões mais recentes. Para ajudar com isso [, acompanhe as novidades das](site-recovery-whats-new.md) atualizações e [habilite e instale as atualizações](service-updates-how-to.md) à medida que elas são lançadas.
- **Monitorar de forma proativa**: à medida que você obtém a recuperação de desastre em funcionamento, você deve monitorar proativamente o status e a integridade de máquinas replicadas e recursos de infraestrutura.
- **Análise de recuperação de desastres**: você deve executar análises de recuperação de desastre regularmente. Isso não afeta o ambiente de produção, mas ajuda a garantir que o failover para o Azure funcione conforme o esperado quando necessário.



## <a name="gather-capacity-planning-information"></a>Reunir informações de planejamento de capacidade

Reúna informações sobre seu ambiente local, para ajudar a avaliar e estimar suas necessidades de capacidade de destino (Azure).
- Para o VMware, execute o Planejador de Implantações para VMs VMware para fazer isso.
- Para servidores físicos, reúna as informações manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Executar o Planejador de Implantações para VMs VMware

O Planejador de Implantações ajuda a coletar informações sobre seu ambiente local do VMware.

- Execute o Planejador de Implantações durante um período que represente a rotatividade típica para suas VMs. Isso irá gerar estimativas e recomendações mais precisas.
- Recomendamos que você execute o Planejador de Implantações no computador do servidor de configuração, já que o planejador calcula a taxa de transferência do servidor no qual ele está sendo executado. [Saiba mais](site-recovery-vmware-deployment-planner-run.md#get-throughput) sobre como medir a taxa de transferência.
- Se você ainda não tiver um servidor de configuração configurado:
    - [Obtenha uma visão geral](vmware-physical-azure-config-process-server-overview.md) dos componentes do site Recovery.
    - [Configure um servidor de configuração](vmware-azure-deploy-configuration-server.md)para executar o planejador de implantações nele.

Em seguida, execute o planejador da seguinte maneira:

1. [Saiba mais sobre](site-recovery-deployment-planner.md) o planejador de implantações. Você pode baixar a versão mais recente no portal ou [baixá-la diretamente](https://aka.ms/asr-deployment-planner).
2. Examine os [pré-requisitos](site-recovery-deployment-planner.md#prerequisites) e [as atualizações mais recentes](site-recovery-deployment-planner-history.md) para o planejador de implantações e [Baixe e extraia](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) a ferramenta.
3. [Execute o planejador de implantações](site-recovery-vmware-deployment-planner-run.md) no servidor de configuração.
4. [Gere um relatório](site-recovery-vmware-deployment-planner-run.md#generate-report) para resumir estimativas e recomendações.
5. Analise as [recomendações de relatório](site-recovery-vmware-deployment-planner-analyze-report.md) e as [estimativas de custo](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Por padrão, a ferramenta está configurada para criar o perfil e gerar um relatório para até 1000 VMs. Você pode alterar esse limite aumentando o valor da chave MaxVMsSupported no arquivo ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Requisitos e capacidade do plano de destino (Azure)

Usando suas estimativas e recomendações coletadas, você pode planejar os recursos de destino e a capacidade. Se você executou o Planejador de Implantações para VMs VMware, poderá usar várias [recomendações de relatório](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) para ajudá-lo.

- **VMs compatíveis**: Use esse número para identificar o número de VMs que estão prontas para recuperação de desastre no Azure. As recomendações sobre largura de banda de rede e núcleos do Azure se baseiam nesse número.
- **Largura de banda de rede necessária**: Observe a largura de banda necessária para a replicação delta de VMs compatíveis. 
    - Ao executar o planejador, você especifica o RPO desejado em minutos. As recomendações mostram a largura de banda necessária para atender a esse RPO 100% e 90% do tempo. 
    - As recomendações de largura de banda da rede levam em conta a largura de banda necessária para o número total de servidores de configuração e servidores de processo recomendados no planejador.
- **Núcleos do Azure necessários**: Observe o número de núcleos que você precisa na região do Azure de destino, com base no número de VMs compatíveis. Se você não tiver núcleos suficientes, no failover Site Recovery não poderá criar as VMs do Azure necessárias.
- **Tamanho de lote de VM recomendado**: o tamanho de lote recomendado é baseado na capacidade de concluir a replicação inicial para o lote em 72 horas por padrão, ao mesmo tempo em que atende a um RPO de 100%. O valor de hora pode ser modificado.

Você pode usar essas recomendações para planejar recursos do Azure, largura de banda de rede e envio em lote de VM.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planejar assinaturas e cotas do Azure

Queremos garantir que as cotas disponíveis na assinatura de destino sejam suficientes para lidar com o failover.

**Tarefa** | **Detalhes** | **Ação**
--- | --- | ---
**Verificar núcleos** | Se os núcleos na cota disponível não forem iguais ou excederem a contagem de destino total no momento do failover, ocorrerá falha nos failovers. | Para VMs VMware, verifique se você tem núcleos suficientes na assinatura de destino para atender à recomendação de Planejador de Implantações Core.<br/><br/> Para servidores físicos, verifique se os núcleos do Azure atendem às suas estimativas manuais.<br/><br/> Para verificar as cotas, na **assinatura**do portal do Azure >, clique em **uso + cotas**.<br/><br/> [Saiba mais](../azure-portal/supportability/resource-manager-core-quotas-request.md) sobre como aumentar as cotas.
**Verificar limites de failover** | O número de failovers não deve excede Site Recovery limites de failover. |  Se os failovers excederem os limites, você poderá adicionar assinaturas e fazer failover para várias assinaturas ou aumentar a cota de uma assinatura. 


### <a name="failover-limits"></a>Limites de failover

Os limites indicam o número de failovers com suporte Site Recovery em uma hora, supondo três discos por computador.

O que significa conformidade? Para iniciar uma VM do Azure, o Azure requer alguns drivers para estar no estado de inicialização de boot e serviços como DHCP a serem definidos para serem iniciados automaticamente.
- Os computadores que estão em conformidade já terão essas configurações em vigor.
- Para computadores que executam o Windows, você pode verificar a conformidade proativamente e torná-los compatíveis, se necessário. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Os computadores Linux são colocados em conformidade apenas no momento do failover.

**O computador está em conformidade com o Azure?** | **Limites de VM do Azure (failover de disco gerenciado)**
--- | --- 
Sim | 2000
Não | 1000

- Os limites pressupõem que outros trabalhos mínimos estão em andamento na região de destino da assinatura.
- Algumas regiões do Azure são menores e podem ter limites ligeiramente menores.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planejar a infraestrutura e a conectividade da VM

Após o failover para o Azure, você precisa que suas cargas de trabalho operem como foram locais e para permitir que os usuários acessem as cargas de trabalho em execução nas VMs do Azure.

- [Saiba mais](site-recovery-active-directory.md#test-failover-considerations) sobre como fazer failover do seu Active Directory ou da infraestrutura local do DNS para o Azure.
- [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sobre como se preparar para se conectar às VMs do Azure após o failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Planejar a capacidade e os requisitos de origem

É importante que você tenha servidores de configuração suficientes e servidores de processo de expansão para atender aos requisitos de capacidade. Ao começar sua implantação em larga escala, comece com um único servidor de configuração e um único servidor de processo de escala horizontal. À medida que você atinge os limites indicados, adicione servidores adicionais.

>[!NOTE]
> Para VMs VMware, o Planejador de Implantações faz algumas recomendações sobre os servidores de configuração e de processo necessários. É recomendável que você use as tabelas incluídas nos procedimentos a seguir, em vez de seguir a recomendação Planejador de Implantações. 


## <a name="set-up-a-configuration-server"></a>Configurar um servidor de configuração
 
A capacidade do servidor de configuração é afetada pelo número de máquinas que estão replicando e não pela taxa de rotatividade de dados. Para descobrir se você precisa de servidores de configuração adicionais, use esses limites de VM definidos.

**CPU** | **Memória** | **Cache de disco** | **Limite de máquina replicada**
 --- | --- | --- | ---
8 vCPUs<br> 2 soquetes * 4 núcleos @ 2,5 GHz | 16 GB | 600 GB | Até 550 computadores<br> Pressupõe que cada computador tenha três discos de 100 GB cada.

- Esses limites se baseiam em um servidor de configuração configurado usando um modelo OVF.
- Os limites pressupõem que você não está usando o servidor de processo que está sendo executado por padrão no servidor de configuração.

Se você precisar adicionar um novo servidor de configuração, siga estas instruções:

- [Configure um servidor de configuração](vmware-azure-deploy-configuration-server.md) para a recuperação de desastre de VM VMware usando um modelo ovf.
- [Configure um servidor de configuração](physical-azure-set-up-source.md) manualmente para servidores físicos ou para implantações VMware que não podem usar um modelo ovf.

Ao configurar um servidor de configuração, observe que:

- Quando você configura um servidor de configuração, é importante considerar a assinatura e o cofre nos quais ele reside, pois eles não devem ser alterados após a instalação. Se você precisar alterar o cofre, será necessário desassociar o servidor de configuração do cofre e registrá-lo novamente. Isso interrompe a replicação de VMs no cofre.
- Se você quiser configurar um servidor de configuração com vários adaptadores de rede, você deve fazer isso durante a configuração. Você não pode fazer isso depois de registrar o servidor de configuração no cofre.

## <a name="set-up-a-process-server"></a>Configurar um servidor de processo

A capacidade do servidor de processo é afetada pelas taxas de rotatividade de dados e não pelo número de computadores habilitados para replicação.

- Para implantações grandes, você deve sempre ter pelo menos um servidor de processo de escalabilidade horizontal.
- Para descobrir se você precisa de servidores adicionais, use a tabela a seguir.
- Recomendamos que você adicione um servidor com a especificação mais alta. 


**CPU** | **Memória** | **Cache de disco** | **Taxa de rotatividade**
 --- | --- | --- | --- 
12 vCPUs<br> 2 soquetes * 6 núcleos @ 2,5 GHz | 24 GB | 1 GB | Até 2 TB por dia

Configure o servidor de processo da seguinte maneira:

1. Examine os [pré-requisitos](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Instale o servidor no [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)ou na [linha de comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configure os computadores replicados para usar o novo servidor. Se você já tiver máquinas replicando:
    - Você pode [mover](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) uma carga de trabalho de servidor de processo inteira para o novo servidor de processo.
    - Como alternativa, você pode [mover](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) VMs específicas para o novo servidor de processo.



## <a name="enable-large-scale-replication"></a>Habilitar replicação em larga escala

Após planejar a capacidade e implantar os componentes e a infraestrutura necessários, habilite a replicação para um grande número de VMs.

1. Classificar computadores em lotes. Você habilita a replicação para VMs em um lote e passa para o próximo lote.

    - Para VMs VMware, você pode usar o [tamanho de lote de VM recomendado](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) no relatório planejador de implantações.
    - Para computadores físicos, recomendamos que você identifique os lotes com base em computadores com tamanho e quantidade de dados semelhantes e na taxa de transferência de rede disponível. O objetivo é que os computadores do lote que provavelmente concluam sua replicação inicial em todo o mesmo período de tempo.
    
2. Se a rotatividade de disco de um computador estiver alta ou exceder os limites de implantação do planejador, você poderá mover arquivos não críticos que não precisem ser replicados (como despejos de log ou arquivos temporários) fora do computador. Para VMs VMware, você pode mover esses arquivos para um disco separado e, em seguida, [excluir esse disco](vmware-azure-exclude-disk.md) da replicação.
3. Antes de habilitar a replicação, verifique se as máquinas atendem [aos requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configure uma política de replicação para [VMs VMware](vmware-azure-set-up-replication.md#create-a-policy) ou [servidores físicos](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Habilite a replicação para [VMs VMware](vmware-azure-enable-replication.md) ou [servidores físicos](physical-azure-disaster-recovery.md#enable-replication). Isso aciona a replicação inicial para os computadores selecionados.

## <a name="monitor-your-deployment"></a>Monitorar a implantação

Depois de iniciar a replicação para o primeiro lote de VMs, comece a monitorar sua implantação da seguinte maneira:  

1. Atribua um administrador de recuperação de desastre para monitorar o status de integridade de máquinas replicadas.
2. [Monitorar eventos](site-recovery-monitor-and-troubleshoot.md) de itens replicados e da infraestrutura.
3. [Monitore a integridade](vmware-physical-azure-monitor-process-server.md) de seus servidores de processo de expansão.
4. Inscreva-se para obter [notificações por email](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) para eventos, para facilitar o monitoramento.
5. Realize análises regulares de [recuperação de desastres](site-recovery-test-failover-to-azure.md)para garantir que tudo esteja funcionando conforme o esperado.


## <a name="plan-for-large-scale-failovers"></a>Planejar failovers em larga escala

Em caso de desastre, talvez seja necessário fazer failover de um grande número de máquinas/cargas de trabalho para o Azure. Prepare-se para esse tipo de evento da seguinte maneira.

Você pode se preparar com antecedência para failover da seguinte maneira:

- [Prepare sua infraestrutura e VMs](#plan-infrastructure-and-vm-connectivity) para que suas cargas de trabalho estejam disponíveis após o failover e para que os usuários possam acessar as VMs do Azure.
- Observe os [limites de failover](#failover-limits) anteriormente neste documento. Verifique se os failovers se enquadram dentro desses limites.
- Execute análises regulares de [recuperação de desastre](site-recovery-test-failover-to-azure.md). Detalha a ajuda para:
    - Localize lacunas em sua implantação antes do failover.
    - Estimar o RTO de ponta a ponta para seus aplicativos.
    - Estimar o RPO de ponta a ponta para suas cargas de trabalho.
    - Identificar conflitos de intervalo de endereços IP.
    - À medida que você executa análises, recomendamos que você não use redes de produção para análises e limpe failovers de teste após cada análise.

Para executar um failover em larga escala, recomendamos o seguinte:

1. Criar planos de recuperação para failover de carga de trabalho.
    - Cada plano de recuperação pode disparar o failover de até 100 computadores.
    - [Saiba mais](recovery-plan-overview.md) sobre planos de recuperação.
2. Adicione scripts de runbook de automação do Azure aos planos de recuperação para automatizar qualquer tarefa manual no Azure. As tarefas típicas incluem a configuração de balanceadores de carga, a atualização de DNS, etc. [Saiba mais](site-recovery-runbook-automation.md)
2. Antes do failover, prepare as máquinas do Windows para que elas estejam em conformidade com o ambiente do Azure. [Os limites de failover](#plan-azure-subscriptions-and-quotas) são mais altos para computadores que estão em conformidade. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sobre runbooks.
4.  Disparar failover com o cmdlet [Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) do PowerShell, junto com um plano de recuperação.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar Site Recovery](site-recovery-monitor-and-troubleshoot.md)
