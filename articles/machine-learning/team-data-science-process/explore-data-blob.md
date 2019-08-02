---
title: Explorar dados no Armazenamento de Blobs do Azure com o Pandas – Processo de Ciência de Dados de Equipe
description: Como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python Pandas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 23fef994d01917f5f120c7fcb80871f6f2c82ab2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358592"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados no repositório de blob do Azure com o Pandas

Este artigos aborda como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python [pandas](https://pandas.pydata.org/).

Esta tarefa é uma etapa no [Processo de Ciência de Dados da Equipe](overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md)
* Armazenar seus dados em uma conta de armazenamento de blob do Azure. Se você precisar de instruções, consulte [Movimentação de dados de e para o Armazenamento do Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados em um DataFrame Pandas
Para explorar e manipular um conjunto de dados, primeiro eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um DataFrame Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do blob do Azure com o seguinte código Python de exemplo usando o serviço blob. Substitua a variável no código abaixo por seus valores específicos:

```python
from azure.storage.blob import BlockBlobService
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
print(("It takes %s seconds to download "+blobname) % (t2 - t1))
```

1. Leia os dados em um pandas DataFrame do arquivo baixado.

```python
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Agora você está pronto para explorar os dados e gerar recursos neste conjunto de dados.

## <a name="blob-dataexploration"></a>Exemplos de exploração de dados usando o Pandas
Veja estão alguns exemplos de maneiras de explorar dados usando Pandas:

1. Inspecionar o **número de linhas e colunas**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Inspecione** as primeiras ou últimas **linhas** no conjunto de dados a seguir:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Verifique o **tipo de dados** indicado para cada coluna importada usando o seguinte código de exemplo

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
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
print miss_num
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
