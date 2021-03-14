---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3089a08cedc7011b30b188cc527b196dd55b9875
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617651"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Implantar as Configurações de Diagnóstico do Data Lake Analytics no Hub de Eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |Implanta as configurações de diagnóstico do Data Lake Analytics a serem transmitidas para um Hub de Eventos regional em qualquer Data Lake Analytics criado ou atualizado que não tenha essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implantar as Configurações de Diagnóstico do Data Lake Analytics no workspace do Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |Implanta as configurações de diagnóstico do Data Lake Analytics a serem transmitidas para um workspace do Log Analytics regional em qualquer Data Lake Analytics criado ou atualizado que não tenha essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Os logs de recurso no Data Lake Analytics devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
