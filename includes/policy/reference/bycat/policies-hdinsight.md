---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60278160646de1b80d224cabc6d781872a5624d9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498472"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os clusters do Azure HDInsight devem ser injetados em uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |A injeção de clusters do Azure HDInsight em uma rede virtual desbloqueia recursos de segurança e rede avançados do HDInsight e fornece controle sobre sua configuração de segurança de rede. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Os clusters do Azure HDInsight devem usar chaves gerenciadas pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Use chaves gerenciadas pelo cliente para gerenciar a criptografia em repouso dos seus clusters do Azure HDInsight. Por padrão, os dados do cliente são criptografados com chaves gerenciadas pelo serviço, mas as chaves gerenciadas pelo cliente normalmente são necessárias para atender aos padrões de conformidade regulatória. As chaves gerenciadas pelo cliente permitem que os dados sejam criptografados com uma chave do Azure Key Vault criada por você e de sua propriedade. Você tem total controle e responsabilidade pelo ciclo de vida da chave, incluindo rotação e gerenciamento. Saiba mais em [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk). |Audit, Deny, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Os clusters do Azure HDInsight devem usar criptografia no host para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |A habilitação da criptografia no host ajuda a proteger seus dados para atender aos compromissos de conformidade e segurança da sua organização. Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Os clusters do Azure HDInsight devem usar a criptografia em trânsito para criptografar a comunicação entre os nós de cluster do Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Os dados podem ser adulterados durante a transmissão entre os nós de cluster do Azure HDInsight. A habilitação da criptografia em trânsito resolve problemas de uso indevido e violação durante essa transmissão. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
