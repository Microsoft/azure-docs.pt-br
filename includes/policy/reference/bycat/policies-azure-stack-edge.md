---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83f33626927d88ef9de8a18da5e6f66839704e08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428969"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os dispositivos do Azure Stack Edge devem usar criptografia dupla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Para proteger os dados inativos no dispositivo, ele deve ser duplamente criptografado, o acesso aos dados deve ser controlado e, depois que o dispositivo for desativado, os dados devem ser apagados com segurança dos discos de dados. A criptografia dupla é o uso de duas camadas de criptografia: Criptografia de 256 bits BitLocker XTS-AES nos volumes de dados e criptografia interna dos discos rígidos. Saiba mais na documentação de visão geral de segurança para o dispositivo Stack Edge específico. |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
