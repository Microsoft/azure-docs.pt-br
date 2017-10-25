---
title: "Usar Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído | Microsoft Docs"
description: "Use Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído"
services: hdinsight
documentationcenter: 
author: xiaoyongzhu
manager: asadk
editor: cgronlun
tags: azure-portal
ms.assetid: 71dcd1ad-4cad-47ad-8a9d-dcb7fa3c2ff9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: xiaoyzhu
ms.openlocfilehash: 14b7808c9534bce3049422d6bce1e8914b2c2fbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Use Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído


## <a name="introduction"></a>Introdução

O aprendizado aprofundado está afetando tudo, desde os serviços de saúde até os transportes e a fabricação e muito mais. As empresas estão recorrendo ao aprendizado aprofundado para resolver problemas de disco rígidos, como [classificação da imagem](http://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [reconhecimento de fala](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), reconhecimento de objeto e tradução automática. 

Há [muitas estruturas populares](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), incluindo [MiMicrosoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), MXNet, Theano etc. O Caffe é uma das estruturas não simbólicas de rede neural mais famosas (obrigatória) e é amplamente usado em muitas áreas, incluindo a visão do computador. Além disso, o [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) combina o Caffe ao Apache Spark. Nesse caso, o aprendizado aprofundado pode ser facilmente usado em um cluster Hadoop existente, juntamente com pipelines Spark ETL, reduzindo a complexidade do sistema e a latência de aprendizado de ponta a ponta.

O [HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/) é a única oferta de Hadoop de nuvem totalmente gerenciada, que oferece clusters de análise de software livre otimizados para Spark, Hive, MapReduce, HBase, Storm, Kafka e R Server com suporte de SLA de 99,9%. Cada uma dessas tecnologias de Big Data e aplicativos de ISV são facilmente implantáveis como clusters gerenciados, com monitoramento e segurança de nível empresarial.

Alguns usuários perguntam como usar o aprendizado aprofundado no HDInsight, que é o produto Hadoop PaaS da Microsoft. Teremos mais para abordar no futuro, mas hoje queremos para resumir uma postagem de blog técnica sobre como usar o Caffe no HDInsight Spark.

Se tiver instalado o Caffe antes, você observará que a instalação dessa estrutura pode ser um pouco desafiadora. Nesse blog, primeiro ilustramos como instalar o [Caffe no Spark](https://github.com/yahoo/CaffeOnSpark) para um cluster HDInsight e usar a demonstração do MNIST interno para demonstrar como usar o Aprendizado Distribuído Aprofundado usando o HDInsight Spark nas CPUs.

Há quatro etapas principais para fazer com que ele funcione no HDInsight.

1. Instalar as dependências necessárias em todos os nós
2. Compilar Caffe no Spark para HDInsight no nó principal
3. Distribuir as bibliotecas necessárias para todos os nós de trabalho
4. Compor um modelo do Caffe e executá-lo de forma distribuída

Como HDInsight é uma solução de PaaS, ele oferece excelentes recursos de plataforma. Portanto, é muito fácil executar algumas tarefas. Um dos recursos que usamos intensamente nesta postagem de blog é chamado [Ação de Script](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Com ele, você pode executar comandos do shell para personalizar nós de cluster (nó principal, nó de trabalho ou nó de borda).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Etapa 1: instalar as dependências necessárias em todos os nós

Para começar, precisamos instalar as dependências necessárias. O site do Caffe e o [site do CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferecem um wiki muito útil para instalar as dependências para Spark no modo YARN (que é o modo para HDInsight Spark), mas precisamos adicionar algumas outras dependências para a plataforma HDInsight. Vamos usar a ação de script abaixo e executá-la em todos os nós principais e nós de trabalho. Esta ação de script levará cerca de 20 minutos, pois essas dependências também dependem de outros pacotes. Você deve colocá-lo em um local acessível ao cluster HDInsight, como um local do GitHub ou a conta de armazenamento de BLOBs padrão.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose we install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


Há duas etapas na ação de script acima. A primeira etapa é instalar todas as bibliotecas necessárias. Essas bibliotecas incluem as bibliotecas necessárias para compilar o Caffe (como gflags, glog) e executá-lo (como numpy). Estamos usando libatlas para otimização de CPU, mas você sempre pode seguir o wiki CaffeOnSpark sobre a instalação de outras bibliotecas de otimização, como MKL ou CUDA (para GPU).

A segunda etapa é baixar, compilar e instalar protobuf 2.5.0 para Caffe durante a execução. O Protobuf 2.5.0 [é necessário](https://github.com/yahoo/CaffeOnSpark/issues/87). No entanto, essa versão não está disponível como um pacote no Ubuntu 16. Portanto, precisamos compilá-la por meio do código-fonte. Também há alguns recursos na Internet sobre como compilá-lo, como [este](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html)

Para simplesmente começar, você pode executar esta ação de script em relação ao cluster para todos os nós de trabalho e nós principais (para HDInsight 3.5). Você pode executar as ações de script para um cluster em execução ou também pode executar as ações de script durante o tempo de provisionamento do cluster. Para obter mais detalhes sobre as ações de script, confira a documentação [aqui](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions)

![Ações de script para instalar dependências](./media/hdinsight-deep-learning-caffe-spark/Script-Action-1.png)


## <a name="step-2-build-caffe-on-spark-for-hdinsight-on-the-head-node"></a>Etapa 2: compilar o Caffe no Spark para HDInsight no nó principal

A segunda etapa é compilar o Caffe no nó principal e distribuir as bibliotecas compiladas para todos os nós de trabalho. Nesta etapa, você precisará executar [ssh no nó principal](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e, depois, seguir o [processo de compilação do CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). A seguir está o script que você pode usar para compilar o CaffeOnSpark com algumas etapas adicionais. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Talvez seja necessário fazer mais do que diz a documentação do CaffeOnSpark. As alterações são:
- Altere para CPU apenas e use libatlas para essa finalidade específica.
- Coloque os conjuntos de dados no armazenamento de BLOBs, que é um local compartilhado acessível a todos os nós de trabalho para uso posterior.
- Coloque as bibliotecas do Caffe compiladas no armazenamento de BLOBs. Posteriormente, você copiará essas bibliotecas para todos os nós usando ações de script para evitar tempo de compilação adicional.


### <a name="troubleshooting-an-ant-buildexception-has-occured-exec-returned-2"></a>Solução de problemas: ocorreu um Ant BuildException: exec retornou: 2

Ao tentar criar pela primeira vez o CaffeOnSpark, às vezes, ele indicará que

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Basta limpar o repositório de código com "make clean" e executar "make build" para resolver o problema, contanto que você tenha as dependências corretas.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Solução de problemas: tempo limite de conexão do repositório do Maven

Às vezes, o maven gera o erro de tempo limite de conexão, semelhante ao item abaixo:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Tudo estará correto após aguardar alguns minutos e tentar apenas recompilar o código. Pode ser que Maven limite de alguma forma o tráfego de determinado endereço IP.


### <a name="troubleshooting-test-failure-for-caffe"></a>Solução de problemas: falha de teste para Caffe

Você provavelmente receberá uma falha de teste ao fazer a verificação final para o CaffeOnSpark, semelhante ao item abaixo. Isso provavelmente está relacionado à codificação UTF-8, mas não deve afetar o uso do Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Etapa 3: distribuir as bibliotecas necessárias para todos os nós de trabalho

A próxima etapa é distribuir as bibliotecas (basicamente, as bibliotecas em CaffeOnSpark/caffe-público/distribuir/lib/e CaffeOnSpark/caffe-distri/distribute/lib/) para todos os nós. Na Etapa 2, colocamos essas bibliotecas no armazenamento de BLOBs. Nesta etapa, usaremos as ações de script para copiá-las para todos os nós principais e nós de trabalho.

Para fazer isso, basta executar uma ação de script conforme mostrado a seguir (você precisa apontar para o local certo específico para o cluster):

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Como na etapa 2, ele é colocado no armazenamento de BLOBS que é acessível a todos os nós; nesta etapa, simplesmente o copiamos para todos os nós.

## <a name="step-4-compose-a-caffe-model-and-run-it-distributely"></a>Etapa 4: compor um modelo do Caffe e executá-lo de forma distribuída

Depois de executar as etapas acima, Caffe já está instalado no nó principal e estamos prontos. A próxima etapa é escrever um modelo do Caffe. 

O Caffe está usando uma "arquitetura expressiva," em que, para compor um modelo, basta definir um arquivo de configuração, sem codificação (na maioria dos casos). Vamos conferir isso. 

O modelo que treinaremos hoje é um modelo de exemplo para treinamento de MNIST. O banco de dados MNIST de dígitos manuscritos tem um conjunto de treinamento de 60.000 exemplos e um conjunto de teste de 10.000 exemplos. É um subconjunto de um conjunto maior disponível do NIST. Os dígitos foram normalizados em termos de tamanho e centralizados em uma imagem de tamanho fixo. O CaffeOnSpark tem alguns scripts para baixar o conjunto de dados e convertê-lo em um formato correto.

O CaffeOnSpark fornece alguns exemplos de topologias de rede para treinamento de MNIST. Ele tem um bom design que divide a arquitetura de rede (a topologia da rede) e a otimização. Nesse caso, há dois arquivos necessários: 

o arquivo "Solver" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) é usado para supervisionar a otimização e gerar atualizações de parâmetro. Por exemplo, ele define se a CPU ou GPU será usada, qual é a dinâmica, quantas iterações haverá etc. Ela também define qual topologia de rede de neurônios o programa deve usar (que é o segundo arquivo de que precisamos). Para obter mais informações sobre o recurso Solver, confira a [documentação do Caffe](http://caffe.berkeleyvision.org/tutorial/solver.html).

Para este exemplo, como estamos usando a CPU em vez de GPU, devemos alterar a última linha:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Configuração do Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-1.png)

Você pode alterar outras linhas conforme necessário.

O segundo arquivo (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) define a aparência da rede de neurônios e o arquivo de entrada e saída relevante. Também precisamos atualizar o arquivo para refletir o local de dados de treinamento. Altere a seguinte parte em lenet_memory_train_test.prototxt (você precisa apontar para o local certo específico para seu cluster):

- altere "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" para "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- altere "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" para "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Configuração do Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-2.png)

Para obter mais informações sobre como definir a rede, confira a [documentação do Caffe no conjunto de dados MNIST](http://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Para os objetivos deste blog, usamos apenas este exemplo MNIST simples. Você deve executar o comando abaixo no nó principal:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Basicamente, distribui os arquivos necessários (lenet_memory_solver.prototxt e lenet_memory_train_test.prototxt) para cada contêiner YARN e também define o caminho relevante de cada driver/executor Spark para LD_LIBRARY_PATH, que é definido no trecho de código anterior e aponta para o local que tem bibliotecas CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

Como estamos usando o modo de cluster YARN, em que o driver Spark será agendado para um contêiner arbitrário (e um nó de trabalho arbitrário), você só verá no console uma saída semelhante a:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Se quiser saber o que aconteceu, geralmente você precisará obter o log do driver do Spark, que tem mais informações. Nesse caso, você precisa acessar a interface do usuário YARN para encontrar os logs YARN relevantes. Você pode obter a interface do usuário do YARN por meio desta URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![Interface do usuário do YARN](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-1.png)

Você pode conferir a quantidade de recursos alocada para este aplicativo específico. Você pode clicar no link "Agendador" e verá que, para esse aplicativo, existem nove contêineres em execução. Solicitamos que YARN forneça oito executores, e outro contêiner é para o processo de driver. 

![Agendador do YARN](./media/hdinsight-deep-learning-caffe-spark/YARN-Scheduler.png)

Convém verificar os logs de driver ou logs de contêiner, se houver falhas. Para logs de driver, você pode clicar na ID do aplicativo na interface do usuário do YARN e clicar no botão "Logs". Os logs de driver são gravados em stderr.

![IU DO YARN 2](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-2.png)

Por exemplo, você pode ver alguns do erro abaixo dos logs do driver, indicando que você aloca um número excessivo de executores.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Às vezes, o problema pode ocorrer em executores em vez de drivers. Nesse caso, você precisa verificar os logs de contêiner. Você sempre pode obter os logs do contêiner e depois obter o contêiner com falha. Por exemplo, você pode encontrar essa falha ao executar o Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

Nesse caso, você precisa obter a ID de contêiner com falha (no caso acima, é container_1485916338528_0008_05_000005). Em seguida, você precisa executar 

    yarn logs -containerId container_1485916338528_0008_03_000005

no nó principal. Depois de verificar a falha do contêiner, ela é causada pelo uso do modo de GPU (em que você deve usar o modo de CPU em vez disso) em lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Obtendo resultados

Como estamos alocando oito executores, e a topologia de rede é simples, só levará cerca de 30 minutos para executar o resultado. Na linha de comando, você pode ver que colocamos o modelo wasb:///mnist.model e colocamos os resultados em uma pasta chamada wasb:///mnist_features_result.

Você pode obter os resultados executando

    hadoop fs -cat hdfs:///mnist_features_result/*

e o resultado é semelhante a:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

O SampleID representa a ID do conjunto de dados MNIST e o rótulo é o número que identifica o modelo.


## <a name="conclusion"></a>Conclusão

Nesta documentação, você tentou instalar o CaffeOnSpark com a execução de um exemplo simples. O HDInsight é uma plataforma de computação distribuída de nuvem gerenciada completa e é o melhor lugar para executar cargas de trabalho de análise avançada e aprendizado de máquina em um conjunto de dados grande. Para o aprendizado distribuído, você pode usar o Caffe no HDInsight Spark para executar tarefas de aprendizado aprofundado.


## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

