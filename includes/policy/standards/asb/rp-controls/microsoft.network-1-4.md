---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e287e21ca54f086d65007ae62f76cf3cfe05fdf7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021230"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todo o tráfego da Internet deve ser roteado por meio do Firewall do Azure implantado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |A Central de Segurança do Azure identificou que algumas de suas sub-redes não estão protegidas com um firewall de última geração. Proteja suas sub-redes de ameaças potenciais restringindo o acesso a elas com o Firewall do Azure ou um firewall de última geração compatível |AuditIfNotExists, desabilitado |[3.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[A Proteção contra DDoS do Azure Standard deve estar habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |A proteção contra DDoS padrão deve ser habilitada para todas as redes virtuais com uma sub-rede que é parte de um gateway de aplicativo com um IP público. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
