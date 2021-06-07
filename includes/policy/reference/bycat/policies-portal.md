---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e28dc20fe5e789bbaa5aad9402d37f039eabe6e5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498485"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os painéis compartilhados não devem ter blocos de Markdown com um conteúdo embutido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não permita a criação de um painel compartilhado que tenha um conteúdo embutido em blocos de Markdown e imponha o armazenamento do conteúdo como um arquivo de Markdown hospedado online. Se você usar um conteúdo embutido no bloco de Markdown, não poderá gerenciar a criptografia do conteúdo. Ao configurar seu armazenamento, você poderá usar criptografia, criptografia dupla e, até mesmo, trazer suas chaves. A habilitação dessa política restringe os usuários de usar a versão 2020-09-01-preview ou superior da API REST dos painéis compartilhados. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
