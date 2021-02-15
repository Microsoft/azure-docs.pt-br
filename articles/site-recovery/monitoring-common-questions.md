---
title: Perguntas comuns sobre o monitoramento de Azure Site Recovery
description: Obtenha respostas para perguntas comuns sobre monitoramento de Azure Site Recovery, usando monitoramento e Azure Monitor (Log Analytics) embutidos
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131153"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Perguntas comuns sobre o monitoramento de Site Recovery

Este artigo responde a perguntas comuns sobre como monitorar [site Recovery](site-recovery-overview.md)do Azure, usando o monitoramento de site Recovery interno e Azure Monitor (log Analytics).

## <a name="general"></a>Geral

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Como o valor de RPO é registrado diferente do ponto de recuperação mais recente disponível?

O Site Recovery usa um processo assíncrono de várias etapas para replicar computadores no Azure.

- Na penúltima etapa da replicação, as alterações recentes no computador, em conjunto com metadados, são copiadas para uma conta de armazenamento em cache/log.
- Essas alterações, juntamente com a marca que identifica um ponto recuperável, são gravadas na conta de armazenamento/disco gerenciado na região de destino.
- Agora Site Recovery pode gerar um ponto recuperável para o computador.
- Neste ponto, o RPO foi atendido para as alterações carregadas na conta de armazenamento até o momento. Em outras palavras, o RPO do computador neste ponto é igual à quantidade de tempo decorrido do carimbo de data/hora correspondente ao ponto recuperável.
- Agora, o Site Recovery seleciona os dados carregados da conta de armazenamento e aplica-os aos discos de réplica criados para o computador.
- O Site Recovery, em seguida, gera um ponto de recuperação e disponibiliza esse ponto para recuperação no failover.
- Assim, o ponto de recuperação mais recente disponível indica o carimbo de data/hora correspondente ao último ponto de recuperação que já foi processado e aplicado aos discos de réplica.


Uma hora incorreta do sistema na máquina de origem de replicação ou nos servidores de infraestrutura local distorcerá o valor de RPO calculado. Para relatórios precisos de RPO, verifique se o relógio do sistema está correto em todos os servidores e computadores.



## <a name="inbuilt-site-recovery-logging"></a>Log de Site Recovery embutido


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Por que a contagem de VM no modo de exibição de infraestrutura de cofre é diferente da contagem total mostrada em itens replicados?

O modo de exibição de infraestrutura do cofre é com escopo por cenários de replicação. Somente os computadores no cenário de replicação selecionado no momento são incluídos na contagem da exibição. Além disso, contamos apenas as VMs configuradas para replicar no Azure. Os computadores com failover ou os computadores que replicam de volta para um site local não são contados na exibição.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Por que a contagem de itens replicados no Essentials é diferente da contagem total de itens replicados no painel?

Somente os computadores para os quais a replicação inicial foi concluída estão incluídos na contagem mostrada em Essentials. O total de itens replicados inclui todos os computadores no cofre, incluindo aqueles para os quais a replicação inicial está atualmente em andamento.

## <a name="azure-monitor-logging"></a>Registro em log do Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Com que frequência Site Recovery enviar logs de recursos para Azure Monitor log? 

- AzureSiteRecoveryReplicationStats e AzureSiteRecoveryRecoveryPoints são enviados a cada 15 minutos.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurn são enviados a cada cinco minutos. 
- AzureSiteRecoveryJobs é enviado no gatilho e na conclusão de um trabalho.
- AzureSiteRecoveryEvents é enviado sempre que um evento é gerado. 
- AzureSiteRecoveryReplicatedItems é enviado sempre que houver qualquer alteração de ambiente. Normalmente, o tempo de atualização de dados é de 15 minutos após uma alteração. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Por quanto tempo os dados são mantidos em logs de Azure Monitor? 

Por padrão, a retenção é por 31 dias. Você pode aumentar o período na seção **uso e custo estimado** no espaço de trabalho log Analytics. Clique em **retenção de dados**e escolha o intervalo.

### <a name="whats-the-size-of-the-resource-logs"></a>Qual é o tamanho dos logs de recursos? 

Normalmente, o tamanho de um log é de 15-20 KB. 


## <a name="next-steps"></a>Próximas etapas

Saiba como monitorar com [site Recovery monitoramento embutido](site-recovery-monitor-and-troubleshoot.md)ou [Azure monitor](monitor-log-analytics.md).


