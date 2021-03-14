---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 47bb1ed621f8039eb85d8b6d28e7ef6230e849ad
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636429"
---
## <a name="cmmc-level-3"></a>CMMC nível 3

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade regulatória do Azure Policy – CMMC nível 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Para saber mais sobre esse padrão de conformidade, confira [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domínio |ID do controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controle de acesso |AC.1.001 |Limitar o acesso do sistema de informações a usuários autorizados, processos que atuam em nome de usuários autorizados e dispositivos (incluindo outros sistemas de informações). |[CORS não deve permitir que todos os domínios acessem sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Controle de acesso |AC.1.002 |Limitar o acesso do sistema de informações aos tipos de transações e funções que os usuários autorizados têm permissão para executar. |[CORS não deve permitir que todos os domínios acessem sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Controle de acesso |AC.2.016 |Controle o fluxo de CUI de acordo com autorizações aprovadas. |[CORS não deve permitir que todos os domínios acessem sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Gerenciamento de configuração |CM.3.068 |Restrinja, desabilite ou impeça o uso de programas, funções, portas, protocolos e serviços não essenciais. |[CORS não deve permitir que todos os domínios acessem sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Proteção do Sistema e das Comunicações |SC.3.177 |Empregar criptografia validada por FIPS quando usada para proteger a confidencialidade da CUI. |[A API do Azure para FHIR deve usar uma chave gerenciada pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Proteção do Sistema e das Comunicações |SC.3.183 |Negar o tráfego de comunicações de rede por padrão e permitir o tráfego de comunicações de rede por exceção (ou seja, negar tudo, permitir por exceção). |[CORS não deve permitir que todos os domínios acessem sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

