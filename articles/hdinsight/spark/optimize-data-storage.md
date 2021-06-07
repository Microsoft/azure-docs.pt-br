---
title: Otimizar o armazenamento de dados para o Apache Spark – Azure HDInsight
description: Saiba como otimizar o armazenamento de dados para uso com o Apache Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98926993"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Otimização de armazenamento de dados para Apache Spark

Este artigo discute estratégias para otimizar o armazenamento de dados para uma execução de trabalho do Apache Spark eficiente no Azure HDInsight.

## <a name="overview"></a>Visão geral

O Spark fornece suporte a muitos formatos, como csv, json, xml, parquet, orc e avro. O Spark pode ser estendido para fornecer suporte a muitos outros formatos com fontes de dados externos - para obter mais informações, consulte [Pacotes do Apache Spark](https://spark-packages.org).

O melhor formato para desempenho é parquet com *compactação snappy*, que é o padrão no Spark 2.x. O parquet armazena dados em formato colunar e é altamente otimizado no Spark.

## <a name="choose-data-abstraction"></a>Escolher abstração de dados

As versões anteriores do Spark usam RDDs para dados abstratos e o Spark 1.3 e 1.6 introduziram DataFrames e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
    * Melhor escolha na maioria das situações.
    * Fornece otimização de consulta através do Catalyst.
    * Geração de código em estágio inteiro.
    * Acesso direto à memória.
    * Baixa sobrecarga de coleta de lixo (GC).
    * Não é tão amigável para desenvolvedores como os Conjuntos de Dados, pois não há verificações de tempo de compilação ou programação de objeto de domínio.
* **Conjuntos de Dados**
    * Bom em pipelines ETL complexos, onde o impacto no desempenho é aceitável.
    * Não é bom em agregações onde o impacto no desempenho pode ser considerável.
    * Fornece otimização de consulta através do Catalyst.
    * Amigável para o desenvolvedor ao fornecer programação de objeto de domínio e verificações de tempo de compilação.
    * Adicionar sobrecarga de serialização/desserialização.
    * Alta sobrecarga de GC.
    * Interrompe a geração de código em estágio inteiro.
* **RDDs**
    * Não é necessário usar RDDs, a menos que você precise criar um RDD personalizado.
    * Nenhuma otimização de consulta através do Catalyst.
    * Nenhuma geração de código em estágio inteiro.
    * Alta sobrecarga de GC.
    * Deve usar as API herdadas do Spark 1.x.

## <a name="select-default-storage"></a>Selecionar o armazenamento padrão

Ao criar um cluster do Spark, você terá a opção de selecionar Armazenamento de Blobs do Azure ou Azure Data Lake Storage como o armazenamento padrão do cluster. Ambas as opções oferecem o benefício do armazenamento de longo prazo para clusters transitórios. Portanto, seus dados não são excluídos automaticamente quando você exclui o cluster. É possível recriar um cluster transitório e ainda acessar seus dados.

| Tipo de Armazenamento | Sistema de Arquivos | Velocidade | Transitório | Casos de uso |
| --- | --- | --- | --- | --- |
| Armazenamento do Blobs do Azure | **wasb:** //url/ | **Standard** | Sim | Cluster transitório |
| Armazenamento de Blobs do Azure (seguro) | **wasbs:** //url/ | **Standard** | Sim | Cluster transitório |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **Mais rápido** | Sim | Cluster transitório |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **Mais rápido** | Sim | Cluster transitório |
| HDFS local | **hdfs:** //url/ | **Mais rápida** | Não | Cluster interativo 24/7 |

Para obter uma descrição completa das opções de armazenamento, confira [Comparar as opções de armazenamento para uso com os clusters do Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Usar o cache

O Spark fornece os próprios mecanismos de cache nativo que podem ser utilizados através de diferentes métodos, como `.persist()`, `.cache()` e `CACHE TABLE`. Esse cache nativo é efetivo com pequenos conjuntos de dados e em pipelines ETL, em que é necessário armazenar em cache resultados intermediários. No entanto, atualmente, o cache nativo do Spark não funciona bem com o particionamento, pois uma tabela armazenada em cache não mantém os dados de particionamento. Uma técnica de cache mais genérica e confiável é o *cache de camada de armazenamento*.

* Cache do Spark Nativo (não recomendado)
    * Bom para pequenos conjuntos de dados.
    * Não funciona com particionamento, o que pode mudar em futuras versões do Spark.

* Cache de nível de armazenamento (recomendado)
    * Pode ser implementado no HDInsight usando o recurso [Cache de E/S](apache-spark-improve-performance-iocache.md).
    * Usa cache SSD e em memória.

* HDFS local (recomendado)
    * caminho `hdfs://mycluster`.
    * Usa cache SSD.
    * Os dados armazenados em cache serão perdidos ao excluir o cluster, e que será necessário recompilar o cache.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos do Spark são distribuídos, por isso a serialização de dados apropriada é importante para obter o melhor desempenho.  Há duas opções de serialização para Spark:

* A serialização Java é o padrão.
* A serialização `Kryo` é um formato mais recente e pode resultar em serialização mais rápida e compacta do que Java.  O `Kryo` exige que você registre as classes no programa e não dá suporte a todos os tipos serializáveis.

## <a name="use-bucketing"></a>Usar bucketing

O bucketing é semelhante ao particionamento de dados. Porém, cada bucket pode conter um conjunto de valores de coluna, em vez de apenas um. Esse método funciona bem para particionamento em números de valores grandes (em milhões ou mais), como identificadores de produto. Um bucket é determinado por hash e chave de bucket da linha. As tabelas em bucket oferecem otimizações exclusivas porque armazenam metadados como foram particionados e classificados.

Alguns recursos de bucket avançados são:

* Otimização de consulta baseada em informações meta de bucket.
* Agregações otimizadas.
* Junções otimizadas.

É possível utilizar particionamento e bucket ao mesmo tempo.

## <a name="next-steps"></a>Próximas etapas

* [Otimização do processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimização do uso de memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
