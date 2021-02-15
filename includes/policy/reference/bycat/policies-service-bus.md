---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e82953bf8aae3d24f058d1cf28e09afe61c92fba
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220203"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todas as regras de autorização, exceto a RootManageSharedAccessKey, devem ser removidas do namespace do Barramento de Serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Os clientes do Service Bus não devem usar uma diretiva de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica |Audit, Deny, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Os logs de diagnóstico no Barramento de Serviço devem ser habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Os namespaces do Barramento de Serviço Premium devem usar uma chave gerenciada pelo cliente para criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |O Barramento de Serviço do Azure dá suporte à opção de criptografar dados inativos com chaves gerenciadas pela Microsoft (padrão) ou chaves gerenciadas pelo cliente. Optar por criptografar dados usando chaves gerenciadas pelo cliente permite que você atribua, gire, desabilite e revogue o acesso às chaves que o Barramento de Serviço usará para criptografar dados em seu namespace. Observe que o Barramento de Serviço dá suporte apenas à criptografia com chaves gerenciadas pelo cliente para namespaces premium. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
