---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a9295290e8b0a26c407295c3b8f1ccb025159eac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504250"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas do HPC Cache devem usar uma chave gerenciada pelo cliente para criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gerencie a criptografia em repouso do Azure HPC Cache com chaves gerenciadas pelo cliente. Por padrão, os dados do cliente são criptografados com chaves gerenciadas pelo serviço, mas as chaves gerenciadas pelo cliente normalmente são necessárias para atender aos padrões de conformidade regulatória. As chaves gerenciadas pelo cliente permitem que os dados sejam criptografados com uma chave do Azure Key Vault criada por você e de sua propriedade. Você tem total controle e responsabilidade pelo ciclo de vida da chave, incluindo rotação e gerenciamento. |Auditoria, desabilitado, negação |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
