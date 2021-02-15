---
title: Políticas recomendadas para serviços do Azure
description: Descreve como localizar e aplicar políticas recomendadas para serviços do Azure, como máquinas virtuais do Azure.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350272"
---
# <a name="recommended-policies-for-azure-services"></a>Políticas recomendadas para serviços do Azure

Os clientes que são novos no Azure Policy muitas vezes procuram encontrar definições de política comuns para gerenciar e governar seus recursos. **As políticas recomendadas** do Azure Policy fornecem uma lista focada de definições de política comuns para começar. A experiência de **políticas recomendadas** para recursos com suporte é incorporada na experiência do portal para esse recurso.

Para obter Azure Policy internas adicionais, consulte [Azure Policy definições internas](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

As **políticas recomendadas** para as [máquinas virtuais do Azure](../../../virtual-machines/index.yml) estão na página **visão geral** das máquinas virtuais e na guia **recursos** . No cartão de _Azure Policy_ , selecione o texto "não configurado" ou "# atribuído" para abrir um painel lateral com as políticas recomendadas. Qualquer definição de política já atribuída a um escopo em que a máquina virtual é membro está esmaecida. Selecione as políticas recomendadas a serem aplicadas a esta máquina virtual e selecione **atribuir políticas** para criar uma atribuição para cada uma.

Como uma organização atinge a maturidade com a [organização dos recursos e da hierarquia de recursos](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions), é recomendável fazer a transição dessas atribuições de política de uma por recurso para a assinatura ou nível de [grupo de gerenciamento](../../management-groups/index.yml) .

### <a name="azure-virtual-machines-recommended-policies"></a>Políticas recomendadas de máquinas virtuais do Azure

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem a recuperação de desastre configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais sem a recuperação de desastre configurada. Para saber mais sobre recuperação de desastre, acesse [https://aka.ms/asr-doc](../../../site-recovery/index.yml). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Auditar VMs que não usam discos gerenciados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Essa política audita VMs que não usam discos gerenciados |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Essa política ajuda a auditar se o serviço de Backup do Azure está habilitado para todas as máquinas virtuais. O Backup do Azure é uma solução de backup econômica e com um clique que simplifica a recuperação de dados e é mais fácil de habilitar do que outros serviços de backup em nuvem. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar [Compreendendo os efeitos da política](./effects.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).