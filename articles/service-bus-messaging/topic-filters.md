---
title: "Filtros de tópico do Barramento de Serviço do Azure | Microsoft Docs"
description: "Filtrar tópicos do Barramento de Serviço do Azure"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: sethm
ms.openlocfilehash: b3fe467b7d6ae9b207956ece4980bf558a69761f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="topic-filters-and-actions"></a>Ações e filtros de tópico

Os assinantes podem definir quais mensagens desejam receber de um tópico. Essas mensagens são especificadas na forma de uma ou várias regras de assinatura nomeadas. Cada regra consiste em uma condição que seleciona mensagens específicas e uma ação que anota a mensagem selecionada. Para cada condição de regra com correspondência, a assinatura produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra com correspondência.

Cada assinatura de tópico recém-criada tem uma regra de assinatura padrão inicial. Se você não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o filtro **true** que permite que todas as mensagens sejam selecionadas na assinatura. A regra padrão não tem nenhuma ação de anotação associada.

O Barramento de Serviço dá suporte a três condições de filtro:

-   *Filtros boolianos* – o **TrueFilter** e **FalseFilter** fazem com que todas as mensagens recebidas (**true**) ou nenhuma das mensagens recebidas (**false**) seja selecionada para a assinatura.

-   *Filtros SQL* – um **SqlFilter** contém uma expressão condicional do tipo SQL que é avaliada no agente em relação às propriedades do sistema e propriedades definidas pelo usuário das mensagens recebidas. Todas as propriedades de sistema devem ser prefixadas com `sys.` na expressão condicional. O [subconjunto da linguagem SQL para condições de filtro](service-bus-messaging-sql-filter.md) testa a existência de propriedades (EXISTS), bem como valores nulos (IS NULL), NOT/AND/OR lógicos, operadores relacionais, aritmética numérica simples e correspondência de padrões de texto simples com LIKE.

-   *Filtros de correlação* – um **CorrelationFilter** contém um conjunto de condições que são comparadas com uma ou mais das propriedades do sistema e do usuário de uma mensagem recebida. Um uso comum é para corresponder a propriedade **CorrelationId**, mas o aplicativo também optar por corresponder com **ContentType**, **Label**, **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **To** e quaisquer propriedades definidas pelo usuário. Uma correspondência existe quando o valor de uma propriedade de uma mensagem recebida é igual ao valor especificado no filtro de correlação. Para expressões de cadeia de caracteres, a comparação diferencia maiúsculas de minúsculas. Ao especificar várias propriedades de correspondência, o filtro as combina como uma condição AND lógica, o que significa que para haver a correspondência do filtro, todas as condições devem corresponder.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não podem avaliar o corpo da mensagem.

Regras de filtro complexas requerem capacidade de processamento. Em particular, o uso de regras de filtro SQL resulta em uma taxa de transferência de mensagem geral menor no nível da assinatura, do tópico e do namespace. Sempre que possível, os aplicativos devem escolher os filtros de correlação em detrimento dos filtros do tipo SQL, pois eles são muito mais eficientes no processamento e, portanto, têm menos impacto na taxa de transferência.

## <a name="actions"></a>Ações

Com condições de filtro SQL e apenas com elas, você pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [usa uma expressão do tipo SQL](service-bus-messaging-sql-filter.md) que depende vagamente da sintaxe da instrução SQL UPDATE. A ação é executada na mensagem após ela ter sido correspondida e antes de a mensagem ser selecionada no tópico. As alterações nas propriedades de mensagem são particulares para a mensagem copiada para a assinatura.

## <a name="usage-patterns"></a>Padrões de uso

O cenário de uso mais simples para um tópico é que cada assinatura obtém uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de difusão.

Os filtros e as ações permitem dois grupos de padrões adicionais: particionamento e roteamento.

O particionamento usa filtros para distribuir mensagens através de várias assinaturas de tópico existentes de maneira previsível e mutuamente exclusiva. O padrão de particionamento é usado quando um sistema é escalado horizontalmente para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que mantêm um subconjunto dos dados dos gerais. Por exemplo, informações de perfil do cliente. Com o particionamento, um editor envia a mensagem em um tópico sem a necessidade de qualquer conhecimento do modelo de particionamento. Depois, a mensagem é movida para a assinatura correta da qual ela pode ser recuperada pelo manipulador de mensagens da partição.

O roteamento usa filtros para distribuir mensagens através de assinaturas de tópico de maneira previsível, mas não necessariamente exclusiva. Em conjunto com o recurso de [encaminhamento automático](service-bus-auto-forwarding.md), os filtros de tópico podem ser usados para criar gráficos de roteamento complexos dentro de um namespace do Barramento de Serviço para a distribuição de mensagens dentro de uma região do Azure. Com o Azure Functions ou os Aplicativos Lógicos do Azure atuando como uma ponte entre os namespaces do Barramento de Serviço do Azure, você pode criar tecnologias globais complexas com integração direta em linhas de aplicativos de negócios.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Sintaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)