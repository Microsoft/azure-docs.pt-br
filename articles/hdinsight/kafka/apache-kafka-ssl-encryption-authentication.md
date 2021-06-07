---
title: Autenticação & criptografia TLS Apache Kafka-Azure HDInsight
description: Configure a criptografia TLS para comunicação entre os clientes Kafka e os agentes Kafka, bem como entre os agentes Kafka. Configurar a autenticação SSL de clientes.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: d061832022b983e4d5fd55e72c1d4789b82f6633
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863217"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia e a autenticação TLS para Apache Kafka no Azure HDInsight

Este artigo mostra como configurar a criptografia TLS, anteriormente conhecida como criptografia de protocolo SSL (SSL), entre Apache Kafka clientes e Apache Kafka agentes. Ele também mostra como configurar a autenticação de clientes (às vezes chamados de TLS bidirecional).

> [!Important]
> Há dois clientes que você pode usar para aplicativos Kafka: um cliente Java e um cliente de console. Somente o cliente Java `ProducerConsumer.java` pode usar o TLS para produção e consumo. O cliente do produtor do console não `console-producer.sh` funciona com o TLS.

> [!Note]
> O produtor do console Kafka do HDInsight com a versão 1,1 não oferece suporte a SSL.

## <a name="apache-kafka-broker-setup"></a>Instalação do Apache Kafka Broker

A instalação do agente TLS do Kafka usará quatro VMs do cluster HDInsight da seguinte maneira:

* cabeçalho 0-autoridade de certificação (CA)
* nó de trabalho 0, 1 e 2-agentes

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

O resumo do processo de configuração do agente é o seguinte:

1. As etapas a seguir são repetidas em cada um dos três nós de trabalho:

    1. Gere um certificado.
    1. Crie uma solicitação de assinatura de certificado.
    1. Envie a solicitação de assinatura de certificado para a autoridade de certificação (CA).
    1. Entre na AC e assine a solicitação.
    1. SCP o certificado assinado de volta para o nó de trabalho.
    1. SCP o certificado público da autoridade de certificação para o nó de trabalho.

1. Depois que você tiver todos os certificados, coloque os certificados no repositório de certificados.
1. Vá para Ambari e altere as configurações.

Use as seguintes instruções detalhadas para concluir a configuração do agente:

> [!Important]
> Nos trechos de código a seguir, wnX é uma abreviação de um dos três nós de trabalho e deve ser substituído `wn0` por `wn1` ou `wn2` conforme apropriado. `WorkerNode0_Name` e `HeadNode0_Name` devem ser substituídos pelos nomes dos respectivos computadores.

1. Execute a configuração inicial no nó de cabeçalho 0, que para o HDInsight preencherá a função da autoridade de certificação (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Execute a mesma configuração inicial em cada um dos agentes (nós de trabalho 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um dos nós de trabalho, execute as etapas a seguir usando o trecho de código abaixo.
    1. Crie um repositório de chaves e popule-o com um novo certificado privado.
    1. Crie uma solicitação de assinatura de certificado.
    1. SCP a solicitação de assinatura de certificado para a autoridade de certificação (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na máquina CA, execute o seguinte comando para criar os arquivos CA-CERT e CA-Key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Altere para o computador da CA e assine todas as solicitações de assinatura de certificado recebidas:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados assinados de volta para os nós de trabalho da CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público da autoridade de certificação para cada nó de trabalho.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó de trabalho, adicione o certificado público de CAs ao trustStore e ao keystore. Em seguida, adicione o próprio certificado assinado do nó de trabalho ao repositório de chaves

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Atualizar a configuração do Kafka para usar TLS e reiniciar os agentes

Agora você configurou cada agente do Kafka com um repositório de chaves e trustStore e importou os certificados corretos. Em seguida, modifique as propriedades de configuração do Kafka relacionadas usando o Ambari e reinicie os agentes do Kafka.

Execute as seguintes etapas para concluir a modificação à configuração:

1. Entre no portal do Azure e selecione seu cluster do Apache Kafka do Azure HDInsight.
1. Vá para a interface do usuário do Ambari, clicando em **Página Inicial do Ambari** nos **Painéis do cluster**.
1. Em **Agente do Kafka**, defina a propriedade **ouvintes** para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Agente avançado do Kafka**, defina a propriedade **security.inter.broker.protocol** para `SSL`

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="Editando propriedades de configuração do Kafka SSL no Ambari" border="true":::

1. Em **Agente do Kafka personalizado**, defina a propriedade **ssl.client.auth** para `required`. Esta etapa só será necessária se você estiver configurando a autenticação e a criptografia.

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="Editar as propriedades de configuração de SSL do Kafka no Ambari" border="true":::

1. Para a versão 3,6 do HDI, acesse a interface do usuário do Ambari e adicione as seguintes configurações em **Advanced Kafka-env** e a propriedade de **modelo Kafka-env** .

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Aqui está a captura de tela que mostra a interface do usuário de configuração do Ambari com essas alterações.

    Para HDI versão 3,6:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Editando a propriedade de modelo Kafka-env em Ambari" border="true":::

    Para HDI versão 4,0:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Editando a propriedade de modelo Kafka-env em Ambari quatro" border="true":::

1. Reinicie todos os agentes do Kafka.

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se você não precisar de autenticação, o resumo das etapas para configurar somente a criptografia TLS será:

1. Entre na AC (nó principal ativo).
1. Copie o certificado de autoridade de certificação no computador cliente do computador da CA (WN0).
1. Entre no computador cliente (hn1) e navegue até a `~/ssl` pasta.
1. Importe o certificado de autoridade de certificação para o trustStore.
1. Importe o certificado de autoridade de certificação para o repositório de chaves.

Essas etapas são detalhadas nos trechos de código a seguir.

1. Entre no nó da autoridade de certificação.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copiar o certificado de CA para o computador cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Entre no computador cliente (nó de cabeçalho de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importe o certificado de autoridade de certificação para o trustStore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importe o certificado de autoridade de certificação para o keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Crie o arquivo `client-ssl-auth.properties` no computador cliente (hn1). Ele deve conter as seguintes linhas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Inicie o cliente administrador com as opções produtor e consumidor para verificar se os produtores e os consumidores estão trabalhando na porta 9093. Consulte a seção de [verificação](apache-kafka-ssl-encryption-authentication.md#verification) abaixo para obter as etapas necessárias para verificar a configuração usando o produtor do console/consumidor.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> As etapas a seguir serão necessárias apenas se você estiver configurando a criptografia **e** a autenticação TLS. Se você estiver configurando apenas a criptografia, consulte [configuração do cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

As quatro etapas a seguir resumem as tarefas necessárias para concluir a configuração do cliente:

1. Entre no computador cliente (nó de cabeçalho de espera).
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está sendo executada.
1. Alterne para o computador da AC (nó de cabeçalho ativo) para assinar o certificado do cliente.
1. Vá para o computador cliente (nó de cabeçalho de espera) e navegue até a `~/ssl` pasta. Copie o certificado autoassinado para o computador cliente.

Os detalhes de cada etapa são fornecidos abaixo.

1. Entre no computador cliente (nó de cabeçalho de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Remova qualquer diretório SSL existente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Crie um repositório de chaves Java e crie uma solicitação de assinatura de certificado. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copiar a solicitação de assinatura de certificado para a autoridade de certificação

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Alterne para o computador de CA (nó de cabeçalho ativo) e assine o certificado do cliente.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copie o certificado do cliente assinado da autoridade de certificação (nó principal ativo) para o computador cliente.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copiar o certificado de CA para o computador cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Entre no computador cliente (nó de cabeçalho de espera) e navegue até o diretório SSL.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Criar a loja de cliente com certificado assinado e importar o certificado de autoridade de certificação para o keystore e trustStore no computador cliente (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Criar um arquivo `client-ssl-auth.properties` no computador cliente (hn1). Ele deve conter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificação

Execute estas etapas no computador cliente.

> [!Note]
> Se o HDInsight 4,0 e o Kafka 2,1 estiverem instalados, você poderá usar o produtor do console/consumidores para verificar sua configuração. Caso contrário, execute o produtor do Kafka na porta 9092 e envie mensagens para o tópico e, em seguida, use o consumidor do Kafka na porta 9093 que usa TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 ou superior

1. Crie um tópico se ele ainda não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie o produtor do console e forneça o caminho para `client-ssl-auth.properties` como um arquivo de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Abra outra conexão SSH para o computador cliente e inicie o consumidor do console e forneça o caminho para `client-ssl-auth.properties` o como um arquivo de configuração para o consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Crie um tópico se ele ainda não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie o produtor do console e forneça o caminho para o cliente-SSL-auth. Properties como um arquivo de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Abra outra conexão SSH para o computador cliente e inicie o consumidor do console e forneça o caminho para `client-ssl-auth.properties` o como um arquivo de configuração para o consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Próximas etapas

* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
