---
title: Use o Python para enviar e receber mensagens - Hubs de Eventos do Azure | Microsoft Docs
description: Saiba como enviar eventos, receber eventos e capturar eventos de streaming por meio do Hubs de Eventos usando o Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: f86dc92b4df45119930970acfe9e173f32f894fb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356044"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Como usar Hubs de Eventos de um aplicativo Python
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter mais informações, consulte [Introdução aos Hubs de Eventos do Azure](event-hubs-what-is-event-hubs.md). 

Este artigo fornece links para artigos que mostram como realizar as seguintes tarefas de um aplicativo escrito em **Python**:

- [Enviar eventos para um Hub de Eventos](#send-events-to-event-hubs)
- [Receber eventos do hub de eventos](#receive-events-from-event-hubs)
- A leitura capturou dados de evento de um armazenamento do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie um hub de eventos, seguindo um destes inícios rápidos: [Portal do Azure](event-hubs-create.md), [CLI do Azure](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 ou posterior instalado em seu computador.

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote do Python para Hubs de Eventos, abra um prompt de comando que tenha Python no caminho e, em seguida, execute este comando: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Enviar eventos para os Hubs de Eventos
O código a seguir mostra como enviar eventos para um hub de eventos de um aplicativo Python: 

1. Crie variáveis para armazenar o URL do hub de eventos, o nome da chave e o valor da chave. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Criar um cliente de Hubs de Eventos, adicionar um remetente, executar o cliente enviar o evento usando o remetente e, em seguida, parar o cliente quando você terminar. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Para obter um tutorial completo sobre como enviar eventos para um hub de eventos a partir de um aplicativo gravado em Python, consulte [este artigo](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Receber eventos do Hubs de Eventos
O código a seguir mostra como enviar eventos de um hub de eventos de um aplicativo Python: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Para obter um tutorial completo sobre como receber eventos de um hub de eventos a partir de um aplicativo gravado em Python, consulte [este artigo](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>A leitura capturou dados de evento de um Armazenamento do Microsoft Azure
O código a seguir mostra como ler dados de eventos capturados que são armazenados em um **armazenamento de Blob do Azure** de um aplicativo Python: Habilite o recurso **Capturar** para o hub de eventos, seguindo as instruções de: [Habilitar a Captura de Hubs de Evento usando o portal do Azure](event-hubs-capture-enable-through-portal.md). Em seguida, envie alguns eventos ao hub de eventos antes de testar o código. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

Para obter um tutorial completo sobre como ler os dados do Hubs de Eventos em um armazenamento de blobs do Azure a partir de um aplicativo gravado em Python, consulte [este artigo](event-hubs-capture-python.md)

## <a name="github-samples"></a>Exemplos do GitHub
Você pode encontrar exemplos de Python no [repositório azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Próximas etapas
Leia os artigos na seção Conceitos a partir [da visão geral dos recursos de Hubs de Eventos](event-hubs-features.md).
