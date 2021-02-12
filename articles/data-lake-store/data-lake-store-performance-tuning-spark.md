---
title: Ajuste de desempenho-Spark com Azure Data Lake Storage Gen1
description: Saiba mais sobre as diretrizes de ajuste de desempenho para o Spark no Azure HDInsight e Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722497"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Diretrizes de ajuste de desempenho para Spark no HDInsight e Azure Data Lake Storage Gen1

Ao realizar o ajuste de desempenho no Spark, você deve considerar o número de aplicativos estarão em execução no cluster. Por padrão, você pode executar quatro aplicativos simultaneamente em seu cluster HDI (Observação: a configuração padrão está sujeita a alterações). Você pode decidir usar menos aplicativos, de modo que você possa substituir as configurações padrão e usar mais do cluster para esses aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Executar o cluster Spark no Azure Data Lake Storage Gen1**. Para obter mais informações, consulte [Usar cluster HDInsight do Spark para analisar dados no Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen1**. Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen1](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parâmetros

Ao executar os trabalhos do Spark, aqui estão as configurações mais importantes que podem ser ajustadas para aumentar o desempenho no Data Lake Storage Gen1:

* **Num-executors** – O número de tarefas simultâneas que podem ser executadas.

* **Executor-memory** – A quantidade de memória alocada para cada executor.

* **Executor-cores** – O número de núcleos alocados para cada executor.

**Num-executors** – Num-executors definirá o número máximo de tarefas que podem ser executadas em paralelo. O número real de tarefas que podem ser executadas em paralelo é limitado pela memória e recursos de CPU disponíveis no cluster.

**Executor-memory** – Esta é a quantidade de memória sendo alocada para cada executor. A memória necessária para cada executor depende do trabalho. Para operações complexas, a memória deve ser maior. Para operações simples como leitura e gravação, os requisitos de memória serão menores. A quantidade de memória para cada executor pode ser exibida no Ambari. Em Ambari, navegue até Spark e exiba a guia **configurações** .

**Executor-cores** – Isso define a quantidade de núcleos usados por executor, o que determina o número de threads paralelos que podem ser executados por executor. Por exemplo, se executor-cores = 2, então cada executor pode executar duas tarefas paralelas no executor. O valor de executor-cores necessário dependerá do trabalho. Trabalhos com uso intensivo de E/S não exigem uma grande quantidade de memória por tarefa para que cada executor possa manipular mais tarefas paralelas.

Por padrão, dois núcleos YARN virtuais são definidos para cada núcleo físico ao executar o Spark no HDInsight. Esse número fornece um bom equilíbrio entre a simultaneidade e a quantidade de trocas de contexto de vários threads.

## <a name="guidance"></a>Orientação

Ao executar as cargas de trabalho analíticas do Spark para trabalhar com dados no Data Lake Storage Gen1, é recomendável usar a versão mais recente do HDInsight para obter o melhor desempenho com Data Lake Storage Gen1. Quando o trabalho faz uso mais intensivo de E/S, determinados parâmetros podem ser configurados para melhorar o desempenho. O Data Lake Storage Gen1 é uma plataforma de armazenamento altamente escalonável que pode manipular taxa de transferência. Se o trabalho consistir principalmente em leitura ou gravação, então, aumentar a simultaneidade para E/S de e para o Data Lake Storage Gen1 poderá aumentar o desempenho.

Existem algumas maneiras gerais de aumentar a simultaneidade para os trabalhos com uso intensivo de E/S.

**Etapa 1: determinar quantos aplicativos estão em execução no cluster** – você deve saber quantos aplicativos estão em execução no cluster, incluindo o atual. Os valores padrão para cada configuração do Spark assume que há quatro aplicativos em execução simultaneamente. Portanto, você terá apenas 25% do cluster disponível para cada aplicativo. Para obter um melhor desempenho, você pode substituir os padrões por meio da alteração do número de executores.

**Etapa 2: configurar executor-memory** – a primeira coisa a definir é o executor-memory. A memória dependerá do trabalho que você pretende executar. Você pode aumentar a simultaneidade ao alocar menos memória por executor. Se você vir exceções de memória insuficiente quando executar o trabalho, você deverá aumentar o valor desse parâmetro. Uma alternativa é de obter mais memória pelo uso de um cluster com maiores quantidades de memória ou pelo aumento do tamanho do cluster. Mais memória permitirá o uso de mais executores, o que significa mais simultaneidade.

**Etapa 3: definir executor-cores** – para cargas de trabalho com uso intensivo de E/S que não têm operações complexas, é recomendável iniciar com um número alto de núcleos de executor para aumentar o número de tarefas paralelas por executor. Configurar executor-cores para 4 é um bom começo.

```console
executor-cores = 4
```

Aumentar o número de núcleos de executor lhe dará mais paralelismo para que você pode experimentar com diferentes núcleos de executor. Para trabalhos que têm operações mais complexas, você deve reduzir o número de núcleos por executor. Se executor-cores for definido para um valor maior que 4, a coleta de lixo poderá tornar-se ineficiente e prejudicar o desempenho.

**Etapa 4: determinar a quantidade de memória YARN no cluster** – essas informações estão disponíveis no Ambari. Navegue até YARN e exiba a guia Configurações. A memória YARN é exibida nesta janela.
Observe que, enquanto você estiver na janela, também será possível ver o tamanho do contêiner YARN padrão. O tamanho do contêiner YARN é igual à memória por parâmetro do executor.

Memória total YARN = Nodes * YARN de memória por nó

**Etapa 5: calcular num-executors**

**Calcular a restrição de memória** – o parâmetro num-executors é restrito por memória ou por CPU. A restrição de memória é determinada pela quantidade de memória YARN disponível para seu aplicativo. Pegue a memória total do YARN e divida-a por executor-Memory. O ajuste de escala da restrição precisa ser realizado de acordo com número de aplicativos, então realizamos a divisão pelo número de aplicativos.

Restrição de memória = (total de memória/memória do executor YARN)/n º de aplicativos

**Calcular a restrição de CPU** – a restrição de CPU é calculada como o total de núcleos virtuais dividido pelo número de núcleos por executor. Há dois núcleos virtuais para cada núcleo físico. Semelhante ao que ocorre na restrição de memória, realizamos a divisão pelo número de aplicativos.

núcleos virtuais = (nós no cluster * n º de núcleos físicos no nó * 2) restrição de CPU = (total de núcleos virtuais/n º de núcleos por executor)/# de aplicativos

**Definir num-executors** – o parâmetro num-executors será determinado utilizando o mínimo da restrição de memória e da restrição de CPU. 

num-eXecutors = min (total de núcleos virtuais/n º de núcleos por executor, disponível YARN memória/executor-Memory) definir um número maior de executores núm não necessariamente aumenta o desempenho. Considere que adicionar mais executores adicionará sobrecarga extra para cada executor adicional, o que pode degradar o desempenho. Num-executors é limitado pelos recursos de cluster.

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster composto por 8 nós D4v2 que executa dois aplicativos, incluindo aquele que você vai executar.

**Etapa 1: determinar quantos aplicativos estão em execução no cluster** – você sabe que tem dois aplicativos no cluster, incluindo aquele que você vai executar.

**Etapa 2: definir executor-memory** – neste exemplo, determinamos que 6 GB de executor-memory será suficiente para trabalho com uso intensivo de E/S.

```console
executor-memory = 6GB
```

**Etapa 3: definir executor-cores** – como esse é um trabalho com uso intensivo de e/s, podemos definir o número de núcleos para cada executor como quatro. A definição de núcleos por executor para mais de quatro pode causar problemas de coleta de lixo.

```console
executor-cores = 4
```

**Etapa 4: determinar a quantidade de memória YARN no cluster** – navegamos até o Ambari para descobrir que cada D4v2 tem 25 GB de memória YARN. Como há oito nós, a memória YARN disponível é multiplicada por oito.

Memória total YARN = nós * memória YARN * por nó total de YARN de memória = 8 nós * 25 GB = 200 GB

**Etapa 5: calcular num-executors** – o parâmetro num-executors será determinado utilizando o mínimo da restrição de memória e da restrição de CPU divididos pelo número de aplicativos em execução no Spark.

**Calcular a restrição de memória** – a restrição de memória é calculada como a memória YARN total dividida pela memória por executor.

Restrição de memória = (memória total YARN memória/executor)/# de restrição de memória de aplicativos = (200 GB/6 GB)/2 restrição de memória = 16 (arredondado) **calcular restrição de CPU** -a restrição de CPU é calculada como o total de núcleos de yarn dividido pelo número de núcleos por executor.

Núcleos de YARN = nós no cluster * n º de núcleos por nó * 2 núcleos de YARN = 8 nós * 8 núcleos por "D14 * 2 = 128" restrição de CPU = (total de YARN núcleos/n º de núcleos por executor)/# da restrição de CPU de aplicativos = (128/4)/2 restrição de CPU = 16

**Definir num-executors**

num-eXecutors = min (restrição de memória, restrição de CPU) num-eXecutors = min (16, 16) num-executores = 16