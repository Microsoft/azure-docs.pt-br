---
title: Dados capturados pela leitura do aplicativo do Python - Hubs de Eventos do Azure | Microsoft Docs
description: Exemplo que usa o SDK do Python do Azure para demonstrar o uso do recurso Captura de Hubs de Eventos.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822400"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Passo a passo da Captura de Hubs de Eventos: Python

A Captura é um recurso dos Hubs de Eventos do Azure. Use-a para fornecer automaticamente os dados de streaming em seu hub de eventos para uma conta do armazenamento de Blobs do Azure de sua escolha. Essa capacidade facilita a execução de processamento em lote na transmissão de dados em tempo real. Este artigo descreve como usar a Captura de Hubs de Eventos com o Python. Para saber mais sobre a Captura de Hubs de Eventos, confira o [artigo de visão geral](event-hubs-capture-overview.md).

Este exemplo usa o [SDK para Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso de Captura. O programa sender.py envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O hub de eventos está configurado para usar o recurso de Captura a fim de gravar esses dados no armazenamento de Blobs em lotes. O aplicativo capturereader.py lê esses blobs e cria um arquivo de acréscimo por dispositivo. Em seguida, o aplicativo grava os dados em arquivos .csv.

## <a name="what-youll-accomplish"></a>O que você vai realizar

1. Criar uma conta do armazenamento de Blobs do Azure e um contêiner de blobs nela, usando o portal do Azure.
2. Criar um namespace dos Hubs de Eventos usando o portal do Azure.
3. Criar um hub de eventos com o recurso de Captura habilitado, usando o portal do Azure.
4. Enviar dados para o hub de eventos com um script Python.
5. Ler os arquivos da captura e processá-los usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7.x
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Um [namespace dos Hubs de Eventos e um hub de eventos](event-hubs-create.md) ativos. 
- Habilite o recurso **Capturar** para o hub de eventos, seguindo as instruções de: [Habilitar a Captura de Hubs de Evento usando o Portal do Azure](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de Armazenamento de Blobs do Azure
1. Entre no [Portal do Azure][Azure portal].
2. No painel esquerdo do portal, selecione **Novo** > **Armazenamento** > **Conta de Armazenamento**.
3. Conclua as seleções no painel **Criar conta de armazenamento** e selecione **Criar**.
   
   ![Painel “Criar conta de armazenamento”][1]
4. Depois de ver a mensagem **Implantações Bem-sucedidas**, selecione o nome da nova conta de armazenamento e, no painel **Informações Básicas**, selecione **Blobs**. Quando o painel **Serviço Blob** for aberto, selecione **+ Contêiner** na parte superior. Nomeie o contêiner **capture** e, em seguida, feche o painel **Serviço Blob**.
5. Selecione **Chaves de acesso** no painel esquerdo e copie o nome da conta de armazenamento e o valor de **key1**. Salve esses valores no Bloco de notas ou em outro local temporário.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script Python para enviar eventos a seu Hub de Eventos
1. Abra seu editor de Python favorito, como o [Visual Studio Code][Visual Studio Code].
2. Crie um script chamado **sender.py**. Esse script envia 200 eventos para seu hub de eventos. Eles são simples leituras de ambiente enviadas em JSON.
3. Cole o seguinte código em sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Atualize o código anterior para usar o nome do namespace, o valor de chave e o nome do hub de eventos obtidos ao criar o namespace dos Hubs de Eventos.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Criar um script Python para ler os arquivos da Captura

1. Preencha o painel e selecione **Criar**.
2. Criar um script chamado **capturereader.py**. Esse script lê os arquivos capturados e cria um arquivo por dispositivo para gravar os dados somente para esse dispositivo.
3. Cole o código abaixo no capturereader.py:
   
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
4. Cole os valores apropriados do nome de sua conta de armazenamento e da chave na chamada a `startProcessing`.

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra um prompt de comando com o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Se você tem uma versão anterior do **azure-storage** ou **azure**, talvez precise usar a opção **--upgrade**.
   
   Talvez você também precise executar o seguinte comando (desnecessário na maioria dos sistemas):
   
   ```
   pip install cryptography
   ```
2. Altere o diretório para o qual você salvou sender.py e capturereader.py e execute este comando:
   
   ```
   start python sender.py
   ```
   
   Esse comando inicia um novo processo de Python para executar o remetente.
3. Aguarde alguns minutos para que a captura seja executada. Em seguida, digite o seguinte comando na janela de comando original:
   
   ```
   python capturereader.py
   ```

   Esse processador de captura usa o diretório local para baixar todos os blobs do contêiner/da conta de armazenamento. Ele processa todos os que não estão vazios e grava os resultados como arquivos .csv no diretório local.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os Hubs de Eventos usando os seguintes links:

* [Visão Geral da Captura de Hubs de Eventos][Overview of Event Hubs Capture]
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
