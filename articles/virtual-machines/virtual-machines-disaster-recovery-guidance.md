---
title: Cenários de recuperação de desastre para VMs do Azure | Microsoft Docs
description: Saiba o que fazer caso uma interrupção de serviço do Azure afete as máquinas virtuais do Azure.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc9ca5f5a638f0b36a28d58172fe8052b3d1522f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875442"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>O que fazer caso uma interrupção de serviço do Azure afete as VMs do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece um SLA (Contrato de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos internos de plataforma que oferecem suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, leia [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário real de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de uma interrupção em toda uma região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. No caso de uma interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.

Para ajudar você a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para a máquina virtual do Azure no caso de uma interrupção de serviço de toda a região em que seu aplicativo da máquina virtual do Azure é implantado.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: Iniciar um failover usando Azure Site Recovery
É possível configurar o Azure Site Recovery para suas VMs, de modo que você possa recuperar o aplicativo com um único clique em questão de minutos. É possível replicar para a região do Azure de sua escolha e não restrito para regiões emparelhadas. Você pode começar [replicando suas máquinas virtuais](https://aka.ms/a2a-getting-started). É possível [criar um plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md) para que você possa automatizar todo o processo de failover para sua aplicação. Você pode testar [seus failovers](../site-recovery/site-recovery-test-failover-to-azure.md) previamente sem afetar o aplicativo de produção ou a replicação em andamento. No caso de uma interrupção da região primária, você apenas [inicia um failover](../site-recovery/site-recovery-failover.md) e traz seu aplicativo na região alvo.


## <a name="option-2-wait-for-recovery"></a>Opção 2: Aguardar a recuperação
Nesse caso, nenhuma ação sua é necessária. Saiba que estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço Azure](https://azure.microsoft.com/status/).

Essa será a melhor opção se você não tiver instalado o Azure Site Recovery, o armazenamento com redundância geográfica de acesso de leitura ou o armazenamento com redundância geográfica antes da interrupção. Se você configurou o armazenamento com redundância geográfica de acesso de leitura ou o armazenamento com redundância geográfica para a conta de armazenamento na qual os VHDs (discos rígidos virtuais) de sua VM estão armazenados, você poderá recuperar o VHD da imagem base e tentar provisionar uma nova VM por meio dele. Essa não é uma opção preferencial, pois não há nenhuma garantia de sincronização de dados. Consequentemente, não há garantia de funcionamento dessa opção.


> [!NOTE]
> Lembre-se de que você não tem nenhum controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em toda uma região. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [Estratégias de Dados para Recuperação de Desastre](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Próximas etapas

- Inicie [protegendo seus aplicativos executados em máquinas virtuais do Azure](https://aka.ms/a2a-getting-started) utilizando o Azure Site Recovery

- Para saber mais sobre como implementar uma estratégia de alta disponibilidade e recuperação de desastres, consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [Orientação técnica sobre a resiliência do Azure](../resiliency/resiliency-technical-guidance.md).


- Se as instruções não estiverem claras ou se você desejar que a Microsoft faça as operações em seu nome, entre em contato com o [Atendimento ao Cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
