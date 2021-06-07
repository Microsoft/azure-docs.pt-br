---
title: Enviar ou receber eventos de Hubs de Eventos do Azure usando o JavaScript (mais recente)
description: Este artigo fornece um passo a passo para criar um aplicativo JavaScript que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure/event-hubs mais recente.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: c418c13346fb1ec8ba16965fa1020c676ddf3ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602546"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Enviar eventos para Hubs de Eventos ou receber eventos deles usando o JavaScript (azure/event-hubs)
Este guia de início rápido mostra como enviar e receber eventos de um hub de eventos usando o pacote JavaScript **azure/event-hubs**. 


## <a name="prerequisites"></a>Pré-requisitos
Se você for novo nos Hubs de Eventos do Azure, confira a [Visão geral dos Hubs de Eventos](event-hubs-about.md) antes de prosseguir com este início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Assinatura do Microsoft Azure**. Para usar os serviços do Azure, incluindo os Hubs de Eventos do Azure, você precisa ter uma assinatura.  Caso não tenha uma conta existente do Azure, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/free/) ou use os benefícios do assinante do MSDN quando [criar uma conta](https://azure.microsoft.com).
- Node.js versão 8.x ou posterior. Baixe a [versão LTS (suporte de longo prazo)](https://nodejs.org) mais recente.  
- Visual Studio Code (recomendado) ou qualquer outro IDE (ambiente de desenvolvimento integrado).  
- Um namespace dos Hubs de Eventos e um hub de eventos ativos. Para criá-los, execute as seguintes etapas: 

   1. No [portal do Azure](https://portal.azure.com), crie um namespace do tipo *Hubs de Eventos* e obtenha as credenciais de gerenciamento de que seu aplicativo precisa para se comunicar com o hub de eventos. 
   1. Para criar o namespace e o hub de eventos, siga as instruções em [Início Rápido: Criar um hub de eventos usando o portal do Azure](event-hubs-create.md).
   1. Continue seguindo as instruções neste início rápido. 
   1. Para obter a cadeia de conexão para seu namespace do Hub de Eventos, siga as instruções em [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Registre a cadeia de conexão para usar posteriormente neste início rápido.
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace dos Hubs de Eventos** seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no início rápido.

### <a name="install-the-npm-package"></a>instalar o pacote npm
Para instalar o [pacote npm (Gerenciador de Pacotes do Nó) para Hubs de Eventos](https://www.npmjs.com/package/@azure/event-hubs), abra um prompt de comando que tenha *npm* no caminho dele, altere o diretório para a pasta em que você deseja manter seus exemplos e, em seguida, execute este comando:

```shell
npm install @azure/event-hubs
```

Para o lado receptor, você precisará instalar mais dois pacotes. Neste início rápido, você usará o Armazenamento de Blobs do Azure para persistir pontos de verificação para que o programa não leia os eventos que ele já leu. Ele executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares em um blob. Essa abordagem facilita continuar a receber mensagens mais tarde exatamente do ponto em que você parou.

Execute os seguintes comandos:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Nesta seção, você criará um aplicativo JavaScript que envia eventos a um hub de eventos.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com).
1. Crie um arquivo chamado *send.js* e cole o seguinte código nele:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. No código, use valores reais para substituir o seguinte:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Execute `node send.js` para realizar este arquivo. Esse comando envia um lote de três eventos para seu hub de eventos.
1. No portal do Azure, verifique se o hub de eventos recebeu as mensagens. Na seção **Métricas**, alterne para a exibição **Mensagens**. Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ela mostre que as mensagens foram recebidas.

    [![Verificar se o hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código-fonte completo, incluindo comentários informativos adicionais, acesse a [página sendEvents do GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Parabéns! Agora você enviou eventos a um hub de eventos.


## <a name="receive-events"></a>Receber eventos
Nesta seção, você receberá eventos de um hub de eventos usando um repositório de pontos de verificação do Armazenamento de Blobs do Azure em um aplicativo JavaScript. Ele executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares em um Azure Storage Blob. Essa abordagem facilita continuar a receber mensagens mais tarde exatamente do ponto em que você parou.

> [!WARNING]
> Se você executar esse código no Azure Stack Hub, haverá erros de runtime, a menos que você direcione uma versão específica da API de Armazenamento. Isso ocorre porque o SDK dos Hubs de Eventos usa a API do Armazenamento do Microsoft Azure mais recente disponível no Azure, que pode não estar disponível em sua plataforma do Azure Stack Hub. O Azure Stack Hub pode dar suporte a uma versão diferente do SDK do Armazenamento de Blobs do que aquelas normalmente disponíveis no Azure. Se estiver usando o Armazenamento de Blobs do Azure como um armazenamento de ponto de verificação, verifique a [versão da API do Armazenamento do Azure com suporte de seu build do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e tenha como destino essa versão em seu código. 
>
> Por exemplo, se a execução estiver sendo feita no Azure Stack Hub versão 2005, a versão mais alta disponível para o serviço de Armazenamento será a versão 2019-02-02. Por padrão, a biblioteca de clientes do SDK dos Hubs de Eventos usa a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Nesse caso, além de seguir as etapas desta seção, você também precisará adicionar o código para ter como destino a versão de API 2019-02-02 do serviço de Armazenamento. Para obter um exemplo de como ter como destino uma versão de API específica do Armazenamento, confira as amostras [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) no GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Criar uma conta de armazenamento e um contêiner de blobs do Azure
Para criar uma conta de armazenamento do Azure e um contêiner de blobs nela, execute as seguintes ações:

1. [Crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Crie um contêiner de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Registre a cadeia de conexão e o nome do contêiner para usá-los posteriormente no código de recebimento.

### <a name="write-code-to-receive-events"></a>Gravar código para receber eventos

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com).
1. Crie um arquivo chamado *receive.js* e cole o seguinte código nele:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. No código, use valores reais para substituir os seguintes valores:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Execute `node receive.js` em um prompt de comando para executar esse arquivo. A janela deve exibir mensagens sobre os eventos recebidos.

    > [!NOTE]
    > Para ver o código-fonte completo, incluindo comentários informativos adicionais, acesse a [página receiveEventsUsingCheckpointStore.js do GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Parabéns! Agora você recebeu eventos do seu hub de eventos. O programa receptor receberá eventos de todas as partições do grupo de consumidores padrão no hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Confira estes exemplos no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Exemplos de TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
