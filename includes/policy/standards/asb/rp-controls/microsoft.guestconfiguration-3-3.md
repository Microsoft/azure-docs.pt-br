---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: aaee9ac40909077bbbf33d57b4d623cd32c5fa09
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "101740334"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar computadores Windows que não têm membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local não contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditar os computadores Windows que têm contas extras no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver membros que não estejam listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Auditar computadores Windows que têm os membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
