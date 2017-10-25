---
title: "Executar failover e failback de VMs do Hyper-V replicadas para um data center secundário com o Site Recovery | Microsoft Docs"
description: "Saiba como executar failover de VMs do Hyper-V para um site local secundário e failback para o site primário com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Executar failover e failback em VMs do Hyper-V replicadas para o site local secundário

O serviço [Azure Site Recovery](site-recovery-overview.md) gerencia e orquestra a replicação, o failover e o failback de máquinas locais e de VMs (máquinas virtuais) do Azure.

Este tutorial descreve como executar failover de uma VM do Hyper-V gerenciada em uma nuvem do System Center VMM (Virtual Machine Manager), para um site do VMM secundário. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Executar failover de uma VM do Hyper-V de uma nuvem do VMM primária para uma nuvem do VMM secundária
> * Proteger novamente do site secundário para o primário e executar failback
> * Se desejar, iniciar a replicação do primário para o secundário novamente

## <a name="overview"></a>Visão geral

O failover e o failback têm três estágios:

1. **Failover para o site secundário**: executar failover de máquinas do site primário para o secundário.
2. **Failback do site secundário**: replicar as VMs do secundário para o primário e executar um failover planejado para failback.
3. Após o failover planejado, se desejar inicie a replicação do site primário para o secundário novamente.


## <a name="fail-over-to-a-secondary-site"></a>Executar failover para um site secundário

### <a name="failover-prerequisites"></a>Pré-requisitos de failover

Verifique se você concluiu uma [simulação de recuperação de desastre](tutorial-dr-drill-secondary.md) para verificar se tudo está funcionando conforme o esperado.


### <a name="run-a-failover-from-primary-to-secondary"></a>Executar um failover do primário para o secundário

Você pode executar um failover planejado ou regular para VMs do Hyper-V.

- Use um failover regular nas interrupções inesperadas. Quando você executa esse failover, o Site Recovery cria e liga uma VM no site secundário. Execute o failover em relação a um ponto de recuperação específico. Dependendo do ponto de recuperação usado, poderá ocorrer perda de dados.
- Um failover planejado pode ser usado para manutenção ou durante uma interrupção esperada. Essa opção não causa perda de dados. Quando um failover planejado é disparado, as VMs de origem são desligadas. Os dados não sincronizados são sincronizados e o failover é disparado. 
- 
Este procedimento descreve como executar um failover regular.


1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
    - **Último** (padrão): essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM de réplica criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
    - **Últimos processados**: essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
    - **Consistente com o aplicativo mais recente**: essa opção executa failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery. 
3. A chave de criptografia não é relevante neste cenário.
4. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das VMs de origem antes de disparar o failover. O Site Recovery também tenta sincronizar os dados locais que ainda não foram enviados para o site secundário antes de disparar o failover. Observe que o failover continuará mesmo se o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Agora a VM deve ser exibida na nuvem do VMM secundária.
6. Depois de verificar se VM, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>Proteger novamente executar failback do secundário para o primário

### <a name="prerequisites-for-failback"></a>Pré-requisitos para o failback

Para concluir o failback, verifique se os servidores VMM primário e secundário estão conectados ao Site Recovery.


### <a name="reprotect-and-fail-back"></a>Proteja novamente e execute failback

Inicie a replicação do site secundário para o primário e o failback para o site primário. Depois que as VMs estiverem em execução no site primário novamente, você poderá replicá-las para o site secundário novamente.  

1. Em **Configurações** > **Itens replicados** clique na VM e habilite **Replicação Inversa**. A VM começa a replicar de volta para o site primário.
2. Clique em VM > **Failover Planejado**.
3. Em **Confirmar Failover Planejado**, verifique a direção do failover (da nuvem do VMM secundária) e selecione os locais de origem e destino. 
4. Em **Sincronização de Dados**, especifique como deseja sincronizar:
    - **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**— Essa opção minimiza o tempo de inatividade da VM, pois os dados são sincronizados sem que a VM seja desligada. Aqui está o que ele faz:
        - Cria um instantâneo da VM de réplica e copia-o no host do Hyper-V primário. A VM de réplica continua em execução.
        - Desliga a VM de réplica, para que não ocorra nenhuma nova alteração. O conjunto final de alterações delta é transferido para o site primário e a VM no site primário é iniciada.
    - **Sincronizar dados apenas durante o failover (download completo)** – use essa opção se você estiver executando no site secundário por um longo período. Esta opção é mais rápida, porque esperamos várias mudanças de disco e não desperdiçamos tempo em cálculos de soma de verificação. Essa opção executa um download de disco. Ela também é útil quando a VM primária foi excluída.
5. A chave de criptografia não é relevante neste cenário.
6. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
7. Se você selecionou para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as VMs de réplica no site secundário, clique em **Trabalhos** > nome do trabalho de failover planejado > **Concluir Failover**. Isso desligará a VM secundária, transferirá as alterações mais recentes para o site primário e iniciará a VM primária.
8. Na nuvem do VMM primária, verifique se a VM está disponível.
9. Agora, a VM primária está no estado de Confirmação Pendente. Clique em **Confirmar** para confirmar o failover.
10. Se você deseja reiniciar a replicação da VM primária para o site secundário novamente, habilite a **Replicação Inversa**.


> [!NOTE]
> A replicação inversa só replica as alterações que ocorreram desde que a VM de réplica foi desligada e apenas as alterações delta são enviadas.

