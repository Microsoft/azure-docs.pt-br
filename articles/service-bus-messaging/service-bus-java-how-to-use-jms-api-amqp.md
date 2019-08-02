---
title: Como usar o AMQP 1,0 com a API do barramento de serviço Java JMS | Microsoft Docs
description: Como usar o Java Message Service (JMS) com o Barramento de Serviço do Microsoft Azure e Advanced Message Queuing Protocol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 47b077dbb62088093c60a588660045529678c58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318450"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0
O AMQP 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens robustos em plataformas cruzadas.

O suporte para o AMQP 1.0 no Service Bus significa que você pode usar o enfileiramento e publicar/assinar os recursos de mensagens agenciadas a partir de uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos formados por componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

Este artigo explica como usar os recursos de sistema de mensagens do Barramento de Serviço (tópicos sobre filas e publicação/assinatura) de aplicativos Java usando o popular padrão de API do JMS (Java Message Service). Existe um [artigo complementar](service-bus-amqp-dotnet.md) que explica como fazer o mesmo usando a API do Barramento de Serviço do .NET. Você pode usar esses dois guias em conjunto para saber mais sobre mensagens em plataformas cruzadas usando o AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao Barramento de serviço
Este guia pressupõe que você já tem um namespace do Barramento de Serviço do Microsoft Azure que contém uma fila denominada **basicqueue**. Caso contrário, você pode [criar o namespace e a fila](service-bus-create-namespace-portal.md) usando o [Portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar namespaces e filas do Barramento de Serviço, consulte [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Filas e tópicos particionados também dão suporte ao AMQP. Para saber mais, confira [Entidades de mensagens particionadas](service-bus-partitioning.md) e [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Baixando a biblioteca do cliente do JMS do AMQP 1.0
Para obter informações sobre onde baixar a versão mais recente da biblioteca do cliente Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Você deve adicionar os seguintes quatro arquivos JAR do arquivamento de distribuição do Apache Qpid JMS do AMQP 1.0 ao CLASSPATH do Java ao criar e executar aplicativos do JMS com o Barramento de Serviço:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> Os nomes e versões do JMS JAR podem ter mudado. Para obter detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Codificando os aplicativos Java
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface (JNDI)
O JMS usa a Java Naming and Directory Interface (JNDI) para criar uma separação entre nomes lógicos e físicos. Dois tipos de objetos JMS são resolvidos usando a JNDI: ConnectionFactory e Destination. A JNDI usa um modelo de provedor no qual você pode conectar diferentes serviços de diretório para lidar com tarefas de resolução de nome. A biblioteca Apache Qpid JMS do AMQP 1.0 vem com um Provedor JNDI simples baseado em arquivo de propriedades que é configurado usando um arquivo de propriedades no seguinte formato:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Configurar o contexto JNDI e configurar o ConnectionFactory

O **ConnectionString** referenciado em um disponível nas 'Políticas de Acesso compartilhado' nO [portal do Azure](https://portal.azure.com) na **Cadeia de Conexão Primária**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurar as Filas de Destino do Produtor e do Consumidor
A entrada usada para definir um destino no provedor JNDI do arquivo de propriedades do Qpid tem o seguinte formato:

Para criar a fila de destino para o Produtor- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Para criar a fila de destino para o Consumidor- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Escrever o aplicativo JMS
Não existem APIs ou opções especiais obrigatórias ao usar o JMS com o Service Bus. No entanto, existem algumas restrições que serão abordadas posteriormente. Da mesma forma que ocorre com qualquer aplicativo JMS, a primeiro item necessário é a configuração do ambiente JNDI, para ser capaz de resolver um **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar o InitialContext de JNDI
O ambiente JNDI é configurado por meio da transmissão de uma tabela de hash com informações de configuração para o construtor da classe javax.naming.InitialContext. Os dois elementos necessários da tabela de hash são o nome da classe de Initial Context Factory e a URL do Provedor. O código a seguir mostra como configurar o ambiente JNDI para usar o Provedor JNDI com base em arquivo de propriedades do Qpid com um arquivo de propriedades chamado **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Um aplicativo JMS simples que usa uma fila do Barramento de Serviço
O programa de exemplo a seguir envia TextMessages do JMS para uma fila do Service Bus com o nome lógico de JNDI da FILA e recebe as mensagens de volta.

Você pode acessar todos os as código-fonte e informações de configuração e o [Início Rápido de Fila JMS dos Exemplos do Barramento de Serviço do Microsoft Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Executar o aplicativo
Passe a **Cadeia de Conexão** das políticas de acesso compartilhado para executar o aplicativo.
Abaixo está a saída do formulário, executando o aplicativo:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Disposição do Advanced Message Queuing Protocol e mapeamento da operação de Barramento de Serviço
Aqui está como uma disposição de AMQP se traduz em uma operação de Barramento de Serviço:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tópicos de JMS vs. Tópicos do Service Bus
O uso de tópicos e assinaturas do barramento de serviço do Azure por meio da API JMS (Java Message Service) fornece recursos básicos de envio e recebimento. É uma opção conveniente ao portar aplicativos de outros agentes de mensagem com APIs compatíveis com JMS, mesmo que os tópicos do barramento de serviço sejam diferentes dos tópicos JMS e exijam alguns ajustes. 

Os tópicos do barramento de serviço do Azure roteiam mensagens para assinaturas nomeadas, compartilhadas e duráveis que são gerenciadas por meio da interface de gerenciamento de recursos do Azure, das ferramentas de linha de comando do Azure ou do portal do Azure. Cada assinatura permite até 2000 regras de seleção, cada uma delas pode ter uma condição de filtro e, para filtros SQL, também uma ação de transformação de metadados. Cada correspondência de condição de filtro seleciona a mensagem de entrada a ser copiada na assinatura tehj.  

O recebimento de mensagens de assinaturas é idêntico ao receber mensagens de filas. Cada assinatura tem uma fila de mensagens mortas associada, bem como a capacidade de encaminhar mensagens automaticamente para outra fila ou tópicos. 

Os tópicos JMS permitem que os clientes criem dinamicamente assinantes não duráveis e duráveis que permitem, opcionalmente, filtrar mensagens com seletores de mensagem. Essas entidades não compartilhadas não são suportadas pelo barramento de serviço. No entanto, a sintaxe da regra de filtro SQL para o barramento de serviço é muito semelhante à sintaxe do seletor de mensagem suportada por JMS. 

O lado do Publicador do tópico JMS é compatível com o barramento de serviço, conforme mostrado neste exemplo, mas assinantes dinâmicos não são. Não há suporte para as seguintes APIs JMS relacionadas à topologia com o barramento de serviço. 

## <a name="unsupported-features-and-restrictions"></a>Restrições e recursos não suportados
As restrições a seguir ocorrem durante o uso do JMS sobre o AMQP 1.0 com o Service Bus, ou seja:

* Apenas um **MessageProducer** ou **MessageConsumer** é permitido por **Sessão**. Se precisar criar vários **MessageProducers** ou **MessageConsumers** em um aplicativo, crie uma **Session** dedicada para cada um deles.
* Assinaturas de tópico voláteis não são atualmente suportadas.
* **MessageSelectors** não são atualmente suportados.
* Não há suporte para as sessões transacionadas e transações distribuídas.

Além disso, o Barramento de Serviço do Microsoft Azure divide o plano de controle do plano de dados e, portanto, não é compatível com várias funções de topologia dinâmica do JMS:

| Método sem suporte          | Substitua por                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | criar uma assinatura de tópico portando o seletor de mensagem                                 |
| createDurableConsumer       | criar uma assinatura de tópico portando o seletor de mensagem                                 |
| createSharedConsumer        | Os tópicos do Barramento de Serviço sempre podem ser compartilhados, consulte acima                                       |
| createSharedDurableConsumer | Os tópicos do Barramento de Serviço sempre podem ser compartilhados, consulte acima                                       |
| createTemporaryTopic        | criar um tópico por meio do gerenciamento de API/ferramentas/portal com *AutoDeleteOnIdle* definido como um período de expiração |
| createTopic                 | criar um tópico por meio de ferramentas/API/portal de gerenciamento                                           |
| cancelar assinatura                 | excluir a API de gerenciamento do tópico/ferramentas/portal                                             |
| createBrowser               | sem suporte. Usar a funcionalidade de Peek() da API do Barramento de Serviço                         |
| createQueue                 | criar uma fila por meio de ferramentas/API/portal de gerenciamento                                           | 
| createTemporaryQueue        | criar uma fila por meio do gerenciamento de API/ferramentas/portal com *AutoDeleteOnIdle* definido como um período de expiração |

## <a name="summary"></a>Resumo
Este guia de instruções explicou como usar os recursos do sistema de mensagens agenciado do Barramento de Serviço (tópicos sobre filas e publicação/assinatura) do Java usando a API popular JMS e o AMQP 1.0.

Você também pode usar o AMQP 1.0 do Service Bus de outras linguagens, incluindo .NET, C, Python e PHP. Os componentes criados com essas diferentes linguagens podem trocar mensagens de forma confiável e com total fidelidade usando o suporte do AMQP 1.0 no Service Bus.

## <a name="next-steps"></a>Próximas etapas
* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](service-bus-amqp-overview.md)
* [Como usar o AMQP 1.0 com a API .NET do Barramento de Serviço](service-bus-dotnet-advanced-message-queuing.md)
* [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](service-bus-amqp-dotnet.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Centro de Desenvolvedores do Java](https://azure.microsoft.com/develop/java/)

