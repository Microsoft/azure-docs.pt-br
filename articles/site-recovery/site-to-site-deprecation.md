---
title: Substituição da recuperação de desastre entre sites gerenciados pelo cliente (com o VMM) usando Azure Site Recovery | Microsoft Docs
description: Detalhes sobre a futura substituição de DR entre sites de Propriedade do cliente usando o Hyper-V e entre sites gerenciados pelo SCVMM para o Azure e opções alternativas
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421834"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Substituição da recuperação de desastre entre sites gerenciados pelo cliente (com o VMM) usando Azure Site Recovery

Este artigo descreve o próximo plano de substituição, as implicações correspondentes e as opções alternativas disponíveis para os clientes para o seguinte cenário:

DR entre sites de Propriedade do cliente gerenciados pelo System Center Virtual Machine Manager (SCVMM) usando Site Recovery

> [!IMPORTANT]
> Os clientes são aconselhados a realizar as etapas de correção no primeiro lugar para evitar qualquer interrupção em seu ambiente. 

## <a name="what-changes-should-you-expect"></a>Quais alterações você deve esperar?

- A partir de 2020 de março, você receberá portal do Azure notificações & comunicações por email com a futura substituição de replicação site a site de VMs do Hyper-V. A substituição está planejada para março de 2023.

- Se você tiver uma configuração existente, não haverá nenhum impacto na instalação.

- Depois que os cenários forem preteridos, a menos que o cliente siga as abordagens alternativas, as replicações existentes poderão ser interrompidas. Os clientes não poderão exibir, gerenciar ou executar qualquer operação relacionada à recuperação de desastre por meio da experiência de Azure Site Recovery no portal do Azure.
 
## <a name="alternatives"></a>Alternativas 

Abaixo estão as alternativas que o cliente pode escolher para garantir que sua estratégia de DR não seja afetada quando o cenário for preterido. 

- Opção 1 (recomendado): escolha [começar a usar o Azure como o destino de Dr](hyper-v-vmm-azure-tutorial.md).


- Opção 2: escolha continuar com a replicação site a site usando a [solução de réplica do Hyper-V](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)subjacente, mas você não poderá gerenciar as configurações de DR usando Azure Site Recovery no portal do Azure. 


## <a name="remediation-steps"></a>Etapas de correção

Se você estiver optando por usar a opção 1, execute as seguintes etapas:

1. [Desabilite a proteção de todas as máquinas virtuais associadas ao VMMS](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Use a opção **desabilitar a replicação e remover** ou execute os scripts mencionados para garantir que as configurações de replicação locais sejam limpas. 

2. [Cancele o registro de todos os servidores do VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) da configuração de replicação site a site.

3. [Prepare os recursos do Azure](tutorial-prepare-azure-for-hyperv.md) para habilitar a replicação de suas VMs.
4. [Preparar servidores Hyper-V locais](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurar a replicação para as VMs na nuvem do VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcional, mas recomendado: [executar uma análise de Dr](tutorial-dr-drill-azure.md)

Se você estiver optando por usar a opção 2 de usando a réplica do Hyper-V, execute as seguintes etapas:

1. Em **itens protegidos**itens  >  **replicados**, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
2. Em **desabilitar replicação**, selecione **remover**.

    Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

## <a name="next-steps"></a>Próximas etapas
Planeje a substituição e escolha uma opção alternativa que seja mais adequada para sua infraestrutura e negócios. Caso você tenha alguma consulta sobre isso, acesse Suporte da Microsoft

