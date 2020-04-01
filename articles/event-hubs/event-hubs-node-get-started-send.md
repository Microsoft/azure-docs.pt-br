---
title: Enviar ou receber eventos de Hubs de Eventos do Azure usando o JavaScript (herdado)
description: Este artigo fornece um passo a passo para criar um aplicativo JavaScript que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure/event-hubs versão 2 antigo.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162609"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Início Rápido: Enviar ou receber eventos de Hubs de Eventos do Azure usando o JavaScript (@azure/event-hubs versão 2)
Este início rápido mostra como criar aplicativos JavaScript para enviar e receber eventos de um hub de eventos usando o pacote JavaScript azure-eventhub versão 2. 

> [!WARNING]
> Este início rápido usa o pacote antigo do azure/event-hubs versão 2. Para um início rápido que usa a **versão 5** mais recente do pacote, confira [Enviar e receber eventos usando o azure/eventhubs versão 5](get-started-node-send-v2.md). Para mudar seu aplicativo do pacote antigo para o novo, confira o [Guia para migrar do azure/eventhubs versão 1 para versão 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Pré-requisitos

Se você for novo nos Hubs de Eventos do Azure, confira a [Visão geral dos Hubs de Eventos](event-hubs-about.md) antes de prosseguir com este início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Assinatura do Microsoft Azure**. Para usar os serviços do Azure, incluindo os Hubs de Eventos do Azure, você precisa ter uma assinatura.  Caso não tenha uma conta existente do Azure, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/free/) ou use os benefícios do assinante do MSDN quando [criar uma conta](https://azure.microsoft.com).
- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial. Em seguida, obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.


### <a name="install-npm-package"></a>Instalar o pacote do npm
Para instalar o [pacote npm para Hubs de Eventos](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando

```shell
npm install @azure/event-hubs@2
```

Para instalar o [pacote npm para o Host do Processador de Eventos](https://www.npmjs.com/package/@azure/event-processor-host), execute o comando abaixo em si

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Enviar eventos

Esta seção mostra como criar um aplicativo JavaScript que envia eventos a um hub de eventos. 

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `send.js` e cole nele o código abaixo. Obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome do seu Hub de Eventos no código acima
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo. Isso enviará 100 eventos a seu Hub de Eventos

Parabéns! Agora você enviou eventos a um hub de eventos.


## <a name="receive-events"></a>Receber eventos

Esta seção mostra como criar um aplicativo JavaScript que recebe eventos de uma única partição do grupo de consumidores em um hub de eventos. 

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `receive.js` e cole nele o código abaixo.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome do seu Hub de Eventos no código acima.
4. Em seguida, execute o comando `node receive.js` em um prompt de comando para executar esse arquivo. Isso receberá eventos de uma das partições do grupo de consumidores padrão em seu Hub de Eventos

Parabéns! Agora você recebeu eventos do hub de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos usando o Host de Processador de Eventos

Esta seção mostra como receber eventos de um hub de eventos usando o [EventProcessorHost](event-hubs-event-processor-host.md) do Azure em um aplicativo JavaScript. O EPH (EventProcessorHost) ajuda você a receber eventos de um hub de eventos com eficiência criando receptores em todas as partições no grupo de consumidores de um hub de eventos. Ele usa um ponto de verificação nos metadados das mensagens recebidas em intervalos regulares em um Azure Storage Blob. Com essa abordagem, fica fácil continuar a receber mensagens mais tarde exatamente no ponto em que você parou.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `receiveAll.js` e cole nele o código abaixo.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Insira a cadeia de conexão e o nome do seu hub de eventos no código acima junto com a cadeia de conexão para um Armazenamento de Blobs do Azure
4. Em seguida, execute o comando `node receiveAll.js` em um prompt de comando para executar esse arquivo.

Parabéns! Agora você recebeu eventos do hub de eventos usando o Host do Processador de Eventos. Isso receberá eventos de todas as partições do grupo de consumidores padrão em seu Hub de Eventos

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
- Confira outras amostras do JavaScript para [Hubs de Eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e [Host do Processador de Eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no GitHub
