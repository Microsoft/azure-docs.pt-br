---
title: Alterações de notificação de detecção inteligente-Aplicativo Azure insights
description: Altere para os destinatários de notificação padrão da detecção inteligente. A detecção inteligente permite monitorar rastreamentos de aplicativos com Aplicativo Azure informações sobre padrões incomuns na telemetria de rastreamento.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: fa14f0dd40a30a4750d9bb102d8e67608f958135
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734495"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alteração de notificações por email de detecção inteligente

Com base nos comentários dos clientes, no dia 1º de abril de 2019, alteraremos as funções padrão que recebem notificações por email de detecção inteligente.

## <a name="what-is-changing"></a>O que está mudando?

Atualmente, as notificações por email de detecção inteligente são enviadas por padrão para as funções de _proprietário da assinatura_, _colaborador da assinatura_ e _leitor da assinatura_. Essas funções geralmente incluem usuários que não estão ativamente envolvidos no monitoramento, o que faz com que muitos desses usuários recebam notificações desnecessariamente. Para melhorar essa experiência, estamos fazendo uma alteração para que as notificações por email sejam enviadas por padrão apenas para as funções de [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) e [colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor).

## <a name="scope-of-this-change"></a>Escopo da alteração

Essa alteração afetará todas as regras de detecção inteligente, exceto as seguintes:

* Regras de detecção inteligente marcadas como versão prévia. Essas regras de detecção inteligente não dão suporte a notificações por email no momento.

* Regra de anomalias de falha.

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para essa alteração?

Para garantir que as notificações por email da detecção inteligente sejam enviadas aos usuários relevantes, esses usuários devem ser atribuídos ao [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) ou às funções de colaborador de [monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) da assinatura.

Para atribuir usuários ao leitor de monitoramento ou às funções de colaborador de monitoramento por meio do portal do Azure, siga as etapas descritas no artigo [atribuir funções do Azure](../../role-based-access-control/role-assignments-portal.md) . Certifique-se de selecionar _leitor de monitoramento_ ou _colaborador de monitoramento_ como a função atribuída aos usuários.

> [!NOTE]
> Destinatários específicos das notificações de detecção inteligente, configurados usando a opção _Destinatários de email adicionais_ nas configurações de regra, não serão afetados por essa alteração. Esses destinatários continuarão recebendo as notificações por email.

Se você tiver perguntas ou dúvidas sobre essa alteração, [entre em contato conosco](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a detecção inteligente:

- [Anomalias de falha](./proactive-failure-diagnostics.md)
- [Vazamentos de memória](./proactive-potential-memory-leak.md)
- [Anomalias de desempenho](./proactive-performance-diagnostics.md)

