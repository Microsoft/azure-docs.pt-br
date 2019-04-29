---
title: Copiar dados para e do WASB no Azure Data Lake Storage Gen1 usando o Distcp| Microsoft Docs
description: Use a ferramenta Distcp para copiar dados de e para os Blobs de Armazenamento do Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878793"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Use o Distcp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Como usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Como usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se você tiver um cluster HDInsight com acesso ao Azure Data Lake Storage Gen1, use as ferramentas do ecossistema do Hadoop, como o Distcp para copiar dados, **para e de** um armazenamento de cluster HDInsight (WASB) para uma conta do Data Lake Storage Gen1. Este artigo fornece instruções de como usar a ferramenta Distcp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar Distcp de um cluster HDInsight do Linux

Um cluster do HDInsight é fornecido com o utilitário Distcp, que pode ser usado para copiar dados de fontes diferentes em um cluster do HDInsight. Se você tiver configurado o cluster HDInsight para usar o Data Lake Storage Gen1 como um armazenamento adicional, o utilitário Distcp poderá ser usado prontamente para copiar dados de e para uma conta do Data Lake Storage Gen1. Nesta seção, vamos examinar como usar o utilitário Distcp.

1. Da sua área de trabalho, use o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos do prompt de SSH.

2. Verifique se você pode acessar os WASB (Blobs de Armazenamento do Azure). Execute o comando a seguir:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no blob de armazenamento.

3. Da mesma forma, verifique se você pode acessar a conta de do Data Lake Storage Gen1 do cluster. Execute o comando a seguir:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    A saída deve fornecer uma lista de arquivos/pastas na conta do Data Lake Storage Gen1.

4. Use Distcp para copiar dados do WASB para uma conta do Data Lake Storage Gen1.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    O comando copia o conteúdo da pasta **/example/data/gutenberg/** do WASB em **/myfolder** na conta do Data Lake Storage Gen1.

5. De modo semelhante, use Distcp para copiar dados da conta do Data Lake Storage Gen1 para o WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/myfolder** da conta do Data Lake Storage Gen1 na pasta **/example/data/gutenberg/** no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação ao Data Lake Storage Gen1. O número de cópias simultâneas é controlado pela configuração do parâmetro de número de mapeadores (“m”) na linha de comando. Esse parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é 20.

**Exemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mapeadores que serão usados?

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a memória YARN total** -a primeira etapa é determinar a memória YARN disponível para o cluster onde você executa o trabalho DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia Configurações para ver a memória YARN. Para obter a memória YARN total, multiplique a memória YARN por nó pelo número de nós que você tem no cluster.

* **Etapa 2: Calcule o número de mapeadores** - O valor de **m** é igual ao quociente da memória total do YARN dividido pelo tamanho do contêiner do YARN. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia Configurações. O tamanho do contêiner YARN é exibido nessa janela. A equação para chegar até o número de mapeadores (**m**) é

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
* [Copiar dados do Azure Storage Blobs para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
