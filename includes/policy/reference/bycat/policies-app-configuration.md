---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3214e221f2ef198718528de5ac8c8b0383cb70d7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099964"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Configuração de Aplicativos deve usar uma chave gerenciada pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |As chaves gerenciadas pelo cliente oferecem proteção de dados aprimorada permitindo que você gerencie suas chaves de criptografia. Isso geralmente é necessário para atender aos requisitos de conformidade. |Audit, Deny, desabilitado |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[A Configuração de Aplicativos deve usar um link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |O Link Privado do Azure permite que você conecte sua rede virtual aos serviços do Azure sem um endereço IP público na origem ou no destino. A plataforma de link privado manipula a conectividade entre o consumidor e os serviços na rede de backbone do Azure. Mapeando seus pontos de extremidade privados para suas instâncias de configuração de aplicativos, em vez de todo o serviço, você também estará protegido contra riscos de vazamento de dados. Saiba mais em: [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint). |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
