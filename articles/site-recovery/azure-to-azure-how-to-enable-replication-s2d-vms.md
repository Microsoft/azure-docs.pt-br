---
title: Configurar replicação para VMs s2d (espaços de armazenamento diretos) no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs S2D, de uma região do Azure para outra, usando o Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790263"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replicar máquinas virtuais do Azure usando espaços de armazenamento diretos para outra região do Azure

Este artigo descreve como habilitar a recuperação de desastre de VMs do Azure executando espaços de armazenamento diretos.

>[!NOTE]
>Somente os pontos de recuperação com controle de falhas são compatíveis com clusters de espaços de armazenamento diretos.
>

## <a name="introduction"></a>Introdução 
[S2D (espaços de armazenamento diretos)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) é um armazenamento definido por software, que fornece uma maneira de criar [clusters convidados](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) no Azure.  Um cluster convidado no Microsoft Azure um Cluster de Failover composto de VMs de IaaS. Ele permite que cargas de trabalho de VM hospedadas façam failover entre clusters convidados, alcançando maior disponibilidade de SLA para aplicativos do que uma única VM do Azure pode fornecer. É útil em cenários nos quais uma VM hospeda um aplicativo crítico como SQL ou servidor de arquivos de escala horizontal, etc.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Recuperação de desastre de Máquinas Virtuais do Azure usando espaços de armazenamento diretos
Em um cenário típico, você pode ter um cluster convidado de máquinas virtuais no Azure para maior resiliência do aplicativo, assim como no servidor de arquivos de escala horizontal. Embora isso possa fornecer maior disponibilidade ao aplicativo, é conveniente que você proteja esses aplicativos usando o Site Recovery contra falhas em qualquer nível de região. O Site Recovery replica os dados de uma região para outra região do Azure e traz o cluster na região de recuperação de desastre em um evento de failover.

O diagrama abaixo mostra a representação visual de dois espaços de cluster de failover de VMs do Azure usando os espaços de armazenamento diretos.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Duas Máquinas Virtuais do Azure em um cluster de failover do Windows, sendo que cada máquina virtual tem dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento é apresentado como um CSV (volume compartilhado clusterizado) para o cluster de failover.
- O cluster de Failover usará o CSV para as unidades de dados.

**Considerações de recuperação de desastre**

1. Quando você estiver configurando uma [testemunha de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o cluster, mantenha a testemunha na região da Recuperação de Desastre.
2. Se você pretende fazer failover das máquinas virtuais para a sub-rede na região de Recuperação de Desastre, que é diferente da região de origem, o endereço IP do cluster precisa ser alterado após o failover.  Para alterar o IP do cluster, você precisa usar o [script de plano de recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) do ASR.</br>
[Exemplo de script](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar o comando dentro da VM usando a extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitar o Site Recovery para o cluster S2D:

1. Dentro do cofre dos serviços de recuperação, clique em "+replicar"
1. Selecione todos os nós no cluster e torne-os parte de um [Grupo de consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selecione a política de replicação com a consistência do aplicativo desativada* (apenas o suporte a controle de falhas está disponível)
1. Habilitar a replicação

   ![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá para os itens replicados e você poderá ver o status de ambas as máquinas virtuais. 
3. Ambas as máquinas virtuais estão sendo protegidas e também são mostradas como parte do grupo de consistência de várias VMs.

   ![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em 'Personalizar' para agrupar as VMs. Por padrão, todas as VMs são parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover ou durante o teste de failover para que seus aplicativos funcionem corretamente. É possível automatizar algumas operações pós-failover. Por exemplo, aqui estamos estiver anexando um balanceador de carga e alterando o IP do cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover das máquinas virtuais 
Ambos os nós das máquinas virtuais precisam sofrer failover usando o [plano de recuperação ASR](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Execute um teste de failover
1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou.
3.  Selecione **Failover de Teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, faça validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **Limpar failover de teste**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Próximas etapas

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sobre como executar o failback.
