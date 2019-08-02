---
title: Tópicos sobre espelho do Apache Kafka – Azure HDInsight
description: Saiba como usar o recurso de espelhamento do Apache Kafka para manter uma réplica de um cluster Kafka no HDInsight espelhando tópicos para um cluster secundário.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657136"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Use MirrorMaker para replicar tópicos do Apache Kafka com Kafka no HDInsight

Saiba como usar o recurso de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. O espelhamento pode ser executado como um processo contínuo ou usado de forma intermitente como um método de migração de dados de um cluster para outro.

Neste exemplo, o espelhamento é usado para replicar tópicos entre dois clusters de HDInsight. Ambos os clusters estão em diferentes redes virtuais em diferentes datacenters.

> [!WARNING]  
> O espelhamento não deve ser considerado um meio de obter tolerância a falhas. O deslocamento para itens dentro de um tópico são diferentes entre os clusters primários e secundários, para que os clientes não podem usar os dois intercambiavelmente.
>
> Se estiver preocupado com a tolerância a falhas, você deverá definir a replicação para os tópicos no cluster. Para saber mais, consulte [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelhamento do Apache Kafka

Espelhamento funciona usando o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ferramenta (parte do Apache Kafka) para consumir registros de tópicos no cluster primário e, em seguida, crie uma cópia local no cluster secundário. MirrorMaker usa um (ou mais) *consumidores* que leem do cluster primário e uma *produtor* que grava no cluster (secundários) local.

A configuração de espelhamento mais úteis para a recuperação de desastres utiliza clusters Kafka em diferentes regiões do Azure. Para fazer isso, as redes virtuais, onde residem os clusters são emparelhadas juntos.

O diagrama a seguir ilustra o processo de espelhamento e como a comunicação flui entre clusters:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Os clusters primários e secundários podem ser diferentes no número de partições e nós e deslocamentos nos tópicos também são diferentes. O espelhamento mantém o valor de chave que é usado para particionamento. Assim, a ordem de registros é preservada por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento entre limites de rede

Se você precisa de espelhamento entre clusters Kafka em redes diferentes, há as seguintes considerações adicionais:

* **Gateways**: As redes devem ser capazes de se comunicar no nível do TCP/IP.

* **Endereçamento de servidor**: Você pode optar por tratar os nós de cluster usando seus endereços IP ou nomes de domínio totalmente qualificado.

    * **Endereços IP**: Se você configurar os clusters do Kafka para usar anúncio de endereço IP, você poderá continuar com a configuração de espelhamento com os endereços IP de nós do zookeeper e nós do agente.
    
    * **Nomes de domínio**: Se você não configurar os clusters do Kafka para anúncio de endereço IP, os clusters devem ser capazes de se conectar entre si usando nomes totalmente qualificados domínio (FQDNs). Isso requer um servidor de sistema de nome de domínio (DNS) em cada rede que está configurado para encaminhar solicitações para outras redes. Ao criar uma Rede Virtual do Azure, em vez de usar o DNS automático fornecido com a rede, você deve especificar um servidor DNS personalizado e o endereço IP do servidor. Depois que a Rede Virtual for criada, você deverá criar uma Máquina Virtual do Azure que use esse endereço IP e instalar e configurar o software DNS nela.

    > [!WARNING]  
    > Crie e configure o servidor DNS personalizado antes de instalar o HDInsight na Rede Virtual. Não é necessária configuração adicional para que o HDInsight use o servidor DNS configurado para a Rede Virtual.

Para obter mais informações sobre como conectar duas Redes Virtuais do Azure, confira [Configurar uma conexão de VNet para VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura do espelhamento

Essa arquitetura apresenta dois clusters em diferentes grupos de recursos e redes virtuais: uma **primário** e **secundário**.

### <a name="creation-steps"></a>Etapas de criação

1. Crie dois novos grupos de recursos:

    |Grupo de recursos | Location |
    |---|---|
    | kafka-primary-rg | Centro dos EUA |
    | kafka-secondary-rg | Centro-Norte dos EUA |

1. Criar uma nova rede virtual **kafka-principal-vnet** na **kafka-principal-rg**. Deixe as configurações padrão.
1. Criar uma nova rede virtual **kafka-secundário-vnet** na **kafka-secundário-rg**, também com as configurações padrão.

1. Crie dois novos clusters Kafka:

    | Nome do cluster | Grupo de recursos | Rede Virtual | Conta de armazenamento |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Crie emparelhamentos de rede virtual. Esta etapa criará dois emparelhamentos: um da **kafka-principal-vnet** para **kafka-secundário-vnet** e um failback do **kafka-secundário-vnet** para  **kafka-principal-vnet**.
    1. Selecione o **kafka-principal-vnet** rede virtual.
    1. Clique em **emparelhamentos** sob **configurações**.
    1. Clique em **Adicionar** .
    1. Sobre o **adicionar emparelhamento** tela, insira os detalhes conforme mostrado na captura de tela abaixo.

        ![Adicionar o emparelhamento de rede virtual](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Configure anúncio IP:
    1. Vá para o painel do Ambari para o cluster primário: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Clique em **Services** > **Kafka**. Selecione a guia **Configurações** .
    1. Adicione as seguintes linhas de configuração para a parte inferior **kafka-env modelo** seção. Clique em **Salvar**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Insira uma observação o **salvar Configuration** tela e clique em **salvar**.
    1. Se você for solicitado com aviso de configuração, clique em **continuar assim mesmo**.
    1. Clique em **Okey** sobre o **salvar alterações de configuração**.
    1. Clique em **reinicie** > **reiniciar todos os afetados** no **reinicialização necessária** notificação. Clique em **confirmar reiniciar todos os**.

        ![Reinicie nós de kafka](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Configure o Kafka para escutar em todos os adaptadores de rede.
    1. Mantenha-se na **configurações** guia sob **Services** > **Kafka**. No **Kafka Broker** seção conjunto a **ouvintes** propriedade `PLAINTEXT://0.0.0.0:9092`.
    1. Clique em **Salvar**.
    1. Clique em **reinicie**, e **confirmar reiniciar todos os**.

1. Registre os endereços IP do agente e endereços de Zookeeper para o cluster primário.
    1. Clique em **Hosts** no painel do Ambari.
    1. Anote os endereços IP para os agentes e Zookeepers. Os nós de broker têm **wn** como as duas primeiras letras do nome do host e o zookeeper nós têm **zk** como as duas primeiras letras do nome do host.

        ![Exibir endereços ip](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Repita as três etapas anteriores para o segundo cluster **cluster do kafka-secundário**: configurar o anúncio de IP, definir ouvintes e anote os endereços IP de Zookeeper e Broker.

## <a name="create-topics"></a>Criar tópicos

1. Conectar-se para o **primário** cluster usando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua**sshuser** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **BASENAME** pelo nome base usado ao criar o cluster.

    Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para criar uma variável com os hosts Zookeeper do Apache para o cluster primário. Como as cadeias de caracteres `ZOOKEEPER_IP_ADDRESS1` deve ser substituído com os endereços IP reais registrados anteriormente, como `10.23.0.11` e `10.23.0.7`. Se você estiver usando a resolução do FQDN com um servidor DNS personalizado, siga [essas etapas](apache-kafka-get-started.md#getkafkainfo) obter zookeeper e broker nomes.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Para criar um tópico nomeado `testtopic`, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Use o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta contém `testtopic`.

4. Use o seguinte para exibir as informações de host do Zookeeper para este (a **primário**) cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Isso retorna informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salve essas informações. Ele é usado na próxima seção.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Conectar-se para o **secundário** usando uma sessão SSH diferente do cluster:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua**sshuser** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **SECONDARYCLUSTER** com o nome usado ao criar o cluster.

    Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Um `consumer.properties` arquivo é usado para configurar a comunicação com o **primário** cluster. Para criar o arquivo, use o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Use o seguinte texto como o conteúdo do arquivo `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** com os endereços IP de Zookeeper do **primário** cluster.

    Esse arquivo descreve as informações de consumidor a serem usadas durante a leitura do cluster Kafka primário. Para obter mais informações de configuração do consumidor, confira [Configurações de Consumidor](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para salvar o arquivo, use **Ctrl + X**, **Y** e, em seguida, **Enter**.

3. Antes de configurar o produtor que se comunica com o cluster secundário, de uma variável para os endereços IP do agente de instalação do **secundário** cluster. Use os comandos a seguir para criar essa variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O comando `echo $SECONDARY_BROKERHOSTS` deve retornar informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. Um `producer.properties` arquivo é usado para comunicar-se a **secundário** cluster. Para criar o arquivo, use o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Use o seguinte texto como o conteúdo do arquivo `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** com os endereços IP de agente usados na etapa anterior.

    Para obter mais informações de configuração produtor, confira [Configurações de Produtor](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

5. Use os comandos a seguir para criar uma variável de ambiente com os endereços IP dos hosts do Zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. A configuração padrão do Kafka no HDInsight não permite a criação automática de tópicos. Você deve usar uma das seguintes opções antes de iniciar o processo de Espelhamento:

    * **Criar os tópicos no cluster secundário**: Essa opção também permite que você defina o número de partições e o fator de replicação.

        Você pode criar tópicos com antecedência usando o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Substitua `testtopic` pelo nome do tópico a ser criado.

    * **Configurar o cluster para criação automática de tópico**: Essa opção permite que o MirrorMaker crie tópicos automaticamente, no entanto, ele poderá criá-los com um número diferente de partições ou fator de replicação que o tópico principal.

        Para configurar o cluster secundário para criar tópicos automaticamente, execute estas etapas:

        1. Vá para o painel do Ambari para o cluster secundário: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Clique em **Services** > **Kafka**. Selecione a guia **Configurações** .
        5. No campo __Filtrar__, digite um valor de `auto.create`. Isso filtrará a lista de propriedades e exibirá a configuração `auto.create.topics.enable`.
        6. Altere o valor de `auto.create.topics.enable` para true e, em seguida, selecione __Salvar__. Adicionar uma observação e, em seguida, selecione __Salvar__ novamente.
        7. Selecione o serviço __Kafka__, selecione __Reiniciar__ e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __Confirmar reiniciar tudo__.

        ![Configurar a criação automática de tópico](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. De que a conexão SSH para o **secundário** do cluster, use o seguinte comando para iniciar o processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros usados neste exemplo são:

    * **--consumer.config**: Especifica o arquivo que contém as propriedades do consumidor. Essas propriedades são usadas para criar um consumidor que lê a partir de *primário* cluster Kafka.

    * **--producer.config**: Especifica o arquivo que contém as propriedades do produtor. Essas propriedades são usadas para criar um produtor que grava o *secundário* cluster Kafka.

    * **--whitelist**: Uma lista de tópicos que o MirrorMaker replica do cluster primário para o secundário.

    * **--num.streams**: Número de threads de consumidor a serem criados.

    O consumidor no nó secundário agora está aguardando para receber mensagens.

2. De que a conexão SSH para o **primário** do cluster, use o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando você chega a uma linha em branco com um cursor, digite algumas mensagens de texto. As mensagens são enviadas para o tópico o **primário** cluster. Depois de concluído, use **Ctrl + C** para finalizar o processo de produtor.

3. De que a conexão SSH para o **secundário** do cluster, use **Ctrl + C** para finalizar o processo MirrorMaker. O processo pode levar vários segundos para finalizar. Para verificar que as mensagens foram replicadas para o secundário, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos agora inclui `testtopic`, que é criado quando MirrorMaster espelha o tópico do cluster primário para o secundário. As mensagens recuperadas do tópico são as mesmas que aquelas que você inseriu no cluster primário.

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

As etapas neste documento criaram clusters em grupos de recursos do Azure diferentes. Para excluir todos os recursos criados, você pode excluir os grupos de dois recursos criados: **kafka-principal-rg** e **kafka secondary_rg**. Excluir grupos de recursos remove todos os recursos criados por este documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o [MirrorMake](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)r para criar uma réplica de um cluster [Apache Kafka](https://kafka.apache.org/). Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Documentação do Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conectar-se ao Apache Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
