---
title: Usar o Barramento de Serviço do Azure para executar operações de dados no Barramento de Serviço (versão prévia)
description: Este artigo fornece informações sobre como usar o Gerenciador do Barramento de Serviço do Azure baseado em portal para acessar os dados do Barramento do Serviço do Azure.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738949"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Usar o Gerenciador do Barramento de Serviço para realizar as operações de dados no Barramento de Serviço (versão prévia)

## <a name="overview"></a>Visão geral

O Barramento de Serviço do Azure permite que os aplicativos cliente do remetente e do destinatário desassociem sua lógica de negócios com o uso de semântica de ponto a ponto (fila) e de publicação-assinatura (tópico-assinatura) familiares.

As operações executadas em um namespace do Barramento de Serviço do Azure são de dois tipos 
   * Operações de gerenciamento – Criar, atualizar, excluir do namespace do Barramento de Serviço, filas, tópicos e assinaturas.
   * Operações de dados - Envia e recebe mensagens de filas, tópicos e assinaturas.

O Gerenciador do Barramento de Serviço do Azure expande a funcionalidade do portal além das operações de gerenciamento para dar suporte a operações de dados (enviar, receber, inspecionar) nas filas, tópicos e assinaturas (e suas subentidades de mensagens mortas) - diretamente do próprio portal do Azure.

> [!NOTE]
> Este artigo destaca a funcionalidade do Azure Service Bus Explorer que reside no portal do Azure.
>
> A ferramenta Gerenciador do Barramento de Serviço do Azure ***não*** é a ferramenta OSS de propriedade da comunidade [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Pré-requisitos

Para usar a ferramenta Service Bus Explorer, você precisará executar as seguintes tarefas: 

- Provisionar um namespace do barramento de serviço do Azure.
- Crie uma fila para enviar e receber mensagens do ou de um tópico com uma assinatura para testar a funcionalidade. Para saber como criar filas, tópicos e assinaturas, consulte os seguintes artigos: 
    - [Início rápido - Criar filas ](service-bus-quickstart-portal.md)
    - [Início rápido - Criar tópicos ](service-bus-quickstart-topics-subscriptions-portal.md)
- Verifique se você é membro de uma dessas funções no namespace: 
    - [Proprietário de dados do barramento de serviço](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Colaborador](../role-based-access-control/built-in-roles.md#contributor) 
    - [Proprietário](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Usar o Gerenciador de Barramento de Serviço

Para usar o Gerenciador do Barramento de Serviço do Azure, você precisa navegar até o namespace do Barramento de Serviço no qual você deseja executar as operações enviar, inspecionar e receber.

Se você estiver procurando executar operações em uma Fila, escolha **'Filas'** no menu de navegação. Se você estiver procurando executar operações em relação a um tópico (e suas assinaturas relacionadas), escolha **Tópicos**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Seleção de entidade":::

Depois de escolher as **'Filas'** ou **'Tópicos'** , escolha a fila ou o tópico específico.

Selecione o **"Gerenciador de Barramento de Serviço (versão prévia)"** no menu de navegação à esquerda

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu de navegação à esquerda do Gerenciador SB":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Enviar uma mensagem a uma fila ou um tópico

Para enviar uma mensagem para uma **fila** ou um **tópico**, clique na guia **_Enviar_** no Gerenciador do barramento de serviço.

Para compor uma mensagem aqui - 

1. Escolha o **Tipo de conteúdo** como 'Texto/Simples', 'Aplicativo/Xml' ou 'Aplicativo/Json'.
2. Adicione o **Conteúdo** da mensagem. Verifique se ele corresponde ao **Tipo de conteúdo** definido anteriormente.
3. Defina **Propriedades avançadas** (opcional) - isso inclui ID de correlação, ID da mensagem, Rótulo, ReplyTo, TTL (Vida útil) e Tempo de enfileiramento agendado (para mensagens agendadas).
4. Defina **Propriedades personalizadas** - pode ser qualquer propriedade de usuário definida em relação a uma chave de dicionário.

Depois que a mensagem tiver sido composta, pressione enviar.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Compor mensagem":::

Quando a operação de envio for concluída com êxito, 

* Se estiver enviando para a fila, o contador de métrica de **Mensagens ativas** será incrementado.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Se enviar para o Tópico, o contador de métricas de **Mensagens ativas** será incrementado na assinatura para a qual a mensagem foi roteada.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Recebe uma mensagem de uma Fila

A função de recepção no Gerenciador do Barramento de Serviço permite receber uma única mensagem por vez. A operação de recebimento é executada usando o modo **ReceiveAndDelete**.

> [!IMPORTANT]
> Observe que a operação de Recebimento executada pelo Gerenciador do Barramento de Serviço é um ***recebimento destrutivo***, ou seja, a mensagem é removida da fila quando ela é exibida na ferramenta Gerenciador do Barramento de Serviço.
>
> Para procurar mensagens sem removê-las da fila, considere o uso da funcionalidade ***Espiar***.
>

Para receber uma mensagem de uma fila (ou sua subfila de mensagens mortas) 

1. Clique na guia ***Receber*** no Gerenciador do barramento de serviço.
2. Verifique as métricas para ver se há **Mensagens ativas** ou **Mensagens mortas** a serem recebidas.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Escolha entre a subfila ***fila** _ ou _ *_mensagens mortas_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Clique no botão ***receber** _, seguido de _ *_Sim_** para confirmar a operação ' receber e excluir '.


Quando a operação de recebimento for bem-sucedida, os detalhes da mensagem serão exibidos na grade, como mostrado abaixo. Você pode selecionar a mensagem da grade para exibir seus detalhes.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Captura de tela da janela filas no Gerenciador do barramento de serviço do Azure com detalhes da mensagem exibidos para uma mensagem ativa selecionada na fila.":::


### <a name="peeking-a-message-from-a-queue"></a>Espiar uma mensagem de uma Fila

Com a funcionalidade de Espiar, você pode usar o Gerenciador do Barramento de Serviço para exibir as 32 mensagens principais em uma fila ou na fila de mensagens mortas.

1. Para espiar a mensagem em uma fila, clique na guia ***Espiar*** no Gerenciador do Barramento de Serviço.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Verifique as métricas para ver se há **Mensagens ativas** ou **Mensagens mortas** a serem recebidas.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Em seguida, escolha entre a subfila ***fila** _ ou _ *_mensagens mortas_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Clique no botão ***Espiar***. 

Depois que a operação de inspeção for concluída, até 32 mensagens aparecerão na grade, como mostrado abaixo. Para exibir os detalhes de uma mensagem específica, selecione-a na grade. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Como Peek não é uma operação destrutiva, a mensagem **não** será removida da fila.
>

### <a name="receiving-a-message-from-a-subscription"></a>Receber mensagens de uma assinatura

Assim como ocorre com uma fila, a operação ***Receber*** pode ser executada em uma assinatura (ou em sua entidade de mensagens mortas). No entanto, como uma assinatura reside no contexto do tópico, a operação de recebimento é executada navegando até o Gerenciador do Barramento de Serviço para um determinado Tópico.

> [!IMPORTANT]
> Observe que a operação de Recebimento executada pelo Gerenciador do Barramento de Serviço é um ***recebimento destrutivo***, ou seja, a mensagem é removida da fila quando ela é exibida na ferramenta Gerenciador do Barramento de Serviço.
>
> Para procurar mensagens sem removê-las da fila, considere o uso da funcionalidade ***Espiar***.
>

1. Clique na guia ***receber** _ e selecione a específica _ *_assinatura_** no seletor suspenso.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Escolha entre a subentidade ***Subscription** _ ou _ *_mensagens mortas_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Clique no botão ***receber** _, seguido de _ *_Sim_** para confirmar a operação ' receber e excluir '.

Quando a operação de recebimento for bem-sucedida, os detalhes da mensagem recebida serão exibidos na grade, como mostrado abaixo. Para exibir os detalhes da mensagem, clique na mensagem.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Captura de tela da guia receber no Gerenciador do barramento de serviço do Azure com detalhes da mensagem exibidos para uma mensagem ativa que foi recebida.":::

### <a name="peeking-a-message-from-a-subscription"></a>Espiar mensagens de uma assinatura

Para simplesmente procurar as mensagens em uma Assinatura ou na subentidade de mensagens mortas, a funcionalidade ***Espiar*** também pode ser utilizada na assinatura.

1. Clique na guia ***Peek** _ e selecione a específica _ *_assinatura_** no seletor suspenso.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Escolha entre a subentidade ***Subscription** _ ou _ *_mensagens mortas_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Clique no botão ***Espiar***.

Depois que a operação de inspeção for concluída, até 32 mensagens aparecerão na grade, como mostrado abaixo. Para exibir os detalhes de uma mensagem específica, selecione-a na grade. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Como espiar não é uma operação destrutiva, a mensagem **não** será removida da fila.
>

## <a name="next-steps"></a>Próximas etapas

   * Saiba mais sobre [Filas](service-bus-queues-topics-subscriptions.md#queues) e [Tópicos](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) de Barramento de serviço
   * Saiba mais sobre como criar [Filas do barramento de serviço por meio do portal do Azure](service-bus-quickstart-portal.md)
   * Saiba mais sobre como criar [Tópicos e Assinaturas do Barramento de serviço por meio do portal do Azure](service-bus-quickstart-topics-subscriptions-portal.md)