---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f9a714936d0851d31a7b04001bd4fc4cc08c36d7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500372"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O serviço de Gerenciamento de API deve usar um SKU que dê suporte a redes virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Usando os SKUs compatíveis do Gerenciamento de API, a implantação do serviço em uma rede virtual desbloqueia recursos avançados de segurança e de rede do Gerenciamento de API, o que oferece maior controle sobre a configuração de segurança da rede. Saiba mais em: [https://aka.ms/apimvnet](https://aka.ms/apimvnet). |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[Os serviços do Gerenciamento de API devem usar uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |A implantação da Rede Virtual do Azure fornece isolamento e segurança aprimorada e permite que você coloque o serviço de Gerenciamento de API em uma rede roteável não ligada à Internet para a qual você controla o acesso. Essas redes podem ser conectadas às suas redes locais usando várias tecnologias de VPN, o que permite o acesso aos seus serviços de back-end na rede e/ou locais. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessados pela Internet ou somente na rede virtual. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
