---
title: "Opções de contexto de computação para o R Server no HDInsight – Azure | Microsoft Docs"
description: "Conheça as diferentes opções de contexto de computação disponíveis para usuários com o Servidor R no HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 47f4441612be4f363ba82cc22b09786a6f3bfdc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Opções de contexto de computação para o Servidor R no HDInsight

O Microsoft R Server no Azure HDInsight controla como as chamadas são executadas, configurando o contexto de computação. Este artigo descreve as opções que estão disponíveis para especificar se e como a execução é paralelizada entre núcleos do nó de borda ou o cluster HDInsight.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também pode executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR ou os contextos de computação do Spark.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R Server no Azure HDInsight
O [Microsoft R Server no Azure HDInsight](hdinsight-hadoop-r-server-overview.md) fornece os recursos mais recentes para a análise baseada em R. Ele pode usar dados armazenados em um contêiner HDFS em sua conta de armazenamento de [Blob do Azure](../storage/common/storage-introduction.md "Armazenamento de Blobs do Azure"), em um Data Lake Store ou no sistema de arquivos local do Linux. Uma vez que o R Server é criado no R de software livre, os aplicativos baseados em R que você compilar podem aplicar qualquer um dos mais de 8.000 pacotes de R de software livre. Eles também podem usar as rotinas no [RevoScaleR](https://msdn.microsoft.com/microsoft-r/scaler/scaler), o pacote de análise de Big Data da Microsoft que está incluído no R Server.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de computação para um nó de extremidade
Em geral, um script de R que é executado no Servidor R no nó de borda é executado no interpretador de R nesse nó. As exceções são aquelas etapas que chamam uma função ScaleR. As chamadas de ScaleR são executadas em um ambiente de computação que é determinado pela configuração do contexto de computação do ScaleR.  Quando você executa o script R de um nó de borda, os valores possíveis de contexto de computação são:

- local sequencial (*‘local’*)
- local paralelo (*‘localpar’*)
- Map Reduce
- Spark

As opções *‘local’* e *‘localpar’* diferem apenas em como chamadas do **rxExec** são executadas. As duas executam outras chamadas de função rx de forma paralela entre os núcleos disponíveis, a menos que seja especificado de outra forma por meio do uso da opção **numCoresToUse** de ScaleR, por exemplo, `rxOptions(numCoresToUse=6)`. Opções de execução paralela oferecem um desempenho ideal.

A tabela a seguir resume as várias opções de contexto de computação para definir como as chamadas são executadas:

| Contexto de computação  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequencial | rxSetComputeContext(‘local’)    | Execução em paralelo entre os núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Local paralelo   | rxSetComputeContext(‘localpar’) | Execução paralela entre os núcleos do servidor de nó de borda |
| Spark            | RxSpark()                       | Execução distribuída em paralelo por meio do Spark em todos os nós do cluster do HDI |
| Map Reduce       | RxHadoopMR()                    | Execução distribuída em paralelo por meio do Map Reduce em todos os nós do cluster do HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir em um contexto de computação

Qual das três opções que fornecem execução em paralelo você ddeve escolher depende da natureza de seu trabalho de análise, do tamanho e do local dos seus dados. Não há uma fórmula simples que diga a você qual contexto de computação usar. No entanto, há alguns princípios importantes que podem ajudar você a fazer a escolha certa ou, pelo menos, ajudar a refinar suas escolhas antes de executar um benchmark. Esses princípios básicos incluem:

- O sistema de arquivos Linux local é mais rápido do que o HDFS.
- As análises repetidas serão mais rápidas se os dados forem locais e em XDF.
- É preferível transmitir pequenas quantidades de dados de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-os em XDF antes da análise.
- A sobrecarga da cópia ou transmissão dos dados para o nó de borda para análise se torna incontrolável para grandes quantidades de dados.
- Spark é mais rápido do que Map Reduce para análise no Hadoop.

Com esses princípios, as seções a seguir oferecem algumas regras gerais para selecionar um contexto de computação.

### <a name="local"></a>Local
* Se a quantidade de dados a ser analisada for pequena e não demandar análise repetida, transmita-a diretamente para a rotina de análise usando *'local'* ou *'localpar'*.
* Se a quantidade de dados a ser analisada for de pequeno ou médio porte e necessitar de análise repetida, copie-a para o sistema de arquivos local, importe-a para XDF e analise-a por meio de *'local'* ou *'localpar'*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Se a quantidade de dados a serem analisados for grande, importe-os para um Spark DataFrame usando **RxHiveData** ou **RxParquetData** ou para o XDF no HDFS (exceto se o armazenamento for um problema) e analise-os usando o contexto de computação do Spark.

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* Use o contexto de computação do Map Reduce somente se você encontrar um problema intransponível com o contexto de computação do Spark, pois geralmente ele é mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda embutida em rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação de ScaleR, confira a ajuda embutida sobre R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Você também pode consultar o “[Guia de computação distribuída do ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)” disponível na biblioteca [MSDN do R Server](https://msdn.microsoft.com/library/mt674634.aspx "R Server no MSDN").

## <a name="next-steps"></a>Próximas etapas
Neste artigo você aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada entre núcleos do nó de borda ou o cluster HDInsight. Para aprender mais sobre como usar o R Server com clusters HDInsight, veja os tópicos a seguir:

* [Visão geral do Servidor R no Hadoop](hdinsight-hadoop-r-server-overview.md)
* [Introdução ao Servidor R para o Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Adicionar Servidor do RStudio ao HDInsight (se não instalado durante a criação de cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight](hdinsight-hadoop-r-server-storage.md)

