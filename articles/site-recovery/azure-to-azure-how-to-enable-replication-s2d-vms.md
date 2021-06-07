---
title: Replicar VMs do Azure executando Espaços de Armazenamento Diretos com Azure Site Recovery
description: Saiba como replicar VMs do Azure que executam Espaços de Armazenamento Diretos usando Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92424824"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicar VMs do Azure em execução Espaços de Armazenamento Diretos para outra região

Este artigo descreve como habilitar a recuperação de desastre de VMs do Azure executando espaços de armazenamento diretos.

>[!NOTE]
>Somente os pontos de recuperação com controle de falhas são compatíveis com clusters de espaços de armazenamento diretos.
>

Os [espaços de armazenamento diretos (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) são o armazenamento definido pelo software, que fornece uma maneira de criar [clusters convidados](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) no Azure.  Um cluster de convidado no Microsoft Azure é um cluster de failover composto por VMs de IaaS. Ele permite que as cargas de trabalho de VM hospedadas entrem em clusters de convidado, alcançando um SLA de maior disponibilidade para aplicativos, do que uma única VM do Azure pode fornecer. Ele é útil em cenários em que uma VM hospeda um aplicativo crítico como SQL ou servidor de arquivos de escalabilidade horizontal.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Recuperação de desastres com espaços de armazenamento diretos

Em um cenário típico, você pode ter um cluster convidado de máquinas virtuais no Azure para maior resiliência do aplicativo, assim como no servidor de arquivos de escala horizontal. Embora isso possa fornecer maior disponibilidade ao aplicativo, é conveniente que você proteja esses aplicativos usando o Site Recovery contra falhas em qualquer nível de região. O Site Recovery replica os dados de uma região para outra região do Azure e traz o cluster na região de recuperação de desastre em um evento de failover.

O diagrama abaixo mostra um cluster de failover de VM do Azure de dois nós usando espaços de armazenamento diretos.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Duas Máquinas Virtuais do Azure em um cluster de failover do Windows, sendo que cada máquina virtual tem dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento é apresentado como um CSV (volume compartilhado clusterizado) para o cluster de failover.
- O cluster de Failover usará o CSV para as unidades de dados.

**Considerações sobre recuperação de desastre**

1. Quando você estiver configurando uma [testemunha de nuvem](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o cluster, mantenha a testemunha na região da Recuperação de Desastre.
2. Se você pretende fazer failover das máquinas virtuais para a sub-rede na região de Recuperação de Desastre, que é diferente da região de origem, o endereço IP do cluster precisa ser alterado após o failover.  Para alterar o IP do cluster, você precisa usar o [script de plano de recuperação site Recovery.](./site-recovery-runbook-automation.md)</br>
[Exemplo de script](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar o comando dentro da VM usando a extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitar o Site Recovery para o cluster S2D:

1. Dentro do cofre dos serviços de recuperação, clique em "+replicar"
1. Selecione todos os nós no cluster e torne-os parte de um [Grupo de consistência de várias VMs](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Selecione a política de replicação com a consistência do aplicativo desativada* (apenas o suporte a controle de falhas está disponível)
1. Habilitar a replicação

   ![Captura de tela que mostra onde definir as configurações de replicação.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá para os itens replicados e você poderá ver o status de ambas as máquinas virtuais.
3. Ambas as máquinas virtuais estão sendo protegidas e também são mostradas como parte do grupo de consistência de várias VMs.

   ![Captura de tela que mostra as máquinas virtuais são protegidas e uma parte de um grupo de consistência de várias VMS.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em 'Personalizar' para agrupar as VMs. Por padrão, todas as VMs são parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover ou durante o teste de failover para que seus aplicativos funcionem corretamente. É possível automatizar algumas operações pós-failover. Por exemplo, aqui estamos anexando o balanceador de carga e alterando o IP do cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover das máquinas virtuais 
Ambos os nós das VMs precisam fazer failover usando o [plano de recuperação](./site-recovery-create-recovery-plans.md) site Recovery 

![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Execute um teste de failover
1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou.
3.  Selecione **failover de teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, faça validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **limpar failover de teste**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Próximas etapas

[Saiba mais](./azure-to-azure-tutorial-failover-failback.md) sobre como executar o failback.
