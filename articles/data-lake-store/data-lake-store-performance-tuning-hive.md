---
title: Ajuste de desempenho-Hive em Azure Data Lake Storage Gen1
description: Saiba mais sobre o ajuste de desempenho do hive no HdInsight e Azure Data Lake Storage Gen1. Para consultas com uso intensivo de e/s, ajuste o hive para obter um melhor desempenho.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723774"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Diretrizes de ajuste de desempenho para o Hive no HDInsight e Azure Data Lake Storage Gen1

As configurações padrão foram definidas para fornecer bom desempenho em muitos casos de uso diferentes.  Para consultas intensivas de E/S, o Hive pode ser ajustado para obter um melhor desempenho com Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Execução do Hive no HDInsight**.  Para saber mais sobre como executar trabalhos de Hive no HDInsight, confira [Usar Hive no HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen1**.  Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen1](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes para melhorar o desempenho do Data Lake Storage Gen1:

* **hive.tez.container.size** – a quantidade de memória usada por cada uma das tarefas

* **tez.grouping.min-size** – tamanho mínimo de cada mapeador

* **tez.grouping.max-size** – tamanho máximo de cada mapeador

* **hive.exec.reducer.bytes.per.reducer** – tamanho de cada redutor

**hive.tez.container.size** — o tamanho do contêiner determina quanto de memória está disponível para cada tarefa.  Essa é a entrada principal para controlar a simultaneidade no Hive.  

**tez.grouping.min-size** – esse parâmetro permite definir o tamanho mínimo de cada mapeador.  Se o número de mapeadores que o Tez escolher for menor que o valor desse parâmetro, o Tez usará o valor definido aqui.

**tez.grouping.max-size** – o parâmetro permite definir o tamanho máximo de cada mapeador.  Se o número de mapeadores que o Tez escolher for maior que o valor desse parâmetro, o Tez usará o valor definido aqui.

**hive.exec.reducer.bytes.per.reducer** – esse parâmetro define o tamanho de cada redutor.  Por padrão, o tamanho de cada redutor é de 256 MB.  

## <a name="guidance"></a>Orientação

**Definir hive.exec.reducer.bytes.per.reducer** – o valor padrão funciona bem quando os dados são descompactados.  Para dados compactados, você deve reduzir o tamanho do redutor.  

**Definir hive.tez.container.size** – em cada nó, a memória é especificada por yarn.nodemanager.resource.memory-mb e deve ser corretamente definida no cluster HDI por padrão.  Para obter informações adicionais sobre como definir a memória adequada no YARN, confira esta [postagem](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

As cargas de trabalho que usam muita E/S podem se beneficiar de mais paralelismo reduzindo o tamanho do contêiner do Tez. Isso proporciona ao usuário mais contêineres, o que aumenta a simultaneidade.  No entanto, algumas consultas do Hive exigem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, você receberá uma exceção de falta de memória durante o runtime.  Se você receber sem exceções de falta de memória, será preciso aumentá-la.   

O número simultâneo de tarefas em execução ou de paralelismo será limitado pela memória YARN total.  O número de contêineres YARN ditará quantas tarefas simultâneas podem ser executadas.  Para localizar a memória YARN por nó, você pode ir para o Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nesta janela.  

> Memória total de YARN = nós * YARN de memória por número de nó de contêineres de YARN = tamanho de contêiner de memória total YARN/tez

A chave para melhorar o desempenho usando o Data Lake Storage Gen1 é aumentar a simultaneidade o máximo possível.  O Tez calcula automaticamente o número de tarefas que devem ser criadas, de modo que você não precisa defini-lo.   

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster D14 de 8 nós.  

> Memória total do YARN = nós * YARN memória por nó total de YARN memória = 8 nós * 96 GB = 768GB número de YARN contêineres = 768GB/3072MB = 256

## <a name="limitations"></a>Limitações

**Limitação do data Lake armazenamento Gen1** 

Se você atingir os limites de largura de banda fornecidos pelo Data Lake Storage Gen1, começará a ver falhas de tarefas. Isso poderia ser identificado observando os erros de limitação nos logs de tarefa.  Você pode reduzir o paralelismo aumentando o tamanho do contêiner Tez.  Se precisar de mais simultaneidade para seu trabalho, entre em contato conosco.

Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Coloque a seguinte propriedade nas propriedades Log4J na configuração do hive. Isso pode ser feito na exibição Ambari: Log4J. Logger. com. Microsoft. Azure. datalake. Store = DEBUG reinicie todos os nós/serviços para que a configuração entre em vigor.

2. Se o problema for de limitação, você verá o código de erro HTTP 429 no arquivo de log do Hive. O arquivo de log do Hive está em /tmp/&lt;usuário&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre o ajuste do Hive

Veja a seguir alguns blogs que ajudarão a ajustar as consultas do Hive:
* [Otimizar consultas do Hive para Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Codificando o arquivo de consulta do hive no Azure HDInsight](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Debate sobre como otimizar o Hive no HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)