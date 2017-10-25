---
title: Diretrizes de ajuste de desempenho para Hive do Azure Data Lake Store | Microsoft Docs
description: Diretrizes de ajuste de desempenho para Hive do Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: e10bf8f7cbae2b81d22823ff74fe652c6bcb2da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Diretrizes de ajuste do desempenho para Hive no HDInsight e Azure Data Lake Store

As configurações padrão foram definidas para fornecer bom desempenho em muitos casos de uso diferentes.  Em consultas que usam muita E/S, o Hive pode ser ajustado para obter melhor desempenho com o ADLS.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Execução do Hive no HDInsight**.  Para saber mais sobre como executar trabalhos do Hive no HDInsight, confira [Usar o Hive no HDInsight] (https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes de ajuste de desempenho no ADLS**.  Para ver os conceitos gerais de desempenho, confira [Diretrizes de ajuste de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>parâmetros

Veja a seguir as configurações mais importantes a serem ajustadas para obter desempenho aprimorado do ADLS:

* **hive.tez.container.size** – a quantidade de memória usada por cada uma das tarefas

* **tez.grouping.min-size** – tamanho mínimo de cada mapeador

* **tez.grouping.max-size** – tamanho máximo de cada mapeador

* **hive.exec.reducer.bytes.per.reducer** – tamanho de cada redutor

**hive.tez.container.size** — o tamanho do contêiner determina quanto de memória está disponível para cada tarefa.  Essa é a entrada principal para controlar a simultaneidade no Hive.  

**tez.grouping.min-size** – esse parâmetro permite definir o tamanho mínimo de cada mapeador.  Se o número de mapeadores que o Tez escolher for menor que o valor desse parâmetro, o Tez usará o valor definido aqui.  

**tez.grouping.max-size** – o parâmetro permite definir o tamanho máximo de cada mapeador.  Se o número de mapeadores que o Tez escolher for maior que o valor desse parâmetro, o Tez usará o valor definido aqui.  

**hive.exec.reducer.bytes.per.reducer** – esse parâmetro define o tamanho de cada redutor.  Por padrão, o tamanho de cada redutor é de 256 MB.  

## <a name="guidance"></a>Diretrizes

**Definir hive.exec.reducer.bytes.per.reducer** – o valor padrão funciona bem quando os dados são descompactados.  Para dados compactados, você deve reduzir o tamanho do redutor.  

**Definir hive.tez.container.size** – em cada nó, a memória é especificada por yarn.nodemanager.resource.memory-mb e deve ser corretamente definida no cluster HDI por padrão.  Para obter informações adicionais sobre como definir a memória adequada no YARN, confira esta [postagem](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

As cargas de trabalho que usam muita E/S podem se beneficiar de mais paralelismo reduzindo o tamanho do contêiner do Tez. Isso proporciona ao usuário mais contêineres, o que aumenta a simultaneidade.  No entanto, algumas consultas do Hive exigem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, você receberá uma exceção de falta de memória durante o tempo de execução.  Se você receber sem exceções de falta de memória, será preciso aumentá-la.   

O número simultâneo de tarefas em execução ou de paralelismo será limitado pela memória YARN total.  O número de contêineres YARN ditará quantas tarefas simultâneas podem ser executadas.  Para localizar a memória YARN por nó, você pode ir para o Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nessa janela.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A chave para melhorar o desempenho usando o ADLS é aumentar a simultaneidade tanto quanto possível.  O Tez calcula automaticamente o número de tarefas que devem ser criadas, de modo que você não precisa defini-lo.   

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster D14 de 8 nós.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitações
**Limitação do ADLS** 

Se os limites de largura de banda fornecidos pelo ADLS fossem atingidos, você começaria a verificar as falhas de tarefa. Isso poderia ser identificado observando os erros de limitação nos logs de tarefa.  Você pode reduzir o paralelismo aumentando o tamanho do contêiner Tez.  Se precisar de mais simultaneidade para seu trabalho, entre em contato conosco.   

Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Coloque a propriedade a seguir nas propriedades de log4j na configuração do Hive. Isso pode ser feito no modo de exibição do Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Reinicie todos os nós/serviços para que a configuração entre em vigor.

2. Se o problema for de limitação, você verá o código de erro HTTP 429 no arquivo de log do Hive. O arquivo de log do Hive está em /tmp/&lt;usuário&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre o ajuste do Hive

Veja a seguir alguns blogs que ajudarão a ajustar as consultas do Hive:
* [Otimizar consultas do Hive para Hadoop no HDInsight](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Solução de problemas de desempenho em consultas do Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Debate sobre como otimizar o Hive no HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
