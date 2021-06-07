---
title: Ler dados capturados dos Hubs de Eventos do Azure de um aplicativo Python (mais recente)
description: Este artigo mostra como escrever o código Python para capturar dados enviados para um hub de eventos e ler os dados de evento capturados de uma conta de armazenamento do Azure.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883190"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Capturar dados dos Hubs de Eventos no Armazenamento do Azure e lê-los usando o Python (azure-eventhub)

Você pode configurar um hub de eventos para que os dados enviados para um hub de eventos sejam capturados em uma conta de armazenamento do Azure ou no Azure Data Lake Storage Gen 1 ou Gen 2. Este artigo mostra como escrever o código Python para enviar eventos para um hub de eventos e ler os dados capturados do **Armazenamento de Blobs do Azure**. Para obter mais informações sobre esse recurso, confira [visão geral do recurso Captura dos Hubs de Eventos](event-hubs-capture-overview.md).

Este início rápido usa o [SDK para Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso Captura. O aplicativo *sender.py* envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O hub de eventos está configurado para usar o recurso de Captura a fim de gravar esses dados no armazenamento de Blobs em lotes. O aplicativo *capturereader.py* lê esses blobs e cria um arquivo de acréscimo para cada dispositivo. O aplicativo grava os dados em arquivos CSV.

Neste início rápido, você: 

> [!div class="checklist"]
> * Criar uma conta de armazenamento de Blobs do Azure e um contêiner no portal do Azure.
> * Criar um namespace dos Hubs de Eventos usando o portal do Azure.
> * Crie um hub de eventos com o recurso Captura habilitado e conecte-o à sua conta de armazenamento.
> * Enviar dados para o hub de eventos usando um script Python.
> * Ler e processar arquivos da Captura dos Hubs de Eventos usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python com PIP e os pacotes a seguir instalados. O código deste artigo foi testado em relação a essas versões. 
    - Python 3.7
    - azure-eventhub 5.2.0
    - azure-storage-blob 12.6.0
    - avro-python3 1.10.1
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.  
- Um namespace dos Hubs de Eventos e um hub de eventos ativos.
[Criar um namespace dos Hubs de Eventos e um hub de eventos no namespace](event-hubs-create.md). Registre o nome do namespace dos Hubs de Eventos, o nome do hub de eventos e a chave de acesso primária do namespace. Para obter a chave de acesso, confira [Obter uma cadeia de conexão dos Hubs de Eventos](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave padrão é *RootManageSharedAccessKey*. Para este início rápido, você só precisará da chave primária. Não será necessária a cadeia de conexão.  
- Uma conta de armazenamento do Azure, um contêiner de blobs na conta de armazenamento e uma cadeia de conexão para a conta de armazenamento. Se você não tiver esses itens, faça o seguinte:  
    1. [Crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Crie um contêiner de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Registre a cadeia de conexão e o nome do contêiner para usá-los posteriormente neste início rápido.  
- Habilite o recurso Captura para o hub de eventos. Para fazer isso, siga as instruções em [Habilitar a Captura dos Hubs de Eventos usando o portal do Azure](event-hubs-capture-enable-through-portal.md). Selecione a conta de armazenamento e o contêiner de blobs criados na etapa anterior. Você também poderá habilitar o recurso quando criar o hub de eventos.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script Python para enviar eventos a seu Hub de Eventos
Nesta seção, você criará um script Python que envia 200 eventos (10 dispositivos * 20 eventos) para um hub de eventos. Esses eventos são um exemplo de leitura ambiental enviado no formato JSON. 

1. Abra seu editor favorito do Python, como o [Visual Studio Code][Visual Studio Code].
2. Crie um script chamado *sender.py*. 
3. Cole o seguinte código em *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Substitua os seguintes valores nos scripts:  
    * Substitua `EVENT HUBS NAMESPACE CONNECTION STRING` pela cadeia de conexão do seu namespace dos Hubs de Eventos.  
    * Substitua `EVENT HUB NAME` pelo nome do seu hub de eventos.  
5. Execute o script para enviar eventos ao hub de eventos.  
6. No portal do Azure, você poderá verificar se o hub de eventos recebeu as mensagens. Alterne para a exibição **Mensagens** na seção **Métricas**. Atualize a página para atualizar o gráfico. Poderá levar alguns segundos para que a página exiba que as mensagens foram recebidas. 

    [![Verificar se o hub de eventos recebeu as mensagens](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Criar um script Python para ler os arquivos da Captura
Neste exemplo, os dados capturados são armazenados no Armazenamento de Blobs do Azure. O script nesta seção lê os arquivos de dados capturados da sua conta de armazenamento do Azure e gera arquivos CSV para você abrir e exibir facilmente. Você verá 10 arquivos no diretório de trabalho atual do aplicativo. Esses arquivos conterão as leituras ambientais dos 10 dispositivos. 

1. No editor do Python, crie um script chamado *capturereader.py*. Esse script lê os arquivos capturados e cria um arquivo para cada dispositivo gravar os dados somente desse dispositivo.
2. Cole o seguinte código em *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Substitua `AZURE STORAGE CONNECTION STRING` pela cadeia de conexão da conta de armazenamento do Azure. O nome do contêiner criado neste início rápido é *captura*. Caso tenha usado um nome diferente para o contêiner, substitua *captura* pelo nome do contêiner na conta de armazenamento. 

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra um prompt de comando com o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > O código deste artigo foi testado em relação a essas versões. 
    > - Python 3.7
    > - azure-eventhub 5.2.0
    > - azure-storage-blob 12.6.0
    > - avro-python3 1.10.1
2. Altere o diretório para o diretório no qual você salvou *sender.py* e *capturereader.py* e execute este comando:
   
   ```
   python sender.py
   ```
   
   Esse comando inicia um novo processo de Python para executar o remetente.
3. Aguarde alguns minutos para que a captura seja executada e, em seguida, insira o seguinte comando na janela Comando original:
   
   ```
   python capturereader.py
   ```

   Esse processador de captura usa o diretório local para baixar todos os blobs do contêiner e da conta de armazenamento. Ele processa todos os que não estão vazios e grava os resultados como arquivos CSV no diretório local.

## <a name="next-steps"></a>Próximas etapas
Confira [Exemplos do Python no GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
