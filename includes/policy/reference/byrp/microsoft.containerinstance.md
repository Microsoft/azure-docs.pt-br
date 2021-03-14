---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 581b0f6aeae99a16935716e470a0eeec0bbd74e0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "103422129"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Grupo de contêineres da instância de contêiner do Azure deve ser implantado em uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Proteger a comunicação entre seus contêineres com redes virtuais do Azure. Quando você especifica uma rede virtual, os recursos dentro da rede virtual podem se comunicar de forma segura e privada entre si. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[O grupo de contêineres da instância de contêiner do Azure deve usar a chave gerenciada pelo cliente para criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Proteja seus contêineres com maior flexibilidade usando chaves gerenciadas pelo cliente. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. O uso de chaves gerenciadas pelo cliente fornece funcionalidades adicionais para controlar a rotação da principal chave de criptografia ou para apagar dados criptograficamente. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
