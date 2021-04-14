---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7ecff61cb1e72c8c24c9be1588819219a5792f6c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284522"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar os computadores Windows nos quais o agente do Log Analytics não esteja conectado conforme o esperado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o agente não estiver instalado e nem se ele estiver instalado, mas o objeto COM AgentConfigManager.MgmtSvcCfg retornar que ele está registrado em um workspace diferente da ID especificada no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Essa política audita os Conjuntos de Dimensionamento de Máquinas Virtuais do Windows ou do Linux nos quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[O agente do Log Analytics deve ser instalado nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Essa política audita as máquinas virtuais do Windows ou do Linux nas quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
