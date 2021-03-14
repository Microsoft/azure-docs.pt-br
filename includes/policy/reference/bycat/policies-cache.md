---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c78986a5c2777c0db7cca314d5a49494f733e22
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428973"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Cache do Azure para Redis deve residir em uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |A implantação da Rede Virtual do Azure fornece segurança e isolamento aprimorados para seu Cache do Azure para Redis, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçada publicamente e só pode ser acessada de máquinas virtuais e aplicativos na rede virtual. |Audit, Deny, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
