---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512832"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os cofres de chaves devem ter a proteção contra limpeza habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A exclusão mal-intencionada de um cofre de chaves pode levar à perda permanente de dados. Um funcionário mal-intencionado em sua organização pode potencialmente excluir e limpar os cofres de chaves. A proteção de limpeza protege você contra ataques de invasores impondo um período de retenção obrigatório para cofres de chaves de exclusão temporária. Ninguém dentro de sua organização nem a Microsoft poderá limpar os cofres de chaves durante o período de retenção de exclusão temporária. |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
