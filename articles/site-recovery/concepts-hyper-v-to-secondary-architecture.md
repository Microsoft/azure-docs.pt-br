---
title: "Revisar a arquitetura para replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral da arquitetura para replicação de VMs Hyper-V locais em um VMM do System Center secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Replicação do Hyper-V para um site secundário

Este artigo descreve os componentes e processos envolvidos na replicação de VMs (máquinas virtuais) do Hyper-V locais em nuvens do System Center Virtual Machine Manager (VMM) para um site de VMM secundário usando o serviço [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do Hyper-V para um site secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Assinatura do Azure | Crie um cofre dos Serviços de Recuperação na assinatura do Azure para orquestrar e gerenciar a replicação entre os locais do VMM.
**Servidor VMM** | Você precisa de locais primário e secundário para o VMM. | Recomendamos um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de host do Hyper-V nas nuvens do VMM primárias e secundárias. | Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou autenticação de certificado.  
**VMs Hyper-V** | No servidor de host do Hyper-V. | O servidor host de origem deve ter pelo menos uma VM que você deseja replicar.

**Arquitetura de local para local**

![Local para o próprio local](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais na VM são replicados individualmente para a localização secundária.
3. Se alterações nos discos ocorrerem enquanto a replicação inicial estiver em andamento, 
4. Quando a replicação inicial for concluída, a replicação delta começará. O Rastreador de Replicação de Réplica do Hyper-V acompanha as alterações como logs de replicação do Hyper-V (.hrl). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que é enviado para a localização secundária. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
5. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.
6. 

## <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode fazer o failover de um único computador ou criar planos de recuperação para orquestrar o failover de várias máquinas virtuais.
2. Você pode executar um failover planejado ou não planejado entre sites locais. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
    - Se você realizar um failover não planejado em um site secundário, depois do failover, as máquinas virtuais no local secundário não serão protegidas.
    - Depois de executar um failover planejado, os computadores no local secundário são protegidos.
3. Depois da execução do failover inicial, você confirma-o para começar a acessar a carga de trabalho da réplica de VM.

Quando a localização primária estiver disponível novamente, você poderá fazer failback.

1. Você inicia a replicação inversa, para iniciar a replicação do site secundário para o primário. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário permanece sendo o local ativo.
2. Para transformar o site primário em local ativo novamente, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.



## <a name="next-steps"></a>Próximas etapas

Examine a matriz de suporte e siga o tutorial para habilitar a replicação de Hyper-V entre nuvens de VMM.
Execute um failover e um failback.
