---
title: "Conexões de dados de origem adicionais de exemplo possíveis com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de conexões de dados de origem possíveis com a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Exemplo de conexões origem personalizada (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md)

## <a name="loading-data-from-dataworld"></a>Carregando dados de data.world

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="register-yourself-at-dataworld"></a>Registrar-se em data.world
É necessário um token de API que você obtém do site data.world.

#### <a name="install-dataworld-library"></a>Instalar a biblioteca data.world

Abra a interface de linha de comando do Azure Machine Learning Workbench em _Arquivo->Abrir interface de linha de comando_

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Em seguida, execute `dw configure` na linha de comando, que solicita o token. Quando você inserir o token, um diretório .dw/ será criado no diretório inicial e seu token será armazenado lá.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Agora você deve ser capaz de importar as bibliotecas data.world.

#### <a name="load-data-into-data-preparation"></a>Carregar dados na preparação de dados

Crie um novo fluxo de dados baseado em script e use o script a seguir para carregar os dados de data.world

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>Carregar dados do CosmosDB na preparação de dados

Crie um novo fluxo de dados baseado em script e use o script a seguir para carregar os dados do CosmosDB (as bibliotecas precisarão ser instaladas primeiro, consulte o documento de referência no link acima)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
