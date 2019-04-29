---
title: Diretrizes de Ajuste de Desempenho do Armazenamento de Dados do Azure Data Lake Gen1 | Microsoft Docs
description: Diretrizes de ajuste de desempenho do Armazenamento de dados do Azure Data Lake Gen1
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: a8a50db5ece242bc00a28e66e21c863388950d6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437546"
---
# <a name="tuning-azure-data-lake-storage-gen1-for-performance"></a>Ajustando o Azure Data Lake Storage Gen1 para desempenho

O Data Storage do Azure Data Lake suporta alta taxa de transferência para análise intensiva de E / S e movimentação de dados.  No Data Lake Storage Gen1, usar todos os recursos disponíveis - a quantidade de dados que podem ser lidos ou gravados por segundo - é importante para obter o melhor desempenho.  Isso é obtido executando o maior número possível de leituras e gravações em paralelo.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

O Data Lake Storage Gen1 pode ser dimensionado para fornecer o throughput necessário para todo o cenário de análise. Por padrão, uma conta do Data Lake Storage Gen1 fornece taxa de transferência automaticamente suficiente para atender às necessidades de uma ampla categoria de casos de uso. Para os casos em que os clientes se deparam com o limite padrão, a conta do Data Lake Storage Gen1 pode ser configurada para fornecer mais rendimento entrando em contato com o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para o Data Lake Storage Gen1, é importante considerar que o hardware de origem, o hardware de rede de origem e a conectividade de rede com o Data Lake Storage Gen1 podem ser o gargalo.  

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante garantir que a movimentação de dados não seja afetada por esses fatores.

### <a name="source-hardware"></a>Hardware de Origem

Se você estiver usando computadores locais ou VMs no Azure, você deverá selecionar cuidadosamente o hardware apropriado. Para Hardware de Disco de Origem, prefira SSDs a HDDs e escolha o hardware de disco com eixos mais rápidos. Para Hardware de Rede de Origem, use as NICs mais rápidas que puder.  No Azure, recomendamos VMs Azure D14, que têm o hardware de rede e de disco adequadamente avançado.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Conectividade de rede para armazenamento de dados em Lake Gen1

A conectividade de rede entre os dados de origem e o Data Lake Storage Gen1 às vezes pode ser o gargalo. Quando os dados de origem forem locais, considere o uso de uma conexão dedicada com o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se os dados de origem estiverem no Azure, o desempenho será melhor quando os dados estiverem na mesma região do Azure que a conta do Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurar as ferramentas de ingestão de dados para paralelização máxima

Depois que você tiver resolvido os gargalos de hardware de origem e conectividade de rede acima, você estará pronto para configurar as ferramentas de ingestão. A tabela a seguir resume as configurações de chave para diversas ferramentas de ingestão populares e fornece artigos detalhados de ajuste de desempenho para eles.  Para saber mais sobre qual ferramenta usar para seu cenário, visite este [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Ferramenta               | Configurações     | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| Powershell       | PerFileThreadCount, ConcurrentFileCount |  [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Unidades do Azure Data Lake Analytics  |   [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper)   | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Fábrica de dados do Azure| parallelCopies    | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estruturar seu conjunto de dados

Quando os dados são armazenados no Data Lake Storage Gen1, o tamanho do arquivo, o número de arquivos e a estrutura da pasta afetam o desempenho.  A seção a seguir descreve as práticas recomendadas nessas áreas.  

### <a name="file-size"></a>Tamanho do arquivo

Normalmente os mecanismos de análise, tais como HDInsight e Azure Data Lake Analytics, têm uma sobrecarga por arquivo.  Se você armazenar os dados como vários arquivos pequenos, isso poderá afetar negativamente o desempenho.  

Em geral, organize seus dados em arquivos de tamanhos maior para melhorar o desempenho.  Como regra geral, organize conjuntos de dados em arquivos de 256 MB ou maiores. Em alguns casos, como imagens e dados binários, não é possível processá-los em paralelo.  Nesses casos, é recomendável manter arquivos individuais abaixo de 2 GB.

Às vezes, os pipelines de dados têm controle limitado sobre os dados brutos, que têm muitos arquivos pequenos.  Recomenda-se ter um processo de "cooking" que gere arquivos maiores para usar em aplicativos downstream.

### <a name="organizing-time-series-data-in-folders"></a>Organizar os dados de série temporal em pastas

Para cargas de trabalho de Hive e ADLA, a remoção de partições de dados de série temporal pode ajudar algumas consultas a ler apenas um subconjunto dos dados, o que melhora o desempenho.    

Esses pipelines que ingerem dados de série temporal muitas vezes colocam seus arquivos com uma nomenclatura muito estruturada para arquivos e pastas. Abaixo está um exemplo muito comum para dados estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Observe que as informações de data e hora são exibidas tanto como pastas quanto no nome de arquivo.

Para data e hora, o padrão a seguir é comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Novamente, a escolha que você fizer com a pasta e organização do arquivo deverá otimizar para os maiores tamanhos de arquivo e um número razoável de arquivos em cada pasta.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar os trabalhos com uso intensivo de E/S em cargas de trabalho do Hadoop e Spark no HDInsight

Trabalhos se enquadram em uma destas três categorias:

* **Com uso intensivo de CPU.**  Esses trabalhos têm tempos de computação longos com tempos de E/S mínimos.  Exemplos incluem aprendizado de máquina e trabalhos de processamento de linguagem natural.  
* **Com uso intensivo de memória.**  Estes trabalhos usam muita memória.  Exemplos incluem PageRank e trabalhos de análise em tempo real.  
* **Com uso intensivo de E/S.**  Esses trabalhos passam a maior parte do tempo fazendo E/S.  Um exemplo comum é um trabalho de cópia que realiza somente operações de leitura e gravação.  Outros exemplos incluem trabalhos de preparação de dados que leem muitos dados, executam alguma transformação de dados e, em seguida, gravam os dados de volta para o repositório.  

As diretrizes a seguir são aplicáveis somente a trabalhos com uso intensivo de E/S.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões do HDInsight.** Para melhor desempenho, use a versão mais recente do HDInsight.
* **Regiões.** Coloque a conta Data Lake Storage Gen1 na mesma região que o cluster HDInsight.  

Um cluster HDInsight é composto de dois nós principais e alguns nós de trabalho. Cada nó de trabalho fornece um número específico de núcleos e memória, o que é determinado pelo tipo de VM.  Ao executar um trabalho, o YARN é o negociador de recursos que aloca a memória disponível e núcleos para criar contêineres.  Cada contêiner executa as tarefas necessárias para concluir o trabalho.  Contêineres são executados em paralelo para processar tarefas rapidamente. Portanto, o desempenho é aprimorado executando o máximo possível de contêineres paralelos.

Há três camadas em um cluster HDInsight que podem ser ajustadas para aumentar o número de contêineres e usar toda a taxa de transferência disponível.  

* **Camada física**
* **Camada YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Execute o cluster com mais nós e/ou VMs de tamanhos maior.**  Um cluster maior permite executar mais contêineres YARN, conforme mostrado na figura abaixo.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Use VMs com mais largura de banda de rede.**  A quantidade de largura de banda da rede pode ser um gargalo se houver menos largura de banda de rede do que a taxa de transferência do Data Lake Storage Gen1.  Diferentes VMs terão diversos tamanhos de largura de banda de rede.  Escolha um tipo de VM com a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>Camada YARN

**Use contêineres YARN menores.**  Reduza o tamanho de cada contêiner YARN para criar mais contêineres com a mesma quantidade de recursos.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Dependendo de sua carga de trabalho, sempre haverá um tamanho de contêiner YARN mínimo necessário. Se você selecionar um contêiner muito pequeno, os trabalhos encontrarão problemas de falta de memória. Normalmente, contêineres YARN não devem ser menores que 1 GB. É comum ver contêineres YARN de 3 GB. Para algumas cargas de trabalho, talvez contêineres YARN maiores sejam necessários.  

**Aumente os núcleos por contêiner YARN.**  Aumente o número de núcleos alocados para cada contêiner para aumentar o número de tarefas paralelas que são executadas em cada contêiner.  Isso funciona para aplicativos como Spark, que executam várias tarefas por contêiner.  Para aplicativos como o Hive, que executam um único thread de cada contêiner, é melhor ter mais contêineres em vez de mais núcleos por contêiner.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Use todos os contêineres disponíveis.**  Defina o número de tarefas para que seja igual ou maior que o número de contêineres disponíveis de modo que todos os recursos sejam utilizados.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Tarefas com falha têm alto custo.** Se cada tarefa tem uma grande quantidade de dados a serem processados, a falha de uma tarefa resulta em uma repetição cara.  Portanto, é melhor criar mais tarefas, já que cada uma delas processa uma pequena quantidade de dados.

Além das diretrizes gerais acima, cada aplicativo tem diferentes parâmetros disponíveis para ajustar para esse aplicativo específico. A tabela a seguir lista alguns dos parâmetros e links para começar com o ajuste de desempenho para cada aplicativo.

| Carga de trabalho               | Parâmetro para definir tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark no HDInsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive no HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce no HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalho</li><li>Número de instâncias de spout executor</li><li>Número de instâncias de bolt executor </li><li>Número de tarefas de spout</li><li>Número de tarefas de bolt</li></ul>|

## <a name="see-also"></a>Consulte também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução à Análise Data Lake do Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
