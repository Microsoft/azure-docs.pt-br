---
title: Copiar dados para e do WASB no Data Lake Store usando o Distcp| Microsoft Docs
description: "Use a ferramenta Distcp para copiar dados de e para os Blobs de Armazenamento do Azure para o Repositório Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 1c9e100b4a0e7781f0782a49835d50492895ded1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Use Distcp para copiar dados entre o Repositório do Data Lake e os Blobs de Armazenamento do Azure
> [!div class="op_single_selector"]
> * [Como usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Como usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se você tem um cluster HDInsight com acesso ao Data Lake Store, você pode usar as ferramentas do ecossistema Hadoop, como Distcp, para copiar dados **de e para** um armazenamento de cluster do HDInsight (WASB) em uma conta do Data Lake Store. Este artigo fornece instruções de como usar a ferramenta Distcp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.

## <a name="do-you-learn-fast-with-videos"></a>Você aprende rapidamente com vídeos?
[Assista a este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre como copiar dados entre o Repositório do Data Lake e os Blobs de Armazenamento do Azure usando o DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar Distcp de um cluster HDInsight do Linux

Um cluster do HDInsight é fornecido com o utilitário Distcp, que pode ser usado para copiar dados de fontes diferentes em um cluster do HDInsight. Se você tiver configurado o cluster HDInsight para usar o Data Lake Store como um armazenamento adicional, o utilitário Distcp poderá ser usado prontamente para copiar dados de e para uma conta do Data Lake Store. Nesta seção, vamos examinar como usar o utilitário Distcp.

1. Da sua área de trabalho, use o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos do prompt de SSH.

2. Verifique se você pode acessar os WASB (Blobs de Armazenamento do Azure). Execute o comando a seguir:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no blob de armazenamento.

3. Da mesma forma, verifique se você pode acessar a conta de Repositório do Data Lake do cluster. Execute o comando a seguir:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    A saída deve fornecer uma lista de arquivos/pastas na conta do Data Lake Store.

4. Use Distcp para copiar dados do WASB para uma conta de Repositório do Data Lake.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    O comando copia o conteúdo da pasta **/example/data/gutenberg/** do WASB em **/myfolder** na conta do Data Lake Store.

5. De modo semelhante, use Distcp para copiar dados da conta de Repositório do Data Lake para o WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/myfolder** da conta do Data Lake Store na pasta **/example/data/gutenberg/** no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação ao Data Lake Store. O número de cópias simultâneas é controlado pela configuração do parâmetro de número de mapeadores (“m”) na linha de comando. Esse parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é 20.

**Exemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mapeadores que serão usados?

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a memória total de YARN** ‑ A primeira etapa é determinar a memória YARN disponível para o cluster em que você executa o trabalho DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia Configurações para ver a memória YARN. Para obter a memória YARN total, multiplique a memória YARN por nó pelo número de nós que você tem no cluster.

* **Etapa 2: Calcular o número de mapeadores** ‑ O valor de **m** é igual ao quociente de memória total de YARN dividida pelo tamanho do contêiner YARN. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia Configurações. O tamanho do contêiner YARN é exibido nessa janela. A equação para chegar até o número de mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Suponhamos que você tenha 4 nós D14v2s no cluster e está tentando transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contêm volumes diversos de dados e os tamanhos dos arquivos em cada pasta também são diferentes.

* Total de memória YARN – No portal do Ambari você determina que a memória YARN é 96 GB para um nó D14. Portanto, o total de memória YARN para um cluster de quatro nós é: 

        YARN memory = 4 * 96GB = 384GB

* Número de mapeadores ‑ Do portal do Ambari a você determina que o tamanho do contêiner YARN é 3072 para um nó de cluster D14. Portanto, o número de mapeadores é:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiverem usando memória, então você poderá usar somente uma parte da memória YARN do cluster para o DistCp.

### <a name="copying-large-datasets"></a>Copiando grandes conjuntos de dados

Quando o tamanho do conjunto de dados a ser movido for grande (por exemplo, maior que 1 TB) ou se você tiver muitas pastas diferentes, considere usar vários trabalhos DistCp. Provavelmente, não haverá nenhum ganho de desempenho, porém isso espalhará os trabalhos para que, se algum deles falhar, você só precise reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar mapeadores de tamanho semelhantes para otimizar o desempenho. Aumentar o número de mapeadores nem sempre melhora o desempenho.

* O DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais mapeadores do que arquivos. Como DistCp só pode atribuir um mapeador para um arquivo, isso limita a quantidade de simultaneidade que pode ser usada para copiar grandes arquivos.

* Se você tiver um pequeno número de grandes arquivos, divida-os em partes de arquivo de 256 MB para proporcionar um maior potencial de simultaneidade. 
 
* Se você estiver copiando de uma conta de Armazenamento de Blobs do Azure, seu trabalho de cópia poderá ficar limitado do lado do Armazenamento de Blobs. Isso pode degradar o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do Armazenamento de Blobs do Azure, confira Limites de Armazenamento do Azure em [Limites de serviço e assinatura do Azure](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Consulte também
* [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
