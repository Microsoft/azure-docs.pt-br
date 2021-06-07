---
title: Explorar dados no armazenamento de BLOBs do Azure com pandas-processo de ciência de dados de equipe
description: Como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python Pandas.
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
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805682"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados no armazenamento de BLOBs do Azure com pandas

Este artigos aborda como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python [pandas](https://pandas.pydata.org/).

Esta tarefa é uma etapa no [Processo de Ciência de Dados da Equipe](overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md)
* Armazenou seus dados em uma conta de armazenamento de BLOBs do Azure. Se você precisar de instruções, consulte [Movimentação de dados de e para o Armazenamento do Azure](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados em um DataFrame Pandas
Para explorar e manipular um conjunto de dados, primeiro eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um DataFrame Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do blob do Azure com o seguinte exemplo de código do Python usando o serviço Blob. Substitua a variável no código abaixo por seus valores específicos:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Leia os dados em um pandas DataFrame do arquivo baixado.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Agora você está pronto para explorar os dados e gerar recursos neste conjunto de dados.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Exemplos de exploração de dados usando o Pandas
Veja estão alguns exemplos de maneiras de explorar dados usando Pandas:

1. Inspecionar o **número de linhas e colunas**

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Inspecione** as primeiras ou últimas **linhas** no conjunto de dados a seguir:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Verifique o **tipo de dados** indicado para cada coluna importada usando o seguinte código de exemplo

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Verifique as **estatísticas básicas** para as colunas no conjunto de dados da seguinte maneira

    ```python
    dataframe_blobdata.describe()
    ```

1. Veja o número de entradas para cada valor de coluna da seguinte maneira

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Conte os valores ausentes** em comparação com o número real de entradas em cada coluna usando o seguinte código de exemplo

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Se você tiver **valores ausentes** para uma coluna específica nos dados, poderá removê-los da seguinte maneira:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Outra maneira de substituir valores ausentes é com a função de modo:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Crie um gráfico de **histograma** usando um número variável de compartimentos para plotar a distribuição de uma variável

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Examine **correlações** entre variáveis usando um gráfico disperso ou a função interna de correlação

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
