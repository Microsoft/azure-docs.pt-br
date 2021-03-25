---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 37a6a8c9f0afbaf5ac359287c745bd5d28d1a034
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032886"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A criptografia em repouso do Azure Data Explorer deve usar uma chave gerenciada pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |A habilitação da criptografia em repouso por meio de uma chave gerenciada pelo cliente no cluster do Azure Data Explorer fornece controle adicional sobre a chave que está sendo usada pela criptografia em repouso. Esse recurso é muitas vezes aplicável a clientes com requisitos de conformidade especiais e exige um Key Vault para gerenciar as chaves. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[A criptografia de disco deve ser habilitada no Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |A habilitação da criptografia de disco ajuda a proteger seus dados para atender aos compromissos de conformidade e segurança da sua organização. |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[A criptografia dupla deve ser habilitada no Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |A habilitação da criptografia dupla ajuda a proteger seus dados para atender aos compromissos de conformidade e segurança da sua organização. Quando a criptografia dupla é habilitada, os dados da conta de armazenamento são criptografados duas vezes, uma vez no nível de serviço e outro no nível de infraestrutura, por meio de dois algoritmos de criptografia diferentes e duas chaves distintas. |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[A injeção de rede virtual deve ser habilitada no Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Proteja seu perímetro de rede com a injeção de rede virtual, que permite impor regras de grupo de segurança de rede, conectar-se no local e proteger suas fontes de conexão de dados com pontos de extremidade de serviço. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
