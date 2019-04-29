---
title: Como usar as filas de Barramento de Serviço no Node.js | Microsoft Docs
description: Aprenda a usar as filas do Barramento de Serviço no Azure a partir de um aplicativo Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6159609f894f967e8ee372a0ee316eb900537aba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590002"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Como usar filas do Barramento de Serviço com Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá como criar aplicativos Node. js para enviar e receber mensagens de uma fila do barramento de serviço. As amostras são escritas em JavaScript e usam o módulo Node.js do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar sua [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se você não tiver uma fila para trabalhar com, siga as etapas na [portal do Azure de uso para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) artigo para criar uma fila.
    1. Leia o quick **visão geral** do barramento de serviço **filas**. 
    2. Criar um barramento de serviço **namespace**. 
    3. Obter o **cadeia de caracteres de conexão**. 

        > [!NOTE]
        > Você aprenderá a criar uma **fila** no namespace do barramento de serviço usando o Node. js neste tutorial. 
 

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, confira [Criar e implantar um aplicativo do Node.js em um site da Web do Azure][Create and deploy a Node.js application to an Azure Website] ou [Serviço de Nuvem do Node.js][Node.js Cloud Service] usando o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar o Barramento de Serviço do Azure, baixe e use o pacote do Azure Node.js. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote
1. Use a janela de comando **Windows PowerShell para Node.js** para navegar até a pasta **c:\\node\\sbqueues\\WebRole1** na qual você criou o aplicativo de exemplo.
2. Digite **npm install azure** na janela Comando, o que deve resultar em uma saída semelhante à seguinte:

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
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure**, que contém as bibliotecas necessárias para acessar as filas do Barramento de Serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao início do arquivo **server.js** do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma conexão do barramento de serviço do Azure
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter as informações necessárias para se conectar ao Barramento de Serviço. Se essa variável de ambiente não estiver definida, você deverá especificar as informações da conta chamando `createServiceBusService`.

Para ver um exemplo de como definir variáveis de ambiente no [Portal do Azure][Azure portal] para um Site do Azure, veja [Aplicativo Web do Node.js com Armazenamento][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusService** permite que você trabalhe com filas de barramento de serviço. O código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo ao início do arquivo **server.js** , após a instrução de importação do módulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao chamar `createQueueIfNotExists` no objeto **ServiceBusService**, a fila especificada (se houver) é retornada ou uma nova fila com o nome especificado é criada. O código a seguir usa `createQueueIfNotExists` para criar ou conectar-se à fila denominada `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O método `createServiceBusService` também dá suporte para opções adicionais, que permitem a substituição de configurações padrão da fila, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e a vida útil (TTL) como um minuto:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtros
É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar `next`, passando um retorno de chamada com a assinatura a seguir:

```javascript
function (returnObject, finalCallback, next)
```

Esse retorno de chamada e após processar o `returnObject` (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar `next` se ele existir, para continuar processando outros filtros ou invocar `finalCallback`, terminar a invocação de serviço .

Dois filtros que implementam a lógica de repetição são incluídos com o Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O código a seguir cria um objeto `ServiceBusService` que usa o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Para enviar uma mensagem para uma fila do Barramento de Serviço, seu aplicativo chamará o método `sendQueueMessage` no objeto **ServiceBusService**. Mensagens enviadas para (e recebidas de) as filas do Barramento de Serviço são objetos **BrokeredMessage** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando uma cadeia de caracteres como a mensagem. As propriedades padrão necessárias são preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada `myqueue` usando `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Para saber mais sobre cotas, confira [Service Bus quotas][Service Bus quotas] (Cotas do Barramento de Serviço).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `receiveQueueMessage` no objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da fila à medida que são lidas; no entanto, você pode ler (pico) e bloquear a mensagem sem excluí-la da fila configurando o parâmetro opcional `isPeekLock` como **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `isPeekLock` estiver definido como **true**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método `deleteMessage` e fornecendo a mensagem a ser excluída como um parâmetro. O método `deleteMessage` marcará a mensagem como sendo consumida e a removerá da assinatura.

O exemplo a seguir demonstra como receber e processar mensagens usando `receiveQueueMessage`. Primeiro, o exemplo recebe e exclui uma mensagem, em seguida recebe a mensagem usando `isPeekLock` definido como **true** e, então, exclui a mensagem usando `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método `unlockMessage` no objeto **ServiceBusService**. Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `deleteMessage` seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado de *Processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre filas, veja os seguintes recursos.

* [Filas, tópicos e assinaturas][Queues, topics, and subscriptions]
* Repositório do [SDK do Azure para Node][Azure SDK for Node] no GitHub
* [Centro de desenvolvedores do Node. js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
