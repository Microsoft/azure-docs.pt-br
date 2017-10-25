---
title: Explorar dados no armazenamento de blobs do Azure com o Pandas | Microsoft Docs
description: "Como explorar dados armazenados no contêiner de blob do Azure usando o Pandas."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: f86b2bb2c12edc5274777f049906c59dca22ac87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados no repositório de blob do Azure com o Pandas
Este documento aborda como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python [Pandas](http://pandas.pydata.org/) .

Os links do **menu** a seguir levam a tópicos que descrevem como usar as ferramentas para explorar dados de vários ambientes de armazenamento. Essa tarefa é uma etapa no [Processo de Ciência de Dados]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Armazenar seus dados em uma conta de armazenamento de blob do Azure. Se você precisar de instruções, consulte [Movimentação de dados de e para o Armazenamento do Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados em um DataFrame Pandas
Para explorar e manipular um conjunto de dados, primeiro eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um DataFrame Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do blob do Azure com o seguinte código Python de exemplo usando o serviço blob. Substitua a variável no código abaixo por seus valores específicos: 
   
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
2. Leia os dados em um quadro de dados Pandas do arquivo baixado.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora você está pronto para explorar os dados e gerar recursos neste conjunto de dados.

## <a name="blob-dataexploration"></a>Exemplos de exploração de dados usando o Pandas
Veja estão alguns exemplos de maneiras de explorar dados usando Pandas:

1. Inspecionar o **número de linhas e colunas** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Inspecione** as primeiras ou últimas **linhas** no conjunto de dados a seguir:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Verifique o **tipo de dados** indicado para cada coluna importada usando o seguinte código de exemplo
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Verifique as **estatísticas básicas** para as colunas no conjunto de dados da seguinte maneira
   
        dataframe_blobdata.describe()
5. Veja o número de entradas para cada valor de coluna da seguinte maneira
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Conte os valores ausentes** em comparação com o número real de entradas em cada coluna usando o seguinte código de exemplo
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se você tiver **valores ausentes** para uma coluna específica nos dados, poderá removê-los da seguinte maneira:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   Outra maneira de substituir valores ausentes é com a função de modo:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Crie um gráfico de **histograma** usando um número variável de compartimentos para plotar a distribuição de uma variável    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Examine **correlações** entre variáveis usando um gráfico disperso ou a função interna de correlação
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

