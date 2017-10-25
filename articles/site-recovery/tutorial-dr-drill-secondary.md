---
title: "Executar uma análise detalhada da recuperação de desastre no site local secundário com o Azure Site Recovery | Microsoft Docs"
description: "Saiba mais sobre como executar uma análise detalhada da recuperação de desastre no seu site local secundário com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Execute uma análise detalhada da recuperação de desastre de VMs do Hyper-V em seu site local secundário

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como executar uma análise detalhada da recuperação de desastre em VMs do Hyper-V para seu site local secundário. As VMs do Hyper-V devem ser gerenciadas em uma nuvem privada do VMM (System Center Virtual Machine Manager). Uma análise detalhada valida sua estratégia de replicação, sem perda de dados ou tempo de inatividade, e não afeta seu ambiente de produção. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar pré-requisitos para o failover de teste
> * Executar um failover de teste para um único computador


## <a name="prerequisites"></a>Pré-requisitos

- Você pode executar um failover de teste com algumas opções de rede no site secundário. Você pode executar o failover sem uma rede, com uma rede existente ou permitir que o Site Recovery crie automaticamente uma rede de teste. 
**Se você quiser usar uma rede de produção existente para o failover de teste**:
    - A VM primária deverá ser desligada quando você fizer o failover de teste. Caso contrário, duas VMs com a mesma identidade serão executadas na mesma rede, ao mesmo tempo. 
    - Se você fizer alterações em VMs de teste, essas alterações serão perdidas quando você limpar o failover de teste. As alterações não são replicadas de volta na VM primária.
    - O teste de uma rede de produção resulta em tempo de inatividade para as cargas de trabalho de produção. Peça aos usuários que não usem aplicativos relacionados quando a análise detalhada da recuperação de desastre estiver em andamento. 
- A rede de teste de réplica não precisa ser compatível com o tipo de rede lógica do VMM usado para o failover de teste. Porém, algumas combinações não funcionam. Por exemplo se a réplica usar DHCP e o isolamento baseado em VLAN, você não poderá usar a Virtualização de Rede do Windows para a rede de failover de teste porque ela precisa dos pools de endereços IP. 
- É recomendável que você não use a rede que selecionou para o mapeamento de rede e para o failover de teste.


## <a name="run-a-test-failover-for-a-vm"></a>Executar um failover de teste para uma VM

1. Em **Itens Replicados**, clique na VM > **Failover de Teste**.
2. Em **Failover de Teste**, especifique como as VMs de teste serão conectadas a redes após o failover de teste. Para os fins deste tutorial, é recomendável permitir que o Site Recovery crie automaticamente uma rede de testes. [Saiba mais](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. Clique em **OK** para iniciar o failover. Acompanhar o progresso na guia **Trabalhos** .
4. Após o failover ser concluído, verifique se as VMs de teste são iniciadas com êxito.
5. Quando terminar, clique em **Limpar failover de teste**. Isso exclui as VMs de teste e as redes criadas durante o failover de teste.
6. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. 


## <a name="next-steps"></a>Próximas etapas

[Executar um failover de produção](tutorial-vmm-to-vmm-failover-failback.md)






