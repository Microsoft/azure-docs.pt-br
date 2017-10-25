---
title: "Usar o Apache Kafka com o Storm no HDInsight – Azure | Microsoft Docs"
description: "O Apache Kafka é instalado com o Apache Storm no HDInsight. Saiba como gravar no Kafka e depois ler dele, usando os componentes KafkaBolt e KafkaSpout fornecidos com o Storm. Saiba também como usar a estrutura Flux para definir e enviar topologias Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/21/2017
ms.author: larryfr
ms.openlocfilehash: e8895ef3c11aea48513e4060a20f5f49b11fc961
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Usar o Apache Kafka (visualização) com o Storm no HDInsight

Saiba como usar o Apache Storm para ler e gravar no Apache Kafka. Este exemplo também demonstra como salvar dados de uma topologia Storm no sistema de arquivos compatível com HDFS usado pelo HDInsight.

> [!NOTE]
> As etapas neste documento criam um grupo de recursos do Azure que contém um Storm no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Storm se comunique diretamente com o cluster Kafka.
> 
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="get-the-code"></a>Obter o código

O código do exemplo usado neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Para compilar esse projeto, você precisará da seguinte configuração para seu ambiente de desenvolvimento:

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. HDInsight 3.5 ou superior exige Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Um cliente SSH (você precisa dos comandos `ssh` e `scp`) — para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um editor de texto ou IDE.

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório em que o JDK está instalado.
* `PATH` – deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório em que o Maven está instalado.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer item que se comunique com o Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters Storm e Kafka estão localizados em uma rede virtual do Azure. O seguinte diagrama mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Storm e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Outros serviços no cluster, como o SSH e o Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, clusters Kafka e Storm manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, clusters Kafka e Storm para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v1.json**. Ele cria os seguintes recursos:
    
    * Grupo de recursos do Azure
    * Rede Virtual do Azure
    * Conta de Armazenamento do Azure
    * Kafka no HDInsight versão 3.6 (três nós de trabalho)
    * Storm no HDInsight versão 3.6 (três nós de trabalho)

  > [!WARNING]
  > Para garantir a disponibilidade do Kafka no HDInsight, o cluster deve conter pelo menos três nós de trabalho. Este modelo cria um cluster de Kafka que contém três nós de trabalho.

2. Use as seguintes diretrizes para preencher as entradas na folha de **implantação personalizada** :
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.
   
    * **Local**: escolha um local geograficamente perto de você.

    * **Nome do Cluster de base**: esse valor é usado como o nome de base para os clusters Storm e Kafka. Por exemplo, inserir **hdi** cria um cluster Storm chamado **storm-hdi** e um cluster Kafka chamado **kafka-hdi**.
   
    * **Nome de usuário de logon do cluster**: o nome de usuário do administrador para os clusters Storm e Kafka.
   
    * **Senha de logon do cluster**: a senha do administrador para os clusters Storm e Kafka.
    
    * **Nome de usuário SSH**: o usuário SSH para criar para os clusters Storm e Kafka.
    
    * **Senha SSH**: a senha para o usuário SSH dos clusters Kafka e Storm.

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Depois que os recursos forem criados, a folha do grupo de recursos será exibida.

![Folha do grupo de recursos para rede virtual e clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **storm-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="understanding-the-code"></a>Compreender o código

Este projeto contém duas topologias:

* **KafkaWriter**: definida pelo arquivo **writer.yaml**, essa topologia grava sentenças aleatórias no Kafka usando o KafkaBolt fornecido com o Apache Storm.

    Essa topologia usa um componente **SentenceSpout** personalizado para gerar sentenças aleatórias.

* **KafkaReader**: definida pelo arquivo **reader.yaml**, essa topologia lê dados do Kafka usando o KafkaSpout fornecido com o Apache Storm e registra os dados em stdout.

    Essa topologia usa o Storm HdfsBolt para gravar dados no armazenamento padrão do cluster Storm.
### <a name="flux"></a>Flux

As topologias são definidas usando o [Flux](https://storm.apache.org/releases/1.1.0/flux.html). O Flux foi introduzido no Storm 0.10.x e permite que você separe a configuração de topologia do código. Para topologias que usam a estrutura Flux, a topologia é definida em um arquivo YAML. O arquivo do YAML pode ser incluído como parte da topologia. Ele também pode ser um arquivo autônomo usado ao enviar a topologia. O Flux também dá suporte à substituição de variáveis em tempo de execução, o que é usado neste exemplo.

Os seguintes parâmetros são definidos em tempo de execução para essas topologias:

* `${kafka.topic}`: o nome do tópico do Kafka na qual as topologias leem/gravam.

* `${kafka.broker.hosts}`: os hosts no qual os agentes do Kafka são executados. As informações de agente são usadas pelo KafkaBolt ao gravar no Kafka.

* `${kafka.zookeeper.hosts}`: os hosts nos quais o Zookeeper é executado no cluster Kafka.

Para obter mais informações sobre as topologias do Flux, consulte [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="download-and-compile-the-project"></a>Baixar e compilar o projeto

1. No ambiente de desenvolvimento, baixe o projeto de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra uma linha de comando e altere os diretórios para o local em que você baixou o projeto.

2. No diretório **hdinsight-storm-java-kafka**, use o seguinte comando para compilar o projeto e criar um pacote para implantação:

  ```bash
  mvn clean package
  ```

    O processo de pacote cria um arquivo chamado `KafkaTopology-1.0-SNAPSHOT.jar` no `target` diretório.

3. Use os seguintes comandos para copiar o pacote para o cluster Storm no HDInsight. Substitua **NOMEDOUSUÁRIO** pelo nome de usuário SSH do cluster. Substitua **BASENAME** pelo nome base que você usou ao criar o cluster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Quando solicitado, digite a senha usada durante a criação dos clusters.

## <a name="configure-the-topology"></a>Configurar a topologia

1. Use um dos seguintes métodos para descobrir os hosts do agente do Kafka:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > O exemplo de Bash supõe que `$CLUSTERNAME` contenha o nome do cluster HDInsight. Ele também pressupõe que [jq](https://stedolan.github.io/jq/) esteja instalado. Quando solicitado, insira a senha para a conta de logon do cluster.

    O valor retornado é semelhante ao seguinte texto:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Embora possa haver mais de dois hosts de agente para seu cluster, não será preciso fornecer uma lista completa de todos os hosts aos clientes. Um ou dois são suficientes.

2. Use um dos seguintes métodos para descobrir os hosts do Kafka Zookeeper:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > O exemplo de Bash supõe que `$CLUSTERNAME` contenha o nome do cluster HDInsight. Ele também pressupõe que [jq](https://stedolan.github.io/jq/) esteja instalado. Quando solicitado, insira a senha para a conta de logon do cluster.

    O valor retornado é semelhante ao seguinte texto:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Embora haja mais de dois nós Zookeeper, você não precisa fornecer uma lista completa de todos os hosts aos clientes. Um ou dois são suficientes.

    Salve esse valor, pois ele é usado mais tarde.

3. Edite o arquivo `dev.properties` na raiz do projeto. Adicione as informações de hosts do Agente e do Zookeeper às linhas correspondentes nesse arquivo. O seguinte exemplo é configurado usando os valores de exemplo das etapas anteriores:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Salve o arquivo `dev.properties` e, em seguida, use o seguinte comando para carregá-lo no cluster Storm:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    Substitua **NOMEDOUSUÁRIO** pelo nome de usuário SSH do cluster. Substitua **BASENAME** pelo nome base que você usou ao criar o cluster.

## <a name="start-the-writer"></a>Iniciar o gravador

1. Use o seguinte para se conectar ao cluster Storm usando o SSH. Substitua**USERNAME** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **BASENAME** pelo nome base usado ao criar o cluster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Quando solicitado, digite a senha usada durante a criação dos clusters.
   
    Para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Na conexão SSH, use o seguinte comando para criar o tópico do Kafka usado pela topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Substitua `$KAFKAZKHOSTS` pelas informações de host do Zookeeper recuperadas na seção anterior.

2. Na conexão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de gravador:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Os parâmetros usados com esse comando são:

    * `org.apache.storm.flux.Flux`: use o Flux para configurar e executar essa topologia.

    * `--remote`: envie a topologia para o Nimbus. A topologia é distribuída entre os nós de trabalho no cluster.

    * `-R /writer.yaml`: use o arquivo `writer.yaml` para configurar a topologia. `-R` indica que esse recurso está incluído no arquivo jar. Está na raiz do jar, então `/writer.yaml` é o caminho para ele.

    * `--filter`: popule entradas na topologia `writer.yaml` usando os valores do arquivo `dev.properties`. Por exemplo, o valor da entrada `kafka.topic` no arquivo é usada para substituir a entrada `${kafka.topic}` na definição de topologia.

5. Depois que a topologia for iniciada, use o seguinte comando para verificar se ela está gravando os dados no tópico do Kafka:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Substitua `$KAFKAZKHOSTS` pelas informações de host do Zookeeper recuperadas na seção anterior.

    Esse comando usa um script fornecido com o Kafka para monitorar o tópico. Após alguns instantes, ele deve começar a retornar sentenças aleatórias que foram gravadas para o tópico. A saída deverá ser semelhante ao seguinte exemplo:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Use Ctrl + c para interromper o script.

## <a name="start-the-reader"></a>Iniciar o leitor

1. Na sessão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de leitor:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Quando a topologia for iniciada, abra a interface do usuário do Storm. Essa interface do usuário da Web está localizada em https://storm-BASENAME.azurehdinsight.net/stormui. Substitua __BASENAME__ pelo nome de base usado quando o cluster foi criado. 

    Quando solicitado, use o nome de logon de administrador (o padrão, `admin`) e a senha usados quando o cluster foi criado. Você verá uma página da Web semelhante à seguinte imagem:

    ![Interface do Usuário do Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Na interface do usuário do Storm, selecione o link __kafka-reader__ na seção __Resumo da Topologia__ para exibir informações sobre a topologia __kafka-reader__.

    ![Seção de resumo da topologia da interface do usuário da Web do Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Para exibir informações sobre as instâncias do componente logger-bolt, selecione o link __logger-bolt__ na seção __Bolts (Sempre)__.

    ![Link logger-bolt na seção bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Na seção __Executores__, selecione um link na coluna __Porta__ para exibir informações de log sobre essa instância do componente.

    ![Link de executores](./media/hdinsight-apache-storm-with-kafka/executors.png)

    O log contém um log dos dados lidos do tópico Kafka. As informações no log são semelhantes ao seguinte texto:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Interromper as topologias

Em uma sessão SSH ao cluster Storm, use os seguintes comandos para interromper as topologias Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. A exclusão do grupo de recursos remove todos os recursos criados com as instruções deste documento.

## <a name="next-steps"></a>Próximas etapas

Para obter mais topologias de exemplo que podem ser usadas com o Storm no HDInsight, confira [Topologias Storm de exemplo e componentes](hdinsight-storm-example-topology.md).

Para obter informações sobre como implantar e monitorar topologias no HDInsight baseado em Linux, confira [Implantar e gerenciar topologias Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)