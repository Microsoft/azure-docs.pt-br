---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2d28e331b576c9152593b556864fb2c4b50a3742
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807701"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os data factories do Azure devem ser criptografados com uma CMK (chave gerenciada pelo cliente)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Use CMKs (chaves gerenciadas pelo cliente) para gerenciar a criptografia em repouso do seu Azure Data Factory. Por padrão, os dados do cliente são criptografados com chaves gerenciadas pelo serviço, mas as CMK normalmente são necessárias para atender aos padrões de conformidade regulatória. CMKs permitem que os dados sejam criptografados com uma chave do Azure Key Vault criada por você e de sua propriedade. Você tem total controle e responsabilidade pelo ciclo de vida da chave, incluindo rotação e gerenciamento. Saiba mais sobre a criptografia CMK em [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk). |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
