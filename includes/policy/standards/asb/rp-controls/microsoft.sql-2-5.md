---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c61f7bbe9f926ad186710cc6e804060d1e30950
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510519"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL com auditoria para o destino da conta de armazenamento devem ser configurados com retenção de 90 dias ou mais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Para fins de investigação de incidentes, é recomendável configurar a retenção de dados para a auditoria do SQL Server no destino de conta de armazenamento para pelo menos 90 dias. Confirme que você está cumprindo as regras de retenção necessárias para as regiões em que está operando. Às vezes, isso é necessário para que você tenha conformidade com os padrões regulatórios. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
