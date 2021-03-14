---
title: Filtros de tópico do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como os assinantes podem definir quais mensagens desejam receber de um tópico especificando filtros.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f28b26ee112b47b9782823f6c79670dee9a3f082
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651656"
---
# <a name="topic-filters-and-actions"></a>Ações e filtros de tópico

Os assinantes podem definir quais mensagens desejam receber de um tópico. Essas mensagens são especificadas na forma de uma ou mais regras de assinatura nomeadas. Cada regra consiste em uma **condição de filtro** que seleciona mensagens específicas e, **opcionalmente** , contém uma **ação** que anota a mensagem selecionada. 

Todas as regras **sem ações** são combinadas usando uma `OR` condição e resultam em uma **única mensagem** na assinatura mesmo que você tenha várias regras de correspondência. 

Cada regra **com uma ação** produz uma cópia da mensagem. Essa mensagem terá uma propriedade chamada `RuleName` onde o valor é o nome da regra de correspondência. A ação pode adicionar ou atualizar propriedades ou excluir propriedades da mensagem original para produzir uma mensagem na assinatura. 

Considere o seguinte cenário:

- A assinatura tem cinco regras.
- Duas regras contêm ações.
- Três regras não contêm ações.

Neste exemplo, se você enviar uma mensagem que corresponda a todas as cinco regras, receberá três mensagens na assinatura. Essas são duas mensagens para duas regras com ações e uma mensagem para três regras sem ações. 

Cada assinatura de tópico recém-criada tem uma regra de assinatura padrão inicial. Se você não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o filtro **true** que permite que todas as mensagens sejam selecionadas na assinatura. A regra padrão não tem nenhuma ação de anotação associada.

## <a name="filters"></a>Filtros
O Barramento de Serviço dá suporte a três condições de filtro:

-   *Filtros SQL* – um **SqlFilter** contém uma expressão condicional do tipo SQL que é avaliada no agente em relação às propriedades do sistema e propriedades definidas pelo usuário das mensagens recebidas. Todas as propriedades de sistema devem ser prefixadas com `sys.` na expressão condicional. O [subconjunto de idiomas SQL para filtrar condições](service-bus-messaging-sql-filter.md) testa a existência de Propriedades ( `EXISTS` ), valores nulos ( `IS NULL` ), não lógicos e/ou operadores relacionais, aritmética de numérico simples e correspondência de padrão de texto simples com `LIKE` .
-   *Filtros boolianos* – o **TrueFilter** e **FalseFilter** fazem com que todas as mensagens recebidas (**true**) ou nenhuma das mensagens recebidas (**false**) seja selecionada para a assinatura. Esses dois filtros derivam do filtro SQL. 
-   *Filtros de correlação* – um **CorrelationFilter** contém um conjunto de condições que são comparadas com uma ou mais das propriedades do sistema e do usuário de uma mensagem recebida. Um uso comum é fazer a correspondência com a propriedade **CorrelationId** , mas o aplicativo também pode optar por corresponder às seguintes propriedades:

    - **ContentType**
     - **Rótulo**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Para**
     - qualquer propriedade definida pelo usuário. 
     
     Uma correspondência existe quando o valor de uma propriedade de uma mensagem recebida é igual ao valor especificado no filtro de correlação. Para expressões de cadeia de caracteres, a comparação diferencia maiúsculas de minúsculas. Ao especificar várias propriedades de correspondência, o filtro as combina como uma condição AND lógica, o que significa que para haver a correspondência do filtro, todas as condições devem corresponder.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não podem avaliar o corpo da mensagem.

Regras de filtro complexas requerem capacidade de processamento. Em particular, o uso de regras de filtro SQL causa uma menor taxa de transferência geral da mensagem na assinatura, no tópico e no nível do namespace. Sempre que possível, os aplicativos devem escolher filtros de correlação em filtros semelhantes ao SQL porque eles são muito mais eficientes no processamento e têm menos impacto na taxa de transferência.

## <a name="actions"></a>Ações

Com condições de filtro SQL, você pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [usa uma expressão do tipo SQL](service-bus-messaging-sql-filter.md) que depende vagamente da sintaxe da instrução SQL UPDATE. A ação é feita na mensagem depois que ela é correspondida e antes de a mensagem ser selecionada na assinatura. As alterações nas propriedades de mensagem são particulares para a mensagem copiada para a assinatura.

## <a name="usage-patterns"></a>Padrões de uso

O cenário de uso mais simples para um tópico é que cada assinatura obtém uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de difusão.

Os filtros e as ações permitem dois grupos de padrões adicionais: particionamento e roteamento.

O particionamento usa filtros para distribuir mensagens através de várias assinaturas de tópico existentes de maneira previsível e mutuamente exclusiva. O padrão de particionamento é usado quando um sistema é escalado horizontalmente para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que mantêm um subconjunto dos dados dos gerais. Por exemplo, informações de perfil do cliente. Com o particionamento, um editor envia a mensagem em um tópico sem a necessidade de qualquer conhecimento do modelo de particionamento. Depois, a mensagem é movida para a assinatura correta da qual ela pode ser recuperada pelo manipulador de mensagens da partição.

O roteamento usa filtros para distribuir mensagens através de assinaturas de tópico de maneira previsível, mas não necessariamente exclusiva. Em conjunto com o recurso de [encaminhamento automático](service-bus-auto-forwarding.md), os filtros de tópico podem ser usados para criar grafos de roteamento complexos dentro de um namespace do Barramento de Serviço para a distribuição de mensagens dentro de uma região do Azure. Com o Azure Functions ou os Aplicativos Lógicos do Azure atuando como uma ponte entre os namespaces do Barramento de Serviço do Azure, você pode criar tecnologias globais complexas com integração direta em aplicativos de linhas de negócios.

## <a name="examples"></a>Exemplos
Para obter exemplos, consulte [exemplos de filtro do barramento de serviço](service-bus-filter-examples.md).



> [!NOTE]
> Como o portal do Azure agora dá suporte à funcionalidade do Gerenciador do barramento de serviço, os filtros de assinatura podem ser criados ou editados no Portal. 

## <a name="next-steps"></a>Próximas etapas
Confira as seguintes amostras: 

- [.NET-tutorial de envio e recebimento básico com filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-filtros de tópico](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Modelo do Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)