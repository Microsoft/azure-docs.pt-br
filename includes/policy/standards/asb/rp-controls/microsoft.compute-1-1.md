---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0c1fea1c608b2d3c3ee8209e97f8e50807d2c3af
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512980"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O encaminhamento IP na máquina virtual deve ser desabilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |A habilitação do encaminhamento de IP na NIC de uma máquina virtual permite que o computador receba o tráfego endereçado a outros destinos. O encaminhamento de IP raramente é necessário (por exemplo, ao usar a VM como uma solução de virtualização de rede) e, portanto, isso deve ser examinado pela equipe de segurança de rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Portas de gerenciamento devem ser fechadas nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |As portas abertas de gerenciamento remoto estão expondo sua VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam obter credenciais por força bruta para obter acesso de administrador à máquina. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
