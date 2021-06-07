---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f5b70c5da99fb4f8db32b234c032c13bd4585a0d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513550"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
