---
title: Como usar tópicos e assinaturas do Barramento de Serviço do Azure com o Node.js | Microsoft Docs
description: Aprenda a usar assinaturas e tópicos do Barramento de Serviço no Azure por meio de um aplicativo Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 3dbec81237edd7cbf51e4812e83da068b9a366e0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541003"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Como os tópicos do barramento de serviço de uso e as assinaturas com Node. js e o pacote azure-sb
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote de Node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Neste tutorial, você aprenderá a criar aplicativos Node. js para enviar mensagens para um tópico do barramento de serviço e receber mensagens de uma assinatura do barramento de serviço usando o [azure-sb](https://www.npmjs.com/package/azure-sb) pacote. Os exemplos são escritos em JavaScript e usar o Node. js [módulo do Azure](https://www.npmjs.com/package/azure) que usa internamente o `azure-sb` pacote.

O [azure-sb](https://www.npmjs.com/package/azure-sb) usos do pacote [APIs de tempo de execução do REST do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando a nova [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacote que usa o mais rápido [protocolo AMQP 1.0](service-bus-amqp-overview.md). Para saber mais sobre o novo pacote, consulte [como usar tópicos do barramento de serviço e assinaturas com Node. js e @azure/service-bus pacote](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), caso contrário, continue lendo para saber como usar os [azure](https://www.npmjs.com/package/azure) pacote.

Os cenários abordados aqui incluem:

- Criar tópicos e assinaturas 
- Criar filtros de assinatura 
- Enviar mensagens para um tópico 
- Receber mensagens de uma assinatura
- Excluir tópicos e assinaturas 

Para saber mais sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar sua [benefícios de assinante do MSDN ou Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [guia de início rápido: Use o portal do Azure para criar um tópico do barramento de serviço e assinaturas do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um barramento de serviço **namespace** e obtenha o **cadeia de caracteres de conexão**.

    > [!NOTE]
    > Você aprenderá a criar uma **tópico** e uma **assinatura** para o tópico usando **Node. js** neste início rápido. 

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site da Web do Azure], [Serviço de Nuvem do Node.js][Node.js Cloud Service] usando o Windows PowerShell ou Site com o WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar o Barramento de Serviço, baixe o pacote do Node.js do Azure. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote
1. Abra uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Navegue até a pasta onde você criou o aplicativo de exemplo.
3. Digite **npm install azure** na janela de comando, que deve resultar na seguinte saída:

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure**, que contém as bibliotecas necessárias para acessar os tópicos do Barramento de Serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao início do arquivo **server.js** do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do Barramento de Serviço
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para a cadeia de conexão que você obteve na etapa anterior, "Obter as credenciais." Se essa variável de ambiente não estiver definida, você deverá especificar as informações da conta chamando `createServiceBusService`.

Para ver um exemplo de como definir as variáveis de ambiente para um Serviço de Nuvem do Azure, confira [Definir variáveis de ambiente](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Criar um tópico
O objeto **ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo ao início do arquivo **server.js** , após a instrução de importação do módulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Se você chamar `createTopicIfNotExists` no objeto **ServiceBusService**, o tópico especificado será retornado (se existir) ou um novo tópico com o nome especificado será criado. O código a seguir usa `createTopicIfNotExists` para criar ou conectar-se ao tópico denominado `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

O método `createTopicIfNotExists` também dá suporte a opções adicionais, que permitem substituir configurações padrão do tópico, como a vida útil da mensagem ou o tamanho máximo do tópico. 

O exemplo a seguir define o tamanho máximo do tópico para 5 GB com um tempo de vida de um minuto:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros
É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após executar o pré-processamento nas opções de solicitação, o método chama `next` e passa para o retorno de chamada com a assinatura a seguir:

```javascript
function (returnObject, finalCallback, next)
```

Neste retorno de chamada, e após processar `returnObject` (a resposta da solicitação ao servidor), o retorno de chamada deverá invocar next (se existir) para continuar processando outros filtros ou chamar `finalCallback` para finalizar a chamada de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa o **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Criar assinaturas
As assinaturas do tópico também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [!NOTE]
> Por padrão, as assinaturas são persistentes até que elas ou o tópico ao qual estão associadas sejam excluídos. Se o aplicativo contiver lógica para criar uma assinatura, ele deverá primeiro verificar se a assinatura existe usando o método `getSubscription`.
>
> Você pode ter as assinaturas são excluídas automaticamente definindo a [AutoDeleteOnIdle propriedade](https://docs.microsoft.com/javascript/api/azure-arm-sb/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
O filtro **MatchAll** é o filtro padrão usado quando uma assinatura é criada. Quando você usa o filtro **MatchAll**, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada AllMessages e usa o filtro padrão **MatchAll**.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros
Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Os filtros podem ser adicionados a uma assinatura usando o método `createRule` do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

> [!NOTE]
> Como o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão, senão o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método `deleteRule` do objeto **ServiceBusService**.
>
>

O exemplo a seguir cria uma assinatura denominada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade personalizada `messagenumber` maior que 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Da mesma forma, o seguinte exemplo cria uma assinatura denominada `LowMessages` com um **SqlFilter** que seleciona apenas mensagens que têm uma propriedade `messagenumber` menor ou igual a 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando uma mensagem é enviada para `MyTopic`, ela é entregue aos destinatários inscritos na assinatura do tópico `AllMessages` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do tópico `HighMessages` e `LowMessages` (dependendo do conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens a um tópico
Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo deve usar o método `sendTopicMessage` do objeto **ServiceBusService**.
As mensagens enviadas aos tópicos do Barramento de Serviço são objetos **BrokeredMessage**.
Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como `Label` e `TimeToLive`), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo dos dados da cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem, passando um valor de cadeia de caracteres para `sendTopicMessage` e todas as propriedades padrão necessárias são preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `MyTopic`. O valor da propriedade `messagenumber` de cada mensagem varia de acordo com a iteração do loop (essa propriedade determina quais assinaturas a receberão):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite no número de mensagens mantidas em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
As mensagens são recebidas de uma assinatura usando o método `receiveSubscriptionMessage` no objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da assinatura conforme são lidas. No entanto, é possível definir o parâmetro opcional `isPeekLock` para **true** para ler (espiar) e bloquear a mensagem sem excluí-la da assinatura.

O comportamento padrão de leitura e exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Como o Barramento de Serviço marcou a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `isPeekLock` estiver definido como **true**, o recebimento se torna uma operação de dois estágios, o que possibilita dar suporte para aplicativos que não podem tolerar mensagens perdidas. Quando o Barramento de Serviço recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e retorna-a ao aplicativo.
Após o aplicativo processar a mensagem (ou armazená-la de maneira confiável para processamento futuro), ele conclui o segundo estágio do processo chamando o método **deleteMessage** e passa a mensagem para excluir como um parâmetro. O método **deleteMessage** marca a mensagem como consumida e a remove da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo `receiveSubscriptionMessage` padrão. O primeiro exemplo recebe e exclui uma mensagem da assinatura “LowMessages” e recebe uma mensagem da assinatura “HighMessages” usando `isPeekLock` definido como true. Em seguida, ele exclui a mensagem usando `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método `unlockMessage` no objeto **ServiceBusService**. Esse método faz com que o Barramento de Serviço desbloqueie a mensagem dentro da assinatura e a disponibilize para ser recebida novamente. Neste caso, pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Há também um tempo limite associado a uma mensagem bloqueada na assinatura. Se o aplicativo não conseguir processar a mensagem antes que o tempo limite de bloqueio expire (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `deleteMessage` seja chamado, a mensagem será reenviada ao aplicativo quando for reiniciado. Esse comportamento geralmente é chamado de *Processamento Pelo menos uma Vez*. Ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações, a mesma mensagem poderá ser reenviada. Se o cenário não puder tolerar o processamento duplicado, será necessário adicionar lógica ao aplicativo para tratar a entrega de mensagens duplicadas. É possível usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
Tópicos e assinaturas são persistentes, a menos que o [autoDeleteOnIdle propriedade](https://docs.microsoft.com/javascript/api/azure-arm-sb/sbsubscription?view=azure-node-latest#autodeleteonidle) é definido e devem ser explicitamente excluídos por meio de [portal do Azure][Azure portal] ou programaticamente.
O exemplo a seguir demonstra como excluir o tópico denominado `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura chamada `HighMessages` do tópico `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* Confira [Filas, tópicos e assinaturas][Queues, topics, and subscriptions].
* Referência da API para [SqlFilter][SqlFilter].
* Visite o repositório [SDK do Azure para o nó][Azure SDK for Node] no GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Criar e implantar um aplicativo Node.js em um site da Web do Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

