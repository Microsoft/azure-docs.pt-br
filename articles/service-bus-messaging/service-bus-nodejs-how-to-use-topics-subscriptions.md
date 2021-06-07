---
title: Usar o azure/service-bus JavaScript de versão prévia com tópicos e assinaturas
description: Saiba como escrever um programa JavaScript que usa a última versão prévia do pacote @azure/service-bus para enviar mensagens a um tópico do Barramento de Serviço e receber mensagens de uma assinatura para o tópico.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 59f1bb29099f2b921e687ca9de46365bc34f1b91
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537255"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Início Rápido: Tópicos e assinaturas do Barramento de Serviço do Azure com o Node.js e o pacote azure/service-bus de versão prévia
Neste tutorial, você aprenderá a usar o pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) em um programa JavaScript para enviar mensagens a um tópico do Barramento de Serviço e receber mensagens de uma assinatura do Barramento de Serviço para esse tópico.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico](service-bus-quickstart-topics-subscriptions-portal.md). Anote a cadeia de conexão, o nome do tópico e o nome de uma assinatura. Você usará apenas uma assinatura neste guia de início rápido. 

> [!NOTE]
> - Este tutorial funciona com amostras que você pode copiar e executar usando o [Node.js](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo Node.js, confira [Criar e implantar um aplicativo do Node.js em um site da Web do Azure](../app-service/quickstart-nodejs.md) ou [Serviço de Nuvem do Node.js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
Para instalar o pacote npm para o Barramento de Serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
O código de exemplo a seguir mostra como enviar um lote de mensagens para um tópico do Barramento de Serviço. Confira os comentários sobre código para obter detalhes. 

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `sendtotopic.js` e cole nele o código abaixo. Esse código enviará uma mensagem ao tópico.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. Substitua `<SERVICE BUS NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço.
1. Substitua `<TOPIC NAME>` pelo nome do tópico. 
1. Em seguida, execute o comando em um prompt de comando para executar esse arquivo.

    ```console
    node sendtotopic.js 
    ```
1. A saída a seguir será exibida.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado **receivefromsubscription.js** e cole o código a seguir nele. Confira os comentários sobre código para obter detalhes. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. Substitua `<SERVICE BUS NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace. 
1. Substitua `<TOPIC NAME>` pelo nome do tópico. 
1. Substitua `<SUBSCRIPTION NAME>` pelo nome da assinatura para o tópico. 
1. Em seguida, execute o comando em um prompt de comando para executar esse arquivo.

    ```console
    node receivefromsubscription.js
    ```
1. A saída a seguir será exibida.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

No portal do Azure, acesse o namespace do Barramento de Serviço e selecione o tópico no painel inferior para ver a página **Tópico do Barramento de Serviço** do tópico. Nessa página, você verá três mensagens de entrada e três mensagens de saída no gráfico **Mensagens**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída":::

Se você só executar o aplicativo de envio na próxima vez, na página **Tópico do Barramento de Serviço**, verá seis mensagens de entrada (três novas), mas três mensagens de saída. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Página do tópico atualizada":::

Nessa página, se você selecionar uma assinatura, chegará à página **Assinatura do Barramento de Serviço**. Você poderá ver a contagem de mensagens ativas, a contagem de mensagens mortas, entre outros nessa página. Neste exemplo, há três mensagens ativas que ainda não foram recebidas por um destinatário. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas":::

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação: 

- [Biblioteca de clientes do Barramento de Serviço do Azure para Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). A pasta **javascript** traz exemplos de JavaScript, e a pasta **typescript** contém exemplos de TypeScript. 
- [Documentação de referência de azure-servicebus](/javascript/api/overview/azure/service-bus)
