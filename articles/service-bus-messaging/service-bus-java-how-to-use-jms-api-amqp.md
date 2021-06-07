---
title: Usar o AMQP com a API do serviço de mensagens Java e o barramento de serviço do Azure
description: Use o Java Message Service (JMS) com o barramento de serviço do Azure e o Advanced Message Queuing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90086684"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Usar o serviço de mensagem Java com o barramento de serviço do Azure e o AMQP 1,0

> [!WARNING]
> Este artigo *atende ao suporte limitado* para a API JMS (Java Message Service) 1,1 e existe apenas para a camada standard do barramento de serviço do Azure.
>
> O suporte completo para a API 2,0 do serviço de mensagens Java está disponível somente na [camada Premium do barramento de serviço do Azure na visualização](how-to-use-java-message-service-20.md). Recomendamos que você use essa camada.
>

Este artigo explica como usar os recursos de mensagens do barramento de serviço de aplicativos Java usando o popular API JMS padrão. Esses recursos de mensagens incluem filas e publicação ou assinatura de tópicos. Um [artigo complementar](service-bus-amqp-dotnet.md) explica como fazer o mesmo usando a API .net do barramento de serviço do Azure. Você pode usar esses dois artigos juntos para saber mais sobre mensagens de plataforma cruzada usando o Advanced Message Queuing Protocol (AMQP) 1,0.

O AMQP 1,0 é um protocolo de mensagens eficiente, confiável e de nível de conexão que você pode usar para criar aplicativos de mensagens robustos e entre plataformas.

O suporte para AMQP 1,0 no barramento de serviço significa que você pode usar o enfileiramento e publicar ou assinar recursos de mensagens orientadas de uma variedade de plataformas usando um protocolo binário eficiente. Você também pode criar aplicativos compostos por componentes criados usando uma combinação de linguagens, estruturas e sistemas operacionais.

## <a name="get-started-with-service-bus"></a>Introdução ao Barramento de serviço

Este artigo pressupõe que você já tenha um namespace do barramento de serviço que contém uma fila chamada `basicqueue` . Se não tiver, você poderá [criar o namespace e a fila](service-bus-create-namespace-portal.md) usando o [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar namespaces e filas do Barramento de Serviço, consulte [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Filas e tópicos particionados também dão suporte ao AMQP. Para saber mais, confira [Entidades de mensagens particionadas](service-bus-partitioning.md) e [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Baixar a biblioteca de cliente AMQP 1,0 JMS

Para obter informações sobre onde baixar a versão mais recente da biblioteca de cliente Apache QPID JMS AMQP 1,0, consulte o [site de download do Apache QPID](https://qpid.apache.org/download.html).

Você deve adicionar os seguintes arquivos JAR do arquivo de distribuição do Apache QPID JMS AMQP 1,0 à variável de ambiente CLASSPATH Java ao compilar e executar aplicativos JMS com o barramento de serviço:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> As versões e os nomes de JAR JMS podem ter sido alterados. Para obter mais informações, consulte [QPID JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Codificar aplicativos Java

### <a name="java-naming-and-directory-interface"></a>Nomenclatura de Java e interface de diretório

O JMS usa a Java Naming and Directory Interface (JNDI) para criar uma separação entre nomes lógicos e físicos. Dois tipos de objetos JMS são resolvidos usando JNDI: **ConnectionFactory** e **Destination**. A JNDI usa um modelo de provedor no qual você pode conectar diferentes serviços de diretório para lidar com tarefas de resolução de nome. A biblioteca Apache QPID JMS AMQP 1,0 vem com um provedor JNDI baseado em arquivo de propriedade simples que é configurado usando um arquivo de propriedades do seguinte formato:

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

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Configurar o contexto JNDI e configurar o objeto ConnectionFactory

A cadeia de conexão referenciada é a disponível nas políticas de acesso compartilhado no [portal do Azure](https://portal.azure.com) na **cadeia de conexão primária**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurar as filas de destino do produtor e do consumidor

A entrada usada para definir um destino no arquivo de propriedades QPID do provedor JNDI é do formato a seguir.

Para criar uma fila de destino para o produtor:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Para criar uma fila de destino para o consumidor:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Escrever o aplicativo JMS

Nenhuma API ou opção especial é necessária quando você usa JMS com o barramento de serviço. Há algumas restrições que serão abordadas posteriormente. Assim como acontece com qualquer aplicativo JMS, a primeira coisa necessária é a configuração do ambiente JNDI para poder resolver um objeto **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext-object"></a>Configurar o objeto InitialContext JNDI

O ambiente JNDI é configurado passando uma tabela de hash de informações de configuração para o construtor da classe javax.naming.InitialContext. Os dois elementos necessários na tabela de hash são o nome da classe da fábrica de contexto inicial e a URL do provedor. O código a seguir mostra como configurar o ambiente JNDI para usar o provedor JNDI baseado em arquivo de propriedades QPID com um arquivo de propriedades chamado **ServiceBus. Properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Um aplicativo JMS simples que usa uma fila do barramento de serviço

O programa de exemplo a seguir envia mensagens de texto JMS para uma fila do barramento de serviço com o nome lógico JNDI da fila e recebe as mensagens de volta.

Você pode acessar todas as informações de código-fonte e de configuração do [início rápido da fila JMS de exemplos do barramento de serviço do Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

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
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
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
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
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
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
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

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
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
A saída a seguir é do formulário que executa o aplicativo:

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

Veja como uma disposição AMQP é convertida em uma operação do barramento de serviço:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tópicos sobre JMS vs. sobre o barramento de serviço

O uso de tópicos e assinaturas do barramento de serviço por meio da API JMS fornece recursos básicos de envio e recebimento. É uma opção conveniente quando você porta aplicativos de outros agentes de mensagens com APIs compatíveis com JMS, mesmo que os tópicos do barramento de serviço sejam diferentes dos tópicos JMS e exijam alguns ajustes.

Os tópicos do barramento de serviço roteiam mensagens para assinaturas nomeadas, compartilhadas e duráveis que são gerenciadas por meio da interface de gerenciamento de recursos do Azure, das ferramentas de linha de comando do Azure ou da portal do Azure. Cada assinatura permite até 2.000 regras de seleção, cada uma delas pode ter uma condição de filtro e, para filtros SQL, também uma ação de transformação de metadados. Cada correspondência de condição de filtro seleciona a mensagem de entrada a ser copiada na assinatura.  

O recebimento de mensagens de assinaturas é idêntico ao recebimento de mensagens de filas. Cada assinatura tem uma fila de mensagens mortas associada e a capacidade de encaminhar mensagens automaticamente para outra fila ou tópicos.

Os tópicos JMS permitem que os clientes criem dinamicamente assinantes não duráveis e duráveis que permitem, opcionalmente, filtrar mensagens com seletores de mensagem. Essas entidades não compartilhadas não têm suporte do barramento de serviço. A sintaxe da regra de filtro SQL para o barramento de serviço é semelhante à sintaxe do seletor de mensagem compatível com JMS.

O lado do Publicador do tópico JMS é compatível com o barramento de serviço, conforme mostrado neste exemplo, mas os assinantes dinâmicos não são. Não há suporte para as seguintes APIs JMS relacionadas à topologia com o barramento de serviço.

## <a name="unsupported-features-and-restrictions"></a>Restrições e recursos não suportados

As restrições a seguir existem quando você usa JMS sobre AMQP 1,0 com barramento de serviço, ou seja:

* Somente um objeto **MessageProducer** ou **MessageConsumer** é permitido por sessão. Se você precisar criar vários objetos **MessageProducer** ou **MessageConsumer** em um aplicativo, crie uma sessão dedicada para cada um deles.
* Atualmente, não há suporte para assinaturas de tópico volátil.
* Atualmente, não há suporte para objetos **MessageSelector** .
* As transações distribuídas não têm suporte, mas há suporte para sessões transacionadas.

O barramento de serviço divide o plano de controle do plano de dados, de modo que ele não dá suporte a várias funções de topologia dinâmica de JMS.

| Método sem suporte          | Substitua por                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Crie uma assinatura de tópico que porta o seletor de mensagem.                                |
| createDurableConsumer       | Crie uma assinatura de tópico que porta o seletor de mensagem.                                |
| createSharedConsumer        | Os tópicos do barramento de serviço são sempre compartilháveis. Consulte a seção "tópicos de JMS vs. tópicos do barramento de serviço".                                    |
| createSharedDurableConsumer | Os tópicos do barramento de serviço são sempre compartilháveis. Consulte a seção "tópicos de JMS vs. tópicos do barramento de serviço".                                      |
| createTemporaryTopic        | Crie um tópico por meio da API de gerenciamento, das ferramentas ou do portal com *AutoDeleteOnIdle* definido como um período de expiração. |
| createTopic                 | Crie um tópico por meio da API de gerenciamento, das ferramentas ou do Portal.                                         |
| cancelar assinatura                 | Exclua a API, as ferramentas ou o portal de gerenciamento de tópico.                                            |
| createBrowser               | Sem suporte: Use a funcionalidade Peek () da API do barramento de serviço.                         |
| createQueue                 | Crie uma fila por meio da API de gerenciamento, das ferramentas ou do Portal.                                           | 
| createTemporaryQueue        | Crie uma fila por meio da API de gerenciamento, das ferramentas ou do portal com *AutoDeleteOnIdle* definido como um período de expiração. |
| receiveNoWait               | Use o método Receive () fornecido pelo SDK do barramento de serviço e especifique um tempo limite muito baixo ou zero. |

## <a name="summary"></a>Resumo

Este artigo mostrou como usar os recursos de mensagens orientadas do barramento de serviço, como filas e tópicos de publicação ou assinatura, do Java usando a API JMS popular e o AMQP 1,0.

Você também pode usar o barramento de serviço AMQP 1,0 de outras linguagens, como .NET, C, Python e PHP. Os componentes criados com o uso dessas linguagens diferentes podem trocar mensagens de forma confiável e com total fidelidade usando o suporte do AMQP 1,0 no barramento de serviço.

## <a name="next-steps"></a>Próximas etapas

* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](service-bus-amqp-overview.md)
* [Usar o AMQP 1,0 com a API .NET do barramento de serviço](./service-bus-amqp-dotnet.md)
* [Guia do desenvolvedor do AMQP 1,0 do barramento de serviço](service-bus-amqp-dotnet.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Centro de desenvolvedores de Java](https://azure.microsoft.com/develop/java/)