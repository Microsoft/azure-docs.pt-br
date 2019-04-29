---
title: Definir as configurações do Spark - Azure HDInsight
description: Como configurar o Spark para um cluster Azure HDInsight.
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 77f4ec9cce5d02ea4cbcc4968d02773a13edfe5b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098365"
---
# <a name="configure-apache-spark-settings"></a>Definir configurações do Apache Spark

Um cluster HDInsight Spark inclui uma instalação da biblioteca [Apache Spark](https://spark.apache.org/).  Cada cluster HDInsight inclui parâmetros de configuração padrão para todos os seus serviços instalados, incluindo Spark.  Um aspecto importante do gerenciamento de um cluster do HDInsight Apache Hadoop é monitorar a carga de trabalho, incluindo o Spark Jobs, para garantir que as tarefas estejam sendo executadas de maneira previsível. Para executar melhor trabalhos Spark, considere a configuração de cluster físico ao determinar como otimizar a configuração lógica do cluster.

O cluster padrão do HDInsight Apache Spark inclui os seguintes nós: três nós do [Apache ZooKeeper](https://zookeeper.apache.org/), dois nós principais e um ou mais nós do trabalhador:

![Arquitetura do HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

O número de VMs e os tamanhos VM para os nós no cluster HDInsight também podem afetar sua configuração Spark. Valores de configuração do HDInsight não padrão geralmente requerem valores de configuração do Spark não padrão. Quando você cria um cluster HDInsight Spark, serão exibidas tamanhos de VM sugeridos para cada um dos componentes. Atualmente os [tamanhos de VM do Linux com otimização de memória](../../virtual-machines/linux/sizes-memory.md) do Azure são D12 v2 ou posterior.

## <a name="apache-spark-versions"></a>Versões Apache Spark

Use a versão recomendada do Spark para seu cluster.  O serviço HDInsight inclui várias versões do Spark e HDInsight em si.  Cada versão do Spark inclui um conjunto de configurações de cluster padrão.  

Quando você cria um novo cluster, aqui estão as versões atuais do Spark para escolher. Para ver a lista completa, [versões e componentes do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> A versão padrão de Apache Spark para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tem uma dependência de versão, a Microsoft recomenda que você especifique essa versão específica ao criar clusters usando o SDK do .NET, o Azure PowerShell e a CLI Clássica do Azure.

Apache Spark tem três locais de configuração do sistema:

* Propriedades do Spark controla a maioria dos parâmetros do aplicativo e pode ser definida usando um `SparkConf` objeto, ou por meio de propriedades do sistema Java.
* Variáveis de ambiente podem ser usadas para definir configurações por computador, como o endereço IP, por meio de `conf/spark-env.sh` script em cada nó.
* Registro em log pode ser configurado por meio de `log4j.properties`.

Quando você seleciona uma versão específica do Spark, seu cluster inclui as definições de configuração padrão.  Você pode alterar os valores de configuração do Spark padrão usando um arquivo de configuração personalizado do Spark.  Um exemplo é mostrado abaixo.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

O exemplo mostrado acima substitui vários valores padrão para cinco parâmetros de configuração do Spark.  Esses são o codec de compactação, o tamanho mínimo de divisão do Apache Hadoop MapReduce e os tamanhos de blocos de parquete, além dos valores padrão da partição Spar SQL e do tamanho de arquivo aberto.  Essas alterações de configuração são escolhidas porque os trabalhos e dados associados (por exemplo, dados de genoma) têm características específicas que executarão melhor usando essas configurações personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Exibir definições de configuração do cluster

Verifique as definições de configuração de cluster HDInsight atuais antes de executar a otimização de desempenho no cluster. Iniciar o dashboard de HDInsight do portal do Azure clicando no link **Dashboard** no painel de cluster Spark. Faça logon com o nome de usuário e a senha do administrador do cluster.

A interface da Web do Apache Ambari aparece, com uma visualização do painel de métricas de utilização de recursos do cluster principal.  O painel do Ambari mostra a configuração do Apache Spark e outros serviços que você instalou. O painel inclui uma guia do **histórico de configuração**, onde você pode exibir informações de configuração para todos os serviços instalados, incluindo o Spark.

Para ver os valores de configuração para o Apache Spark, selecione **Histórico de Configuração**, em seguida, selecione **Spark2**.  Selecione a guia **configurações** e, em seguida, selecione o link `Spark` (ou `Spark2`, dependendo de sua versão) na lista de serviços.  Você verá uma lista de valores de configuração para o cluster:

![Configurações Spark](./media/apache-spark-settings/spark-config.png)

Para ver e alterar valores de configuração Spark individuais, selecione qualquer link com a palavra "spark" no título do link.  Configurações para Spark incluem os dois valores de configuração avançada e personalizada nestas categorias:

* Spark2-defaults personalizada
* Spark2-metrics-properties personalizada
* Spark2-defaults avançada
* Spark2-env avançada
* Spark2-hive-site-override avançada

Se você criar um conjunto de valores de configuração não-padrão, você também poderá ver o histórico de atualizações de sua configuração.  Esse histórico de configuração pode ser útil para ver qual configuração não-padrão tem um desempenho ideal.

> [!NOTE]  
> Para ver, mas não alterar, definições de configurações de cluster Spark comuns, selecione a guia **Ambiente** na interface superior **interface do usuário de trabalho Spark**.

## <a name="configuring-spark-executors"></a>Configurando executores Spark

O diagrama a seguir mostra os objetos de chave Spark: o programa de driver e seu contexto Spark associado e o gerenciador de cluster e seus *n* nós de trabalho.  Cada nó de trabalho inclui um Executor, um cache, e *n* instâncias da tarefa.

![Objetos de cluster](./media/apache-spark-settings/spark-arch.png)

Os trabalhos de Spark usam recursos de trabalho, particularmente, memória, portanto, é comum para ajustar os valores de configuração do Spark para executores de nó de trabalho.

Três parâmetros de chave que geralmente são ajustados para ajustar as configurações do Spark para melhorar os requisitos do aplicativo são `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Um Executor é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. Para cada cluster, o número padrão de executores e os tamanhos do executor são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Eles são armazenados em `spark-defaults.conf` nos nós do cabeçalho do cluster.  Você pode editar esses valores em um cluster em execução, selecionando o link **spark-defaults personalizado** da interface do usuário do Ambari web.  Depois de fazer alterações, você será solicitado pela interface do usuário para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Esses três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também especificados para cada aplicativo individualmente.

Outra fonte de informações sobre os recursos que estão sendo usados pelos executores Spark é a interface do usuário do aplicativo Spark.  Na IU do Spark, selecione a guia **Executores** para exibir os modos de exibição de resumo e detalhes da configuração e recursos consumidos pelos executores.  Esses modos de exibição podem ajudá-lo a determinar se alterar os valores padrão para executores Spark para todo o cluster ou um conjunto específico de execuções de trabalho.

![Executores Spark](./media/apache-spark-settings/spark-executors.png)

Como alternativa, você pode usar a API REST do Ambari para verificar periodicamente as configurações de cluster HDInsight e o Spark.  Mais informações estão disponíveis na [Referência da API do Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da carga de trabalho do Spark, você poderá determinar que uma configuração Spark não padrão proporcione execuções de trabalho do Spark mais otimizadas.  Você deve executar testes de benchmark com cargas de trabalho de exemplo para validar quaisquer configurações de cluster não padrão.  Estes são alguns dos parâmetros comuns que você pode considerar ajustar:

* `--num-executors` define o número de executores.
* `--executor-cores` define o número de núcleos para cada executor. É recomendável usar executores de tamanho médio, pois outros processos também consomem parte da memória disponível.
* `--executor-memory` controla o tamanho da memória (tamanho de heap) de cada executor no [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), e você precisará deixar alguma memória para a sobrecarga de execução.

Aqui está um exemplo de dois nós de trabalho com valores de configuração diferentes:

![Duas configurações de nó](./media/apache-spark-settings/executor-config.png)

A lista a seguir mostra os principais parâmetros de memória de executor Spark.

* `spark.executor.memory` define a quantidade total de memória disponível para um executor.
* `spark.storage.memoryFraction` (padrão aproximadamente 60%) define a quantidade de memória disponível para o armazenamento RDDs persistentes.
* `spark.shuffle.memoryFraction` (padrão aproximadamente 20%) define a quantidade de memória reservada para ordem aleatória.
* `spark.storage.unrollFraction` e `spark.storage.safetyFraction` (totalizando aproximadamente 30% do total de memória) - esses valores são usados internamente pelo Spark e não devem ser alterados.

O YARN controla a soma máxima da memória usada por todos os contêineres em cada nó do Spark. O diagrama a seguir mostra as relações por nó entre objetos de configuração YARN e objetos Spark.

![Gerenciamento de memória YARN do Spark](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para um aplicativo em execução no bloco de notas do Jupyter

Os clusters Spark no HDInsight incluem um número de componentes por padrão. Cada um desses componentes inclui valores de configuração padrão que podem ser substituídos conforme necessário.

* Spark Core - Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e Apache Spark MLlib.
* Anaconda - gerenciador de pacote de um python.
* [Apache Livy](https://livy.incubator.apache.org/) - a API REST do Apache Spark, usada para enviar tarefas remotas para um cluster do HDInsight Spark.
* Notebooks [Jupyter](https://jupyter.org/) e [Apache Zeppelin](https://zeppelin.apache.org/) - interface do usuário interativa baseada em navegador para interagir com seu cluster do Spark.
* O driver ODBC - conecta clusters Spark no HDInsight para ferramentas de business intelligence (BI), como o Microsoft Power BI e Tableau.

Para aplicativos em execução no bloco de notas Jupyter, você pode usar o comando `%%configure` para fazer as alterações na configuração no próprio bloco de notas. Essas alterações de configuração serão aplicadas aos trabalhos Spark executados da sua instância do bloco de notas. Você deve fazer tais alterações no início do aplicativo, antes de executar a primeira célula de código. A configuração alterada é aplicada à sessão Livy, quando ela é criada.

> [!NOTE]  
> Para alterar a configuração em uma fase posterior no aplicativo, use o parâmetro `-f` (força). No entanto, todo o progresso do aplicativo será perdido.

O código abaixo mostra como alterar a configuração para um aplicativo em execução em um bloco de notas do Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Há um número de definições de configuração principais que você precisa monitorar e ajustar para garantir que seus trabalhos de Spark sejam executados de forma previsível e de alto desempenho. Essas configurações ajudam a determinar a melhor configuração de cluster do Spark para suas cargas de trabalho específicas.  Além disso, você precisará monitorar a execução de execuções de trabalhos de longa execução e/ou o consumo de recursos do Spark.  O desafio mais comum se concentra na demanda de memória, devido a configurações incorretas (particularmente, executores de tamanho errado), operações de execução longa e tarefas que resultam em operações Cartesianas.

## <a name="next-steps"></a>Próximas etapas

* [Componentes e versões do Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuração do Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Executando o Apache Spark no Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
