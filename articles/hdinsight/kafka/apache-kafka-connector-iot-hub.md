---
title: Use o Apache Kafka no HDInsight com o Microsoft Azure Hub IoT
description: Saiba como usar o Apache Kafka no HDInsight com o Microsoft Azure Hub IoT. O projeto Hub IoT do Microsoft Azure do Kafka Connect fornece uma fonte e conector de coletor para o Kafka. O conector de origem pode ler dados de Hub IoT e o conector de coletor grava o Hub IoT.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 7980003dd63e5e51d87f85542029a1f25e7223df
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932868"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Use o Apache Kafka no HDInsight com o Microsoft Azure Hub IoT

Aprenda como usar o conector do [Hub IoT do Azure do Apache Kafka Connect](https://github.com/Azure/toketi-kafka-connect-iothub) para mover dados entre o Apache Kafka no HDInsight e o Azure IoT Hub. Neste documento, você aprenderá a executar o conector do Hub IoT de um nó de borda do cluster.

A API de conexão do Kafka permite que você implemente os conectores que continuamente extraem dados para Kafka ou enviam dados do Kafka para outro sistema. O [Hub Apache Kafka Connect do Azure IoT](https://github.com/Azure/toketi-kafka-connect-iothub) é um conector que extrai dados do Hub do Azure IoT para o Kafka. Também pode enviar dados do Kafka para o Hub IoT.

Quando efetuar pull do Hub IoT, você usa um __conector__ de fonte. Quando efetuar pull do Hub IoT, você usa um __conector__ de coletor. O conector de Hub IoT fornece a origem e os conectores de coletor.

O diagrama a seguir mostra o fluxo de dados entre o Hub IoT do Microsoft zure e Kafka no HDInsight ao usar o conector.

![Imagem mostrando os dados que fluem do Hub IoT para o Kafka através do conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para obter mais informações sobre a API de conexão, consulte [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Kafka no HDInsight. Para obter mais informações, consulte o documento [Início rápido do Kafka no HDInsight](apache-kafka-get-started.md).

* Um nó de borda do cluster Kafka. Para saber mais, consulte o documento [Usar nós de borda com o documento do Microsoft Azure HDInsight](../hdinsight-apps-use-edge-node.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um hub IoT do Azure e um dispositivo. Para este artigo, considere usar [conectar simulador online do Raspberry Pi ao Hub IOT do Azure](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md).

* [Ferramenta de criação escalar](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Criar o conector

1. Baixe a origem do conector do no [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) seu ambiente local.

2. Em um prompt de comando, navegue até o `toketi-kafka-connect-iothub-master` diretório. Em seguida, use o seguinte comando para compilar e empacotar o projeto:

    ```cmd
    sbt assembly
    ```

    A compilação levará alguns minutos para ser concluída. O comando cria um arquivo chamado `kafka-connect-iothub-assembly_2.11-0.7.0.jar` no `toketi-kafka-connect-iothub-master\target\scala-2.11` diretório para o projeto.

## <a name="install-the-connector"></a>Instalar o conector

1. Carregue o arquivo. jar no nó de borda do seu Kafka no cluster HDInsight. Edite o comando a seguir substituindo pelo `CLUSTERNAME` nome real do cluster. Os valores padrão para a conta de usuário SSH e o nome do [nó de borda](../hdinsight-apps-use-edge-node.md#access-an-edge-node) são usados abaixo, modifique conforme necessário.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Uma vez concluída a cópia do arquivo, conecte-se ao nó de borda usando SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para instalar o conector ao diretório do Kafka `libs`, use o seguinte comando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Mantenha a conexão SSH ativa para as etapas restantes.

## <a name="configure-apache-kafka"></a>Configure Apache Kafka

Em sua conexão SSH com o nó de borda, use as seguintes etapas para configurar o Kafka para executar o conector no modo autônomo:

1. Configurar variável de senha. Substitua senha pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Instale o utilitário [JQ](https://stedolan.github.io/jq/) . JQ torna mais fácil processar documentos JSON retornados de consultas Ambari. Digite o seguinte comando:

    ```bash
    sudo apt -y install jq
    ```

1. Coletar endereço IP do agente do Kafka. Pode haver muitos agentes no seu cluster, mas você só precisa fazer referência a um ou dois. Para obter o endereço dos dois hosts de agente, use o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Copie os valores para uso posterior. O valor retornado é semelhante ao seguinte texto:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Obter o endereço de nós de Apache Zookeeper. Há vários nós do Zookeeper no cluster, mas você só precisa referenciar um ou dois. Use o seguinte comando para armazenar os endereços na variável `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ao executar o conector no modo autônomo, o `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` arquivo é usado para se comunicar com os agentes de Kafka. Para editar esse arquivo `connect-standalone.properties`, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Faça as seguintes edições:

    |Valor atual |Novo valor | Comentário |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Substitua o `localhost:9092` valor pelos hosts do agente da etapa anterior|Define a configuração autônoma para o nó de borda para localizar os agentes Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Essa alteração permite que você teste usando o produtor do console incluído com Kafka. Talvez seja necessário conversores diferentes para outros produtores e consumidores. Para obter informações sobre como usar outros valores de conversor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Mesmo que acima.|
    |N/D|`consumer.max.poll.records=10`|Adicionar ao final do arquivo. Essa alteração é evitar os tempos limite no conector do coletor limitando a 10 registros por vez. Para obter mais informações, confira [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Para salvar o arquivo, use __Ctrl + X__, __Y__ e, em seguida, __Enter__.

1. Para criar os tópicos usados pela conector use os comandos a seguir:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para verificar que os tópicos `iotin` e `iotout` existem, use o comando a seguir:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    O tópico `iotin` é usado para receber mensagens de Hub IoT. O tópico `iotout` é usado para enviar mensagens de Hub IoT.

## <a name="get-iot-hub-connection-information"></a>Obter a cadeia de conexão do Hub IoT

Para recuperar informações de Hub IoT usadas pelo conector, use as seguintes etapas:

1. Obtenha o ponto de extremidade compatível com o evento de Hub e nome de ponto de extremidade compatível para o seu Hub IoT. Para obter essas informações, use um dos métodos a seguir:

   * __Do [portal do Azure](https://portal.azure.com/)__, use as etapas a seguir:

     1. Navegue até seu Hub IoT e clique em __Pontos de Extremidade__.
     2. Em __Pontos de extremidade internos__, selecione __Eventos__.
     3. De __Propriedades__, copie o valor dos campos a seguir:

         * __Nome compatível com o Hub de eventos__
         * __Ponto de extremidade compatível com o Hub de eventos__
         * __Partições__

        > [!IMPORTANT]  
        > O valor de ponto de extremidade do portal pode conter texto extra que não é necessário neste exemplo. Extrair o texto que corresponde a esse padrão `sb://<randomnamespace>.servicebus.windows.net/`.

   * __Na [CLI do Azure](/cli/azure/get-started-with-azure-cli)__ digite o seguinte comando:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Substitua `myhubname` pelo nome do seu Hub IoT. A resposta é semelhante ao texto a seguir:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Obtenha a __política de acesso compartilhado__ e __chave__. Para este exemplo, use a chave de __serviço__. Para obter essas informações, use um dos métodos a seguir:

    * __Do [portal do Azure](https://portal.azure.com/)__, use as etapas a seguir:

        1. Selecione __Políticas de acesso compartilhado__ e selecione __serviço__.
        2. Copie o valor da __chave primária__ .
        3. Copie o valor da __cadeia de conexão--chave primária__ .

    * __Na [CLI do Azure](/cli/azure/get-started-with-azure-cli)__ digite o seguinte comando:

        1. Para obter o valor de chave primária, use o seguinte comando:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Substitua `myhubname` pelo nome do seu Hub IoT. A resposta é a chave primária para o `service` política para esse hub.

        2. Para obter a cadeia de conexão para a `service` política, use o seguinte comando:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Substitua `myhubname` pelo nome do seu Hub IoT. A resposta e a string de conexão para a política `service`.

## <a name="configure-the-source-connection"></a>Configurar a conexão da fonte

Para configurar a fonte para trabalhar com o Hub IoT, execute as seguintes ações de uma conexão SSH para o nó de borda:

1. Criar uma cópia do arquivo `connect-iot-source.properties` no `/usr/hdp/current/kafka-broker/config/` diretório. Para baixar o arquivo do projeto toketi-kafka-connect-iothub, use o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Para editar o arquivo `connect-iot-source.properties` e adicionar as informações de Hub IoT, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. No editor, localize e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotin`. Mensagens recebidas do Hub IoT são colocadas no `iotin` tópico.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo Hub de Eventos - nome compatível.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo Hub de Eventos - ponto de extremidade compatível.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Substitua `PLACEHOLDER` por `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Substitua `PLACEHOLDER` pela chave primária da `service` política.|
    |`IotHub.Partitions=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo número de partições nas etapas anteriores.|
    |`IotHub.StartTime=PLACEHOLDER`|Substitua `PLACEHOLDER` por uma data UTC. Esta data é quando o conector inicia a verificação de mensagens. O formato de data é `yyyy-mm-ddThh:mm:ssZ`.|
    |`BatchSize=100`|Substitua `100` por `5`. Essa alteração faz com que o conector lei as mensagens em Kafka, uma vez que há cinco novas mensagens no Hub IoT.|

    Para obter um exemplo de configuração, consulte [conector de origem do Kafka Connect para o Hub IOT do Azure](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Para salvar as alterações, use __Ctrl + X__, __Y__ e, em seguida, __Enter__.

Para obter mais informações sobre como configurar a origem do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>Configurar a conexão do coletor

Para configurar a conexão do coletor para trabalhar com o Hub IoT, execute as seguintes ações de uma conexão SSH para o nó de borda:

1. Criar uma cópia do arquivo `connect-iothub-sink.properties` no `/usr/hdp/current/kafka-broker/config/` diretório. Para baixar o arquivo do projeto toketi-kafka-connect-iothub, use o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Para editar o arquivo `connect-iothub-sink.properties` e adicionar as informações de Hub IoT, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. No editor, localize e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`topics=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotout`. Mensagens gravadas no `iotout` tópico são encaminhadas para o Hub IoT.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Substitua `PLACEHOLDER` pela cadeia de conexão obtida para a `service` política.|

    Para obter um exemplo de configuração, consulte [conector do coletor do Kafka Connect para o Hub IOT do Azure](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Para salvar as alterações, use __Ctrl + X__, __Y__ e, em seguida, __Enter__.

Para obter mais informações sobre como configurar o coletor do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>Iniciar o conector de origem

1. Para iniciar o conector de origem, use o comando a seguir a partir de uma conexão SSH para o nó de borda:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Depois que o conector é iniciado, envie mensagens para o Hub IoT do seu dispositivo. Como o conector lê mensagens do Hub IoT e os armazena no tópico Kafka, ele registra informações no console:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Você pode ver avisos que o conector for iniciado. Esses avisos não causam problemas para receber mensagens de hub IoT.

1. Pare o conector após alguns minutos usando **Ctrl + C** duas vezes. Levará alguns minutos para que o conector pare.

## <a name="start-the-sink-connector"></a>Iniciar o coletor do conector

Para uma conexão SSH ao nó de conexão, use o comando a seguir para iniciar o conector do coletor no modo autônomo:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Informações semelhantes ao seguinte texto serão exibidas são exibidas:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Você pode ver vários avisos que o conector foi iniciado. Você pode ignorá-los com segurança.

## <a name="send-messages"></a>Enviar mensagens

Para enviar mensagens por meio do conector, use as seguintes etapas:

1. Abra *uma segunda* sessão SSH para o cluster Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Obtenha o endereço dos agentes Kafka para a nova sessão SSH. Substitua senha pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Para enviar mensagens ao tópico `iotout`, use o comando a seguir:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando não retorna para o prompt de bash normal. Em vez disso, envia a entrada do teclado para o tópico `iotout`.

1. Para enviar uma mensagem para o seu dispositivo, colar um documento JSON para a sessão SSH para a `kafka-console-producer`.

    > [!IMPORTANT]  
    > Você deve definir o valor de `"deviceId"` entrada para a ID do dispositivo. O exemplo a seguir, o dispositivo é denominado `myDeviceId`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    O esquema para este documento JSON é descrito em mais detalhes em [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Se você estiver usando o dispositivo Simulated Raspberry Pi e ele estiver em execução, a seguinte mensagem será registrada pelo dispositivo:

    ```output
    Receive message: Turn On
    ```

    Reenvie o documento JSON, mas altere o valor da entrada `"message"`. O novo valor é registrado pelo dispositivo.

Para obter mais informações sobre como usar o conector do coletor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API do Apache Kafka Connect para iniciar o IoT Kafka Connector no HDInsight. Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
