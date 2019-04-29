---
title: Como usar os tópicos do Barramento de Serviço do Azure com o Python | Microsoft Docs
description: Saiba como usar tópicos do Barramento de Serviço do Azure e assinaturas do Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 102fe85916194648501be3d2cb39d8bcda9e9f5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811085"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Como usar tópicos e assinaturas do Barramento de Serviço com Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar tópicos e assinaturas do Barramento de Serviço. Os exemplos são escritos em Python e usam o [pacote SDK para Azure Python][Azure Python package]. Os cenários abordados incluem:

- Criar tópicos e assinaturas 
- Criar filtros de assinatura 
- Enviar mensagens para um tópico 
- Receber mensagens de uma assinatura
- Excluir tópicos e assinaturas

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar sua [benefícios de assinante do MSDN ou Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [guia de início rápido: Use o portal do Azure para criar um tópico do barramento de serviço e assinaturas do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um barramento de serviço **namespace** e obtenha o **cadeia de caracteres de conexão**.

    > [!NOTE]
    > Você aprenderá a criar uma **tópico** e uma **assinatura** para o tópico usando **Python** neste início rápido. 
3. Instale [pacote do Python Azure][Azure Python package]. Consulte a [guia de instalação do Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o seguinte código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço de forma programática:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código a seguir cria um objeto **ServiceBusService**. Substitua `mynamespace`, `sharedaccesskeyname` e `sharedaccesskey` pelo namespace real e pelo nome e valor da chave de SAS (Assinatura de Acesso Compartilhado).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Você pode obter os valores de nome e valor da chave da SAS no [Portal do Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

O método `create_topic` também dá suporte a opções adicionais, que permitem substituir configurações padrão do tópico, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir define o tamanho máximo do tópico como 5 GB e uma TTL (vida útil) de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

As assinaturas de tópicos também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [!NOTE]
> As assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão inscritas sejam excluídos.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **MatchAll** (padrão) será utilizado. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada `AllMessages` e usa o filtro padrão **MatchAll**.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros

Você também pode definir filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é um **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre as expressões que podem ser usadas com um filtro SQL, confira a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

É possível adicionar filtros a uma assinatura usando o método **create\_rule** do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

> [!NOTE]
> Como o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão, senão o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método `delete_rule` do objeto **ServiceBusService**.
> 
> 

O exemplo a seguir cria uma assinatura denominada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade personalizada `messagenumber` maior que 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Da mesma forma, o seguinte exemplo cria uma assinatura denominada `LowMessages` com um **SqlFilter** que seleciona apenas mensagens que têm uma propriedade `messagenumber` menor ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando uma mensagem for enviada para `mytopic`, ela sempre será fornecida aos destinatários inscritos na assinatura do tópico **AllMessages** e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo deve usar o método `send_topic_message` do objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `mytopic`. A propriedade `messagenumber` de cada mensagem varia de acordo com a iteração do loop (isso determina quais assinaturas a receberão):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB. Para saber mais sobre cotas, confira [Service Bus quotas][Service Bus quotas] (Cotas do Barramento de Serviço).

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o método `receive_subscription_message` no objeto **ServiceBusService**:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são excluídas da assinatura conforme são lidas quando o parâmetro `peek_lock` é definido como **False**. Você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila ao definir o parâmetro `peek_lock` como **True**.

O comportamento de leitura e exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem se houver uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Como o Barramento de Serviço marcou a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `peek_lock` estiver definido como **True**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para o processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método `delete` no objeto **Message**. O método `delete` marcará a mensagem como sendo consumida e a removerá da assinatura.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não conseguir processar a mensagem por algum motivo, ele chamará o método `unlock` no objeto **Message**. Esse método faz com que o Barramento de Serviço desbloqueie a mensagem na assinatura e a disponibilize para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `delete` seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Esse comportamento é chamado frequentemente de Processamento de pelo menos uma vez\*; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Para fazer isso, você pode usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do [Portal do Azure][Azure portal] ou de forma programática. O seguinte exemplo mostra como excluir o tópico chamado `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O seguinte código mostra como excluir uma assinatura chamada `HighMessages` do tópico `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* Confira [Filas, tópicos e assinaturas][Queues, topics, and subscriptions].
* Referência para [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
