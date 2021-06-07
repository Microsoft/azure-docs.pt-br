---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c68188ec1ee0c0e3a8d174bcb8c19d8a450c22dc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498427"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os dispositivos do Azure Stack Edge devem usar criptografia dupla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Para proteger os dados inativos no dispositivo, ele deve ser duplamente criptografado, o acesso aos dados deve ser controlado e, depois que o dispositivo for desativado, os dados devem ser apagados com segurança dos discos de dados. A criptografia dupla é o uso de duas camadas de criptografia: Criptografia de 256 bits BitLocker XTS-AES nos volumes de dados e criptografia interna dos discos rígidos. Saiba mais na documentação de visão geral de segurança para o dispositivo Stack Edge específico. |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
