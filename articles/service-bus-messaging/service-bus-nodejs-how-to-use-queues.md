---
title: Como usar filas do azure/service-bus em JavaScript
description: Saiba como escrever um programa JavaScript que usa a última versão prévia do pacote @azure/service-bus para enviar e receber mensagens em uma fila do Barramento de Serviço.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537226"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Enviar e receber mensagens em filas do Barramento de Serviço do Azure (JavaScript)
Neste tutorial, você aprenderá a usar o pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) em um programa JavaScript para enviar e receber mensagens em uma fila do Barramento de Serviço.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a **cadeia de conexão** do namespace do Barramento de Serviço e o nome da **fila** que você criou.

> [!NOTE]
> - Este tutorial funciona com amostras que você pode copiar e executar usando o [Node.js](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo Node.js, confira [Criar e implantar um aplicativo do Node.js em um site da Web do Azure](../app-service/quickstart-nodejs.md) ou [Serviço de Nuvem do Node.js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
Para instalar o pacote npm para o Barramento de Serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
O código de exemplo a seguir mostra como enviar uma mensagem para uma fila.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/).
2. Crie um arquivo chamado `send.js` e cole nele o código abaixo. Esse código enviará uma mensagem à sua fila. A mensagem tem um rótulo (Cientista) e um corpo (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
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
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
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
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
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
3. Substitua `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` pela cadeia de conexão para o namespace do Barramento de Serviço.
1. Substitua `<QUEUE NAME>` pelo nome da fila. 
1. Em seguida, execute o comando em um prompt de comando para executar esse arquivo.

    ```console
    node send.js 
    ```
1. A saída a seguir será exibida.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `receive.js` e cole o código a seguir nele.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
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
3. Substitua `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` pela cadeia de conexão para o namespace do Barramento de Serviço.
1. Substitua `<QUEUE NAME>` pelo nome da fila. 
1. Em seguida, execute o comando em um prompt de comando para executar esse arquivo.

    ```console
    node receive.js
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

Na página **Visão geral** do namespace do Barramento de Serviço no portal do Azure, você poderá ver a contagem de mensagens de **entrada** e **saída**. Talvez seja necessário aguardar alguns minutos e atualizar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída":::

Selecione a fila nesta página **Visão geral** para acessar a página **Fila do Barramento de Serviço**. Você também verá a contagem de mensagens de **entrada** e **saída** nessa página. Além disso, verá outras informações, como o **tamanho atual** da fila, o **tamanho máximo**, a **contagem de mensagens ativas** etc. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila":::
## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação: 

- [Biblioteca de clientes do Barramento de Serviço do Azure para JavaScript](https://www.npmjs.com/package/@azure/service-bus)
- [Exemplos de JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemplos de TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Documentação de referência da API](/javascript/api/overview/azure/service-bus)
