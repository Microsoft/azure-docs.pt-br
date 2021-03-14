---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d419d8e338228132a4aa8a32b7e29d8fa2f46ca
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429023"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os painéis compartilhados não devem ter blocos de Markdown com um conteúdo embutido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não permita a criação de um painel compartilhado que tenha um conteúdo embutido em blocos de Markdown e imponha o armazenamento do conteúdo como um arquivo de Markdown hospedado online. Se você usar um conteúdo embutido no bloco de Markdown, não poderá gerenciar a criptografia do conteúdo. Ao configurar seu armazenamento, você poderá usar criptografia, criptografia dupla e, até mesmo, trazer suas chaves. A habilitação dessa política restringe os usuários de usar a versão 2020-09-01-preview ou superior da API REST dos painéis compartilhados. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
