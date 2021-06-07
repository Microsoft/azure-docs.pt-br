---
title: Definir as configurações do Spark - Azure HDInsight
description: Como exibir e definir configurações de Apache Spark para um cluster do Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 25ff8611cc988c9777b52a313cfd74d4ee0a638c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870289"
---
# <a name="configure-apache-spark-settings"></a>Definir configurações do Apache Spark

Um cluster HDInsight Spark inclui uma instalação da biblioteca Apache Spark.  Cada cluster HDInsight inclui parâmetros de configuração padrão para todos os seus serviços instalados, incluindo Spark.  Um aspecto importante do gerenciamento de um cluster Apache Hadoop HDInsight é o monitoramento da carga de trabalho, incluindo trabalhos do Spark. Para executar melhor os trabalhos do Spark, considere a configuração do cluster físico ao determinar a configuração lógica do cluster.

O cluster padrão do HDInsight Apache Spark inclui os seguintes nós: três nós do Apache ZooKeeper, dois nós principais e um ou mais nós do trabalhador:

:::image type="content" source="./media/apache-spark-settings/spark-hdinsight-arch.png" alt-text="Arquitetura do HDInsight Spark" border="false":::

O número de VMs e os tamanhos de VM para os nós em seu cluster HDInsight podem afetar a configuração do Spark. Valores de configuração do HDInsight não padrão geralmente requerem valores de configuração do Spark não padrão. Quando você cria um cluster HDInsight Spark, são mostrados tamanhos de VM sugeridos para cada um dos componentes. Atualmente os [tamanhos de VM do Linux com otimização de memória](../../virtual-machines/sizes-memory.md) do Azure são D12 v2 ou posterior.

## <a name="apache-spark-versions"></a>Versões Apache Spark

Use a versão recomendada do Spark para seu cluster.  O serviço HDInsight inclui várias versões do Spark e HDInsight em si.  Cada versão do Spark inclui um conjunto de configurações de cluster padrão.  

Quando você cria um novo cluster, aqui estão as versões atuais do Spark para escolher. Para ver a lista completa,  [componentes e versões do HDInsight](../hdinsight-component-versioning.md).

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

O exemplo mostrado acima substitui vários valores padrão para cinco parâmetros de configuração do Spark.  Esses valores são o codec de compactação, Apache Hadoop tamanho mínimo de divisão do MapReduce e tamanhos de bloco parquet. Além disso, os valores padrão da partição SQL reserva e dos tamanhos de arquivo abertos.  Essas alterações de configuração são escolhidas porque os dados e trabalhos associados (neste exemplo, dados de genoma) têm características específicas. Essas características serão melhores usando essas definições de configuração personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Exibir definições de configuração do cluster

Verifique os parâmetros de configuração atuais do cluster HDInsight antes de fazer a otimização de desempenho no cluster. Iniciar o dashboard de HDInsight do portal do Azure clicando no link **Dashboard** no painel de cluster Spark. Entre com o nome de usuário e a senha do administrador de cluster.

A interface do usuário da Web do Apache Ambari é exibida com um painel de métricas de uso de recursos de cluster de chave.  O painel do Ambari mostra a configuração de Apache Spark e outros serviços instalados. O painel inclui uma guia **histórico de configuração** , na qual você exibe informações sobre os serviços instalados, incluindo o Spark.

Para ver os valores de configuração para o Apache Spark, selecione **Histórico de Configuração**, em seguida, selecione **Spark2**.  Selecione a guia **configurações** e, em seguida, selecione o link `Spark` (ou `Spark2`, dependendo de sua versão) na lista de serviços.  Você verá uma lista de valores de configuração para o cluster:

:::image type="content" source="./media/apache-spark-settings/spark-configurations.png" alt-text="Configurações Spark" border="true":::

Para ver e alterar valores de configuração individuais do Spark, selecione qualquer link com "Spark" no título.  Configurações para Spark incluem os dois valores de configuração avançada e personalizada nestas categorias:

* Spark2-defaults personalizada
* Spark2-metrics-properties personalizada
* Spark2-defaults avançada
* Spark2-env avançada
* Spark2-hive-site-override avançada

Se você criar um conjunto não padrão de valores de configuração, seu histórico de atualização será visível.  Esse histórico de configuração pode ser útil para ver qual configuração não-padrão tem um desempenho ideal.

> [!NOTE]  
> Para ver, mas não alterar, definições de configurações de cluster Spark comuns, selecione a guia **Ambiente** na interface superior **interface do usuário de trabalho Spark**.

## <a name="configuring-spark-executors"></a>Configurando executores Spark

O diagrama a seguir mostra os objetos de chave Spark: o programa de driver e seu contexto Spark associado e o gerenciador de cluster e seus *n* nós de trabalho.  Cada nó de trabalho inclui um Executor, um cache, e *n* instâncias da tarefa.

:::image type="content" source="./media/apache-spark-settings/hdi-spark-architecture.png" alt-text="Objetos de cluster" border="false":::

Os trabalhos de Spark usam recursos de trabalho, particularmente, memória, portanto, é comum para ajustar os valores de configuração do Spark para executores de nó de trabalho.

Três parâmetros de chave que geralmente são ajustados para ajustar as configurações do Spark para melhorar os requisitos do aplicativo são `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Um Executor é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número de nós de trabalho e o tamanho do nó de trabalho determina o número de executores e os tamanhos de executor. Esses valores são armazenados em `spark-defaults.conf` nos nós de cabeçalho do cluster.  Você pode editar esses valores em um cluster em execução selecionando **Spark-defaults personalizados** na interface do usuário da Web do amAmbari.  Depois de fazer alterações, você será solicitado pela interface do usuário para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Esses três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também especificados para cada aplicativo individualmente.

Outra fonte de informações sobre os recursos usados pelos executores do Spark é a interface do usuário do aplicativo Spark.  Na interface do usuário, os  **executores** exibem exibições de resumo e detalhes da configuração e dos recursos consumidos.  Determine se os valores de executores devem ser alterados para todo o cluster ou para um conjunto específico de execuções de trabalho.

:::image type="content" source="./media/apache-spark-settings/apache-spark-executors.png" alt-text="Executores Spark" border="true":::

Ou você pode usar a API REST do amAmbari para verificar programaticamente as definições de configuração do cluster HDInsight e Spark.  Mais informações estão disponíveis na [Referência da API do Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da carga de trabalho do Spark, você poderá determinar que uma configuração Spark não padrão proporcione execuções de trabalho do Spark mais otimizadas.  Execute testes de comparação com cargas de trabalho de exemplo para validar quaisquer configurações não padrão de cluster.  Estes são alguns dos parâmetros comuns que você pode considerar ajustar:

|Parâmetro |Descrição|
|---|---|
|--num-executors|Define o número de executores.|
|--executor-cores|Define o número de núcleos para cada executor. É recomendável usar executores de tamanho médio, pois outros processos também consomem parte da memória disponível.|
|--executor-memory|Controla o tamanho da memória (tamanho do heap) de cada executor em Apache Hadoop YARN, e você precisará deixar alguma memória para a sobrecarga de execução.|

Aqui está um exemplo de dois nós de trabalho com valores de configuração diferentes:

:::image type="content" source="./media/apache-spark-settings/executor-configuration.png" alt-text="Duas configurações de nó" border="false":::

A lista a seguir mostra os principais parâmetros de memória de executor Spark.

|Parâmetro |Descrição|
|---|---|
|spark.executor.memory|Define a quantidade total de memória disponível para um executor.|
|Spark. Storage. memoryFraction| (padrão aproximadamente 60%) define a quantidade de memória disponível para o armazenamento RDDs persistentes.|
|Spark. ordem aleatória. memoryFraction| (padrão aproximadamente 20%) define a quantidade de memória reservada para ordem aleatória.|
|Spark. Storage. unrollFraction e Spark. Storage. safetyFraction|(total de ~ 30% da memória total)-esses valores são usados internamente pelo Spark e não devem ser alterados.|

O YARN controla a soma máxima da memória usada por todos os contêineres em cada nó do Spark. O diagrama a seguir mostra as relações por nó entre objetos de configuração YARN e objetos Spark.

:::image type="content" source="./media/apache-spark-settings/hdi-yarn-spark-memory.png" alt-text="Gerenciamento de memória YARN do Spark" border="false":::

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para um aplicativo em execução no Jupyter Notebook

Os clusters Spark no HDInsight incluem um número de componentes por padrão. Cada um desses componentes inclui valores de configuração padrão que podem ser substituídos conforme necessário.

|Componente |Descrição|
|---|---|
|Núcleo do Spark|Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e Apache Spark MLlib.|
|Anaconda|Um Gerenciador de pacotes do Python.|
|Apache Livy|A API REST do Apache Spark, usada para enviar trabalhos remotos para um cluster HDInsight Spark.|
|Notebooks Jupyter e notebooks Apache Zeppelin|Interface do usuário interativa baseada em navegador para interagir com o cluster Spark.|
|Driver ODBC|Conecta clusters Spark no HDInsight a ferramentas de business intelligence (BI), como Microsoft Power BI e tableau.|

Para aplicativos em execução no Jupyter Notebook, use o `%%configure` comando para fazer alterações de configuração no próprio bloco de anotações. Essas alterações de configuração serão aplicadas aos trabalhos Spark executados da sua instância do bloco de notas. Faça essas alterações no início do aplicativo antes de executar sua primeira célula de código. A configuração alterada é aplicada à sessão Livy, quando ela é criada.

> [!NOTE]  
> Para alterar a configuração em uma fase posterior no aplicativo, use o parâmetro `-f` (força). No entanto, todo o progresso do aplicativo será perdido.

O código a seguir mostra como alterar a configuração de um aplicativo em execução em um Jupyter Notebook.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Monitore as definições de configuração principais para garantir que seus trabalhos do Spark sejam executados de forma previsível e de alto desempenho. Essas configurações ajudam a determinar a melhor configuração de cluster do Spark para suas cargas de trabalho específicas.  Você também precisará monitorar a execução de execuções de trabalho do Spark de execução demorada e de consumo de recursos.  Os desafios mais comuns são o centro em relação à pressão de memória de configurações inadequadas, como executores de tamanho incorreto. Além disso, operações de execução longa e tarefas, que resultam em operações cartesianas.

## <a name="next-steps"></a>Próximas etapas

* [Componentes e versões do Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configuração de Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Executando o Apache Spark no Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
