---
title: Dados de exemplo no armazenamento de BLOBs do Azure – processo de ciência de dados da equipe
description: Dados de amostragem armazenados no armazenamento de BLOBs do Azure baixando-os programaticamente e, em seguida, fazendo amostragem usando procedimentos escritos em Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788834"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Dados de exemplo no armazenamento de BLOBs do Azure

Este artigo aborda os dados de amostragem armazenados no armazenamento de BLOBs do Azure baixando-os programaticamente e, em seguida, fazendo amostragem usando os procedimentos escritos em Python.

**Por que fazer amostragem dos dados?**
Se o conjunto de dados que você deseja analisar for grande, geralmente, é uma boa ideia reduzir os dados para um tamanho menor, mas representativo e mais gerenciável. A amostragem facilita a compreensão dos dados, a exploração e a engenharia de recursos. Sua função no Processo de Análise do Cortana é habilitar a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml).

## <a name="download-and-down-sample-data"></a>Baixar e reduzir os dados de exemplo
1. Baixe os dados do armazenamento de BLOBs do Azure usando o serviço blob do seguinte código Python de exemplo: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Leia os dados em um quadro de dados Pandas do arquivo baixado anteriormente.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Reduza os dados usando o `random.choice` da `numpy`, da seguinte maneira:

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Agora você pode trabalhar com o quadro de dados acima com a amostra de um percentual para exploração e geração de recursos adicionais.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Carregar os dados e lê-los no Azure Machine Learning
É possível usar o código de exemplo a seguir para os reduzir os dados e usá-los diretamente no Azure Machine Learning:

1. Escrever o quadro de dados em um arquivo local

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Carregue o arquivo local para um blob do Azure usando o seguinte código de exemplo:

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Leia os dados do blob do Azure usando o [Importar Dados](/azure/machine-learning/studio-module-reference/import-data) do Azure Machine Learning, como mostrado na imagem abaixo:

![blob de leitor](./media/sample-data-blob/reader_blob.png)
