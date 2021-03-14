---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2471564ead93f8b7aa678eb7eece07abea30b96
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419875"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de cache do HPC devem usar a chave gerenciada pelo cliente para criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gerencie a criptografia em repouso do cache do Azure HPC com chaves gerenciadas pelo cliente. Por padrão, os dados do cliente são criptografados com chaves gerenciadas pelo serviço, mas as chaves gerenciadas pelo cliente normalmente são necessárias para atender aos padrões de conformidade regulatória. As chaves gerenciadas pelo cliente permitem que os dados sejam criptografados com uma chave do Azure Key Vault criada por você e de sua propriedade. Você tem total controle e responsabilidade pelo ciclo de vida da chave, incluindo rotação e gerenciamento. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
