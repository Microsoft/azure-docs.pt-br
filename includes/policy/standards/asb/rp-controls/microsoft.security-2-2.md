---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 56b2ae4aff99bed46fa5ad7f0947193b2f465431
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512713"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O provisionamento automático do agente do Log Analytics deve ser habilitado na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorar as vulnerabilidades e ameaças à segurança, a Central de Segurança do Azure coleta dados de suas máquinas virtuais do Azure. Os dados são coletados pelo agente do Log Analytics, anteriormente conhecido como MMA (Microsoft Monitoring Agent), que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace do Log Analytics para análise. É recomendável habilitar o provisionamento automático para implantar automaticamente o agente em todas as VMs do Azure com suporte e nas VMs que forem criadas. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
