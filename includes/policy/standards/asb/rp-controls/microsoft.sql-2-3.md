---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cb1f498fe2c077290609efcf1d5282c83d88aa08
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021868"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[As configurações de Auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |A propriedade AuditActionsAndGroups deve conter pelo menos SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP e BATCH_COMPLETED_GROUP para garantir um log de auditoria completo |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |
