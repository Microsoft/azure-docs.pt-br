---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b9720165ce5ff27e1e67e074afa64d21a78efaa6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504146"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O grupo de contêineres da Instância de Contêiner do Azure deve ser implantado em uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Proteja a comunicação entre seus contêineres com as Redes Virtuais do Azure. Quando você especifica uma rede virtual, os recursos dentro da rede virtual podem se comunicar entre si de modo seguro e privado. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[O grupo de contêineres da Instância de Contêiner do Azure deve usar a chave gerenciada pelo cliente para criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Proteja seus contêineres com maior flexibilidade usando chaves gerenciadas pelo cliente. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. O uso de chaves gerenciadas pelo cliente fornece funcionalidades adicionais para controlar a rotação da principal chave de criptografia ou para apagar dados criptograficamente. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
