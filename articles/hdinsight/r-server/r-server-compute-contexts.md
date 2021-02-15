---
title: Opções de contexto de computação para o ML Services no Azure HDInsight
description: Conheça as diferentes opções de contexto de computação disponíveis para usuários com o ML Services no HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 71ce0d87faa33bd7d533242edfcf3b131c8f7e47
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943960"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opções de contexto de computação para o ML Services no HDInsight

O ML Services no Microsoft Azure HDInsight controla como as chamadas são executadas, definindo o contexto de computação. Este artigo descreve as opções que estão disponíveis para especificar se e como a execução é paralelizada entre núcleos do nó de borda ou o cluster HDInsight.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do RevoScaleR nos núcleos do servidor do nó de borda. Você também pode executá-los nos nós do cluster usando os contextos de cálculo Hadoop Map Reduce ou Apache Spark do RevoScaleR.

## <a name="ml-services-on-azure-hdinsight"></a>ML Services no Microsoft Azure HDInsight

O [ML Services no Microsoft Azure HDInsight](r-server-overview.md) fornece os recursos mais recentes para análises baseadas em R. Ele pode usar dados armazenados em um contêiner Apache Hadoop HDFS em sua conta de armazenamento de [BLOBs do Azure](../../storage/common/storage-introduction.md "Armazenamento de Blobs do Azure") , em um data Lake Store ou no sistema de arquivos Linux local. Como os serviços ML se baseiam no R de software livre, os aplicativos baseados em R que você cria podem aplicar qualquer um dos mais de 8000 pacotes de R de software livre. Eles também podem usar as rotinas no [RevoScaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), o pacote de análise de Big Data da Microsoft que está incluído no ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de computação para um nó de extremidade

Em geral, um script R executado no cluster do ML Services no nó de borda é executado no interpretador R nesse nó. As exceções são aquelas etapas que chamam uma função RevoScaleR. As chamadas de RevoScaleR são executadas em um ambiente de computação que é determinado pela configuração do contexto de computação do RevoScaleR.  Quando você executa o script R de um nó de borda, os valores possíveis de contexto de computação são:

- local sequencial (*local*)
- local paralelo (*localpar*)
- Map Reduce
- Spark

As opções *local* e *localpar* diferem apenas em como as chamadas de **rxExec** são executadas. As duas executam outras chamadas de função rx de forma paralela entre os núcleos disponíveis, a menos que seja especificado de outra forma por meio do uso da opção **numCoresToUse** de RevoScaleR, por exemplo, `rxOptions(numCoresToUse=6)`. Opções de execução paralela oferecem um desempenho ideal.

A tabela a seguir resume as várias opções de contexto de computação para definir como as chamadas são executadas:

| Contexto de computação  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequencial | rxSetComputeContext('local')    | Execução em paralelo entre os núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Local paralelo   | rxSetComputeContext('localpar') | Execução paralela entre os núcleos do servidor de nó de borda |
| Spark            | RxSpark()                       | Execução distribuída em paralelo por meio do Spark em todos os nós do cluster do HDI |
| Map Reduce       | RxHadoopMR()                    | Execução distribuída em paralelo por meio do Map Reduce em todos os nós do cluster do HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir em um contexto de computação

Qual das três opções que fornecem execução em paralelo você ddeve escolher depende da natureza de seu trabalho de análise, do tamanho e do local dos seus dados. Não há uma fórmula simples que indique a você, qual contexto de computação usar. No entanto, há alguns princípios importantes que podem ajudar você a fazer a escolha certa ou, pelo menos, ajudar a refinar suas escolhas antes de executar um benchmark. Esses princípios básicos incluem:

- O sistema de arquivos Linux local é mais rápido do que o HDFS.
- As análises repetidas serão mais rápidas se os dados forem locais e em XDF.
- É preferível transmitir pequenas quantidades de dados de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-os em XDF antes da análise.
- A sobrecarga da cópia ou transmissão dos dados para o nó de borda para análise se torna incontrolável para grandes quantidades de dados.
- O Apache Spark é mais rápido que o Mapreduce para análise no Hadoop.

Com esses princípios, as seções a seguir oferecem algumas regras gerais para selecionar um contexto de computação.

### <a name="local"></a>Local

- Se a quantidade de dados a ser analisada for pequena e não exigir análise repetida, transmita-a diretamente para a rotina de análise usando *local* ou *localpar*.
- Se a quantidade de dados a ser analisada for de pequeno ou médio porte e exigir a repetição da análise, copie-a para o sistema de arquivos local, importe-a para XDF e analise-a por meio de *local* ou *localpar*.

### <a name="apache-spark"></a>Apache Spark

- Se a quantidade de dados a serem analisados for grande, importe-os para um Spark DataFrame usando **RxHiveData** ou **RxParquetData** ou para o XDF no HDFS (exceto se o armazenamento for um problema) e analise-os usando o contexto de computação do Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduce

- Use o contexto de computação de redução de mapa somente se você vir um problema insuperável com o contexto de computação do Spark, pois ele é geralmente mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda embutida em rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação de RevoScaleR, confira a ajuda embutida sobre R no método rxSetComputeContext, por exemplo:

```console
> ?rxSetComputeContext
```

Você também pode consultar a [visão geral da computação distribuída](/machine-learning-server/r/how-to-revoscaler-distributed-computing) na [documentação do Machine Learning Server](/machine-learning-server/).

## <a name="next-steps"></a>Próximas etapas

Neste artigo você aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada entre núcleos do nó de borda ou o cluster HDInsight. Para saber mais sobre como usar os ML Services em clusters HDInsight, consulte os seguintes tópicos:

- [Visão geral dos serviços ML para o Apache Hadoop](r-server-overview.md)
- [Opções de Armazenamento do Microsoft Azure para ML Services no HDInsight](r-server-storage.md)