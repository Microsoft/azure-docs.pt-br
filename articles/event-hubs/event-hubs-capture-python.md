---
title: Passo a passo da Captura de Hubs de Eventos do Azure | Microsoft Docs
description: Exemplo que usa o SDK do Python do Azure para demonstrar o uso do recurso Captura de Hubs de Eventos.
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 5fb691ec53fed20e5df4f581da10b964c07e09b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-capture-walkthrough-python"></a>Passo a passo da Captura de Hubs de Eventos: Python

A Captura de Hubs de Eventos é um recurso dos Hubs de Eventos que habilita o fornecimento automático dos dados de streaming no Hub de Eventos para uma conta de Armazenamento de Blobs do Azure desejada. Essa capacidade facilita a execução de processamento em lote na transmissão de dados em tempo real. Este artigo descreve como usar a Captura de Hubs de Eventos com o Python. Para saber mais sobre a Captura de Hubs de Eventos, confira o [artigo de visão geral](event-hubs-capture-overview.md).

Este exemplo usa o [SDK para Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso de Captura. O programa sender.py envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O Hub de Eventos está configurado para usar o recurso de Captura a fim de gravar esses dados no Armazenamento de Blobs em lotes. Em seguida, o aplicativo capturereader.py lê esses blobs e cria um arquivo de acréscimo por dispositivo e grava os dados em arquivos. csv.

## <a name="what-will-be-accomplished"></a>O que será realizado

1. Criar uma conta de Armazenamento de Blobs do Azure e um contêiner de blobs dentro dela, usando o portal do Azure.
2. Criar um namespace do Hub de Eventos usando o portal do Azure.
3. Crie um Hub de Eventos com o recurso de Captura habilitado, usando o Portal do Azure.
4. Envie dados para o Hub de Eventos com um script Python.
5. Leia os arquivos da captura e processe-os com outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7.x
- Uma assinatura do Azure
- Um [Namespace de Hubs de Eventos e Hub de Eventos ativos.](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure
1. Faça logon no [Portal do Azure][Azure portal].
2. No painel de navegação esquerdo do portal, clique em **Novo**, em **Armazenamento** e em **Conta de Armazenamento**.
3. Preencha os campos na folha da conta de armazenamento e clique em **Criar**.
   
   ![][1]
4. Depois de ver a mensagem **Implantações Bem-sucedidas**, clique no nome da nova conta de armazenamento e, na folha **Fundamentos**, clique em **Blobs**. Quando a folha **Serviço Blob** abrir, clique em **+ Contêiner** na parte superior. Nomeie o contêiner **captura** e feche a folha do **Serviço Blob**.
5. Clique em **Chaves de acesso** na folha esquerda e copie o nome da conta de armazenamento e o valor de **key1**. Salve esses valores no Bloco de notas ou em outro local temporário.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script Python para enviar eventos a seu Hub de Eventos
1. Abra seu editor de Python favorito, como o [Visual Studio Code][Visual Studio Code].
2. Crie um script chamado **sender.py**. Esse script envia 200 eventos para seu hub de eventos. Eles são simples leituras de ambiente enviadas em JSON.
3. Cole o seguinte código em sender.py:
   
  ```python
  import uuid
  import datetime
  import random
  import json
  from azure.servicebus import ServiceBusService
   
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

1. Preencha a folha e clique em **Criar**.
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
          if blob.properties.content_length != 0:
              print('Downloaded a non empty blob: ' + blob.name)
              cleanName = string.replace(blob.name, '/', '_')
              block_blob_service.get_blob_to_path(container, blob.name, cleanName)
              processBlob(cleanName)
              os.remove(cleanName)
          block_blob_service.delete_blob(container, blob.name)
  startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
  ```
4. Não se esqueça de colar os valores apropriados do nome da sua conta de armazenamento e da chave na chamada para `startProcessing`.

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra um prompt de comando com o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
  ```
  pip install azure-storage
  pip install azure-servicebus
  pip install avro
  ```
   
  Se você tiver uma versão anterior do Azure ou do Armazenamento do Azure, talvez precise usar a opção **--upgrade**
   
  Você também precisa executar o seguinte (desnecessário na maioria dos sistemas):
   
  ```
  pip install cryptography
  ```
2. Altere o diretório para o qual você salvou sender.py e capturereader.py e execute este comando:
   
  ```
  start python sender.py
  ```
   
  Esse comando inicia um novo processo de Python para executar o remetente.
3. Agora, aguarde alguns minutos para que a captura seja executada. Em seguida, digite o seguinte comando na janela de comando original:
   
   ```
   python capturereader.py
   ```

   Esse processador de captura usa o diretório local para baixar todos os blobs do contêiner/da conta de armazenamento. Ele processa todos os que não estão vazios e grava os resultados como arquivos .csv no diretório local.

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral da Captura de Hubs de Eventos][Overview of Event Hubs Capture]
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
