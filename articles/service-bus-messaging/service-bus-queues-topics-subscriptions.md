---
title: Mensagens do barramento de serviço do Azure – filas, tópicos e assinaturas
description: Este artigo fornece uma visão geral das entidades de mensagens do barramento de serviço do Azure (fila, tópicos e assinaturas).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b8fb68509ad920fc6911290377f49b89ec610b58
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096331"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e assinaturas do Barramento de Serviço
O Barramento de Serviço do Azure oferece suporte a um conjunto de tecnologias middleware orientado a mensagens, baseado em nuvem, incluindo o serviço de enfileiramento de mensagens confiável e o sistema de mensagens de publicação/assinatura durável. Esses recursos de mensagens orientadas podem ser considerados como recursos de mensagens dissociados que dão suporte a cenários de publicação-assinatura, desacoplamento temporal e balanceamento de carga usando a carga de trabalho de mensagens do barramento de serviço. A comunicação dissociada tem muitas vantagens. Por exemplo, clientes e servidores podem se conectar conforme necessário e realizar suas operações de maneira assíncrona.

As entidades de mensagens que formam o núcleo dos recursos de mensagens no barramento de serviço são **filas**, **Tópicos e assinaturas** e regras/ações.

## <a name="queues"></a>Filas
As filas oferecem entrega de mensagem do tipo **FIFO (primeiro a entrar, primeiro a sair)** para um ou mais consumidores concorrentes. Ou seja, os destinatários normalmente recebem e processam mensagens na ordem em que foram adicionados à fila. E, somente um consumidor de mensagem recebe e processa cada mensagem. Um dos principais benefícios do uso de filas é obter **um desacoplamento temporal dos componentes do aplicativo**. Em outras palavras, os produtores (remetentes) e os consumidores (receptores) não precisam enviar e receber mensagens ao mesmo tempo. Isso ocorre porque as mensagens são armazenadas permanentemente na fila. Além disso, o produtor não precisa esperar por uma resposta do consumidor para continuar a processar e enviar mensagens.

Um benefício relacionado é o **nivelamento de carga**, que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes. Em muitos aplicativos, a carga do sistema varia ao longo do tempo. No entanto, o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. A intermediação de produtores de mensagens e consumidores com uma fila significa que o aplicativo de consumo só precisa ser capaz de lidar com a carga média em vez da carga de pico. A profundidade da fila aumentará e diminuirá conforme a carga de entrada variar. Esse recurso economiza dinheiro diretamente com relação à quantidade de infraestrutura necessária para atender à carga do aplicativo. À medida que a carga aumenta, mais processos de trabalho poderão ser adicionados à leitura da fila. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o melhor uso dos computadores de trabalho, mesmo que os computadores de trabalho com processamento de energia recebam mensagens em sua própria taxa máxima. Esse padrão é geralmente denominado de **consumidor concorrente**.

A utilização de filas para intermediar entre produtores e consumidores oferece um acoplamento flexível inerente entre os componentes. Como produtores e consumidores não estão cientes uns dos outros, um consumidor pode ser atualizado sem ter nenhum efeito no produtor.

### <a name="create-queues"></a>Criar filas
Você pode criar filas usando os modelos [portal do Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)ou [Resource Manager](service-bus-resource-manager-namespace-queue.md). Em seguida, envie e receba mensagens usando clientes escritos em [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [python](service-bus-python-how-to-use-queues.md), [JavaScript](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)e [Ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Modos de recepção
Você pode especificar dois modos diferentes em que o barramento de serviço recebe mensagens.

- **Receber e excluir**. Nesse modo, quando o barramento de serviço recebe a solicitação do consumidor, ele marca a mensagem como sendo consumida e a retorna ao aplicativo consumidor. Esse modo é o modelo mais simples. Ele funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem se ocorrer uma falha. Para reconhecer esse cenário, considere um cenário no qual o consumidor emite a solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Como o barramento de serviço marca a mensagem como sendo consumida, o aplicativo começa a consumir mensagens após a reinicialização. Ela perderá a mensagem consumida antes da falha.
- **Bloqueio de inspeção**. Nesse modo, a operação de recebimento se torna duas etapas, o que torna possível dar suporte a aplicativos que não podem tolerar mensagens ausentes. 
    1. Localiza a próxima mensagem a ser consumida, a **bloqueia** para impedir que outros consumidores a recebam e, em seguida, retornam a mensagem para o aplicativo. 
    1. Depois que o aplicativo termina de processar a mensagem, ele solicita que o serviço do barramento de serviço conclua o segundo estágio do processo de recebimento. Em seguida, o serviço **marca a mensagem como sendo consumida**. 

        Se o aplicativo não puder processar a mensagem por algum motivo, ele poderá solicitar que o serviço do barramento de serviço **abandone** a mensagem. O barramento de serviço **desbloqueia** a mensagem e a disponibiliza para ser recebida novamente, seja pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um **tempo limite** associado ao bloqueio. Se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio, o barramento de serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente.

        Se o aplicativo falhar depois de processar a mensagem, mas antes de solicitar que o serviço do barramento de serviço conclua a mensagem, o barramento de serviço reentregará a mensagem ao aplicativo quando ele for reiniciado. Esse processo é geralmente chamado de processamento **pelo menos uma vez** . Ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações a mesma mensagem poderá ser entregue novamente. Se seu cenário não puder tolerar o processamento duplicado, adicione lógica adicional em seu aplicativo para detectar duplicatas. Para saber mais, confira [Detecção de duplicatas](duplicate-detection.md). Esse recurso é conhecido como processamento **exatamente uma vez** .

        > [!NOTE]
        > Para obter mais informações sobre esses dois modos, consulte [liquidar operações de recebimento](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Tópicos e assinaturas
Uma fila permite o processamento de uma mensagem por um único consumidor. Em contraste com as filas, os tópicos e as assinaturas fornecem uma forma de comunicação de um para muitos em um padrão de **publicação e assinatura** . É útil para dimensionar para um grande número de destinatários. Cada mensagem publicada é disponibilizada para cada assinatura registrada com o tópico. O Publicador envia uma mensagem para um tópico e um ou mais assinantes recebem uma cópia da mensagem, dependendo das regras de filtro definidas nessas assinaturas. As assinaturas podem usar filtros adicionais para restringir as mensagens que desejam receber. Os editores enviam mensagens para um tópico da mesma forma que enviam mensagens para uma fila. No entanto, os consumidores não recebem mensagens diretamente do tópico. Em vez disso, os consumidores recebem mensagens de assinaturas do tópico. Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. Os consumidores recebem mensagens de uma assinatura de forma idêntica à maneira como recebem mensagens de uma fila.

A funcionalidade de envio de mensagens de uma fila é mapeada diretamente para um tópico e sua funcionalidade de recebimento de mensagens é mapeada para uma assinatura. Entre outras coisas, esse recurso significa que as assinaturas dão suporte aos mesmos padrões descritos anteriormente nesta seção em relação a filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e assinaturas
A criação de um tópico é semelhante à criação de uma fila, conforme descrito na seção anterior. Você pode criar tópicos e assinaturas usando os modelos [portal do Azure](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-tutorial-topics-subscriptions-cli.md)ou [Resource Manager](service-bus-resource-manager-namespace-topic.md). Em seguida, envie mensagens para um tópico e receba mensagens de assinaturas usando clientes escritos em [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [python](service-bus-python-how-to-use-topics-subscriptions.md), [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)e [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Regras e ações
Em muitos cenários, as mensagens com características específicas precisam ser processadas de maneiras diferentes. Para habilitar esse processamento, você pode configurar assinaturas para localizar as mensagens com as propriedades desejáveis e, em seguida, realizar determinadas modificações nessas propriedades. Embora as assinaturas do Barramento de Serviço vejam todas as mensagens enviadas para o tópico, você só poderá copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Essa filtragem é realizada usando filtros de assinatura. Tais modificações são chamadas de **ações de filtro**. Quando uma assinatura é criada, você pode fornecer uma expressão de filtro que opere nas propriedades da mensagem. As propriedades podem ser ambas as propriedades do sistema (por exemplo, **rótulo**) e propriedades de aplicativo personalizadas (por exemplo, **StoreName**.) A expressão de filtro SQL é opcional nesse caso. Sem uma expressão de filtro SQL, qualquer ação de filtro definida em uma assinatura será feita em todas as mensagens dessa assinatura.

Para um exemplo de trabalho completo, consulte o [exemplo TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no GitHub.

Para saber mais sobre possíveis valores de filtro, consulte a documentação das classes [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="java-message-service-jms-20-entities"></a>Entidades do Java Message Service (JMS) 2,0
As entidades a seguir podem ser acessadas por meio da API do Java Message Service (JMS) 2,0.

  * Filas temporárias
  * Tópicos temporários
  * Assinaturas duráveis compartilhadas
  * Assinaturas duráveis não compartilhadas
  * Assinaturas não duráveis compartilhadas
  * Assinaturas não duráveis não compartilhadas

Saiba mais sobre as [entidades JMS 2,0](java-message-service-20-entities.md) e sobre como [usá-las](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Início rápido: Enviar e receber mensagens usando o portal do Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)


