---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: eda285d029910efa43cbd295765e5c4d63da4c50
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561529"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Use chaves gerenciadas pelo cliente para gerenciar a criptografia em repouso no conteúdo dos seus Registros. Por padrão, os dados são criptografados em repouso com chaves gerenciadas pelo serviço, mas as CMK (chaves gerenciada pelo cliente) normalmente são necessárias para atender aos padrões de conformidade regulatória. CMKs permitem que os dados sejam criptografados com uma chave do Azure Key Vault criada por você e de sua propriedade. Você tem total controle e responsabilidade pelo ciclo de vida da chave, incluindo rotação e gerenciamento. Saiba mais sobre a criptografia CMK em [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registros de contêiner não devem permitir acesso irrestrito à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Por padrão, os registros de contêiner do Azure aceitam conexões pela Internet de hosts em qualquer rede. Para proteger seus Registros contra possíveis ameaças, permita o acesso somente de endereços ou intervalos de endereços IP públicos específicos. Se o Registro não tiver uma regra de IP/firewall ou uma rede virtual configurada, ele será exibido nos recursos não íntegros. Saiba mais sobre as regras de rede do Registro de Contêiner aqui: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e aqui [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registros de contêiner devem usar um link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |O Link Privado do Azure permite que você conecte sua rede virtual aos serviços do Azure sem um endereço IP público na origem ou no destino. A plataforma de link privado manipula a conectividade entre o consumidor e os serviços pela rede de backbone do Azure. Por meio do mapeamento de pontos de extremidade privados para seus registros de contêiner, em vez de todo o serviço, você também estará protegido contra riscos de vazamento de dados. Saiba mais em: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
