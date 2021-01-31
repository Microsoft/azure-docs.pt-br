---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d743454bb7fa39be8a6e73edbbc053776505ef1a
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218869"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os trabalhos do Azure Data Box devem habilitar a criptografia dupla para dados inativos no dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Habilite uma segunda camada de criptografia baseada em software para dados inativos no dispositivo. O dispositivo já está protegido por meio da criptografia AES de 256 bits para dados inativos. Essa opção adiciona uma segunda camada de criptografia de dados. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Os trabalhos do Azure Data Box devem usar uma chave gerenciada pelo cliente para criptografar a senha de desbloqueio do dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Use uma chave gerenciada pelo cliente para controlar a criptografia da senha de desbloqueio do dispositivo para o Azure Data Box. As chaves gerenciadas pelo cliente também ajudam a gerenciar o acesso à senha de desbloqueio do dispositivo pelo serviço Data Box para preparar o dispositivo e copiar dados de maneira automatizada. Os dados no próprio dispositivo já estão criptografados em repouso com a criptografia AES de 256 bits e a senha de desbloqueio do dispositivo é criptografada por padrão com uma chave gerenciada da Microsoft. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
