---
title: Migrar aplicativos Java Message Service (JMS) do Apache ActiveMQ para o barramento de serviço do Azure | Microsoft Docs
description: Este artigo explica como migrar aplicativos JMS existentes que interagem com o Apache ActiveMQ para interagir com o barramento de serviço do Azure.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: b8408dde86d1902cf5b4899c4783c9dd185449ee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515739"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migrar aplicativos Java Message Service (JMS) 2,0 do Apache ActiveMQ para o barramento de serviço do Azure

Este artigo discute como modificar um aplicativo existente do Java Message Service (JMS) 2,0 que interage com um agente JMS para interagir com o barramento de serviço do Azure em vez disso. Em particular, o artigo aborda a migração do Apache ActiveMQ ou do Amazon MQ.

O barramento de serviço do Azure dá suporte à plataforma Java 2, Enterprise Edition e Spring cargas de trabalho que usam a API JMS 2,0 sobre o AMQP (protocolo de enfileiramento de mensagens avançado).

## <a name="before-you-start"></a>Antes de começar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Diferenças entre o barramento de serviço do Azure e o Apache ActiveMQ

O barramento de serviço do Azure e o Apache ActiveMQ são agentes de mensagens, funcionando como provedores JMS para que os aplicativos cliente enviem mensagens e recebam mensagens do. Ambos habilitam a semântica ponto a ponto com filas e a semântica de publicação/assinatura com tópicos e assinaturas. 

Mesmo assim, há algumas diferenças entre as duas, como mostra a tabela a seguir:

| Categoria | ActiveMQ | Barramento de Serviço do Azure |
| --- | --- | --- |
| Camadas do aplicativo | Monolítico clusterizado | Duas camadas <br> (gateway + back-end) |
| Suporte a protocolo | <ul> <li>AMQP</li> <li> IGNORAR </li> <li> OpenWire </li> </ul> | AMQP |
| Modo de provisionamento | <ul> <li> IaaS (infraestrutura como serviço), local </li> <li> Amazon MQ (plataforma como serviço gerenciada) </li> | PaaS (plataforma como serviço) gerenciada |
| Tamanho da mensagem | Configurável pelo cliente | 1 MB (camada Premium) |
| Alta disponibilidade | Gerenciado pelo cliente | Gerenciado pela plataforma |
| Recuperação de desastre | Gerenciado pelo cliente | Gerenciado pela plataforma | 

### <a name="current-supported-and-unsupported-features"></a>Recursos com suporte e sem suporte atuais

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Considerações

A natureza em duas camadas do barramento de serviço do Azure proporciona vários recursos de continuidade de negócios (alta disponibilidade e recuperação de desastre). No entanto, há algumas considerações quando você estiver usando recursos JMS.

#### <a name="service-upgrades"></a>Atualizações de serviço

No caso de atualizações e reinicializações do barramento de serviço, filas ou tópicos temporários são excluídos. Se seu aplicativo for sensível à perda de dados em filas ou tópicos temporários, não use filas ou tópicos temporários. Use filas duráveis, tópicos e assinaturas em vez disso.

#### <a name="data-migration"></a>Migração de dados

Como parte da migração e modificação de seus aplicativos cliente para interagir com o barramento de serviço do Azure, os dados mantidos no ActiveMQ não são migrados para o barramento de serviço. Talvez seja necessário um aplicativo personalizado para drenar as filas, os tópicos e as assinaturas do ActiveMQ e, em seguida, reproduzir as mensagens para as filas, os tópicos e as assinaturas do barramento de serviço.

#### <a name="authentication-and-authorization"></a>Autenticação e autorização

O controle de acesso baseado em função do Azure (RBAC do Azure), apoiado por Azure Active Directory, é o mecanismo de autenticação preferencial para o barramento de serviço. Como o RBAC do Azure, ou a autenticação baseada em declarações, atualmente não é suportado pelo Apache QPID JMS, no entanto, você deve usar chaves SAS para autenticação.

## <a name="pre-migration"></a>Pré-migração

### <a name="version-check"></a>Verificação de versão

Você usa os seguintes componentes e versões enquanto está gravando os aplicativos JMS: 

| Componente | Versão |
|---|---|
| API do Java Message Service (JMS) | 1,1 ou superior |
| Protocolo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Verifique se as portas AMQP estão abertas

O barramento de serviço dá suporte à comunicação por meio do protocolo AMQP. Para essa finalidade, habilite a comunicação pelas portas 5671 (AMQP) e 443 (TCP). Dependendo de onde os aplicativos cliente estão hospedados, talvez seja necessário um tíquete de suporte para permitir a comunicação por essas portas.

> [!IMPORTANT]
> O barramento de serviço dá suporte apenas ao protocolo AMQP 1,0.

### <a name="set-up-enterprise-configurations"></a>Configurar as configurações corporativas

O barramento de serviço permite vários recursos de segurança e alta disponibilidade da empresa. Para obter mais informações, consulte: 

  * [Pontos de extremidade de serviço de rede virtual](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Criptografia do lado do serviço com BYOK (chave gerenciada pelo cliente)](configure-customer-managed-key.md)
  * [Pontos de extremidade privados](private-link-service.md)
  * [Autenticação e autorização](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitoramento, alertas e rastreamento

Para cada namespace do barramento de serviço, você publica métricas em Azure Monitor. Você pode usar essas métricas para alertas e dimensionamento dinâmico de recursos alocados para o namespace.

Para obter mais informações sobre as diferentes métricas e como configurar alertas nelas, consulte [métricas do barramento de serviço em Azure monitor](service-bus-metrics-azure-monitor.md). Você também pode saber mais sobre o [rastreamento do lado do cliente para operações de dados](service-bus-end-to-end-tracing.md) e [log operacional/de diagnóstico para operações de gerenciamento](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Métricas-novo Relic

Você pode correlacionar quais métricas do ActiveMQ MAP para quais métricas no barramento de serviço do Azure. Consulte o seguinte no site do New Relic:

  * [Novas métricas do ActiveMQ/Amazon MQ New Relic](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Novas métricas de Relic do barramento de serviço do Azure](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Atualmente, o New Relic não tem integração direta e integrada com o ActiveMQ, mas eles têm métricas disponíveis para o Amazon MQ. Como o Amazon MQ é derivado de ActiveMQ, a tabela a seguir mapeia as novas métricas Relic do Amazon MQ para o barramento de serviço do Azure.
>

|Agrupamento de métricas| Métrica do Amazon MQ/ActiveMQ | Métrica do barramento de serviço do Azure |
|------------|---------------------------|--------------------------|
|Agente|`CpuUtilization`|`CPUXNS`|
|Agente|`MemoryUsage`|`WSXNS`|
|Agente|`CurrentConnectionsCount`|`activeConnections`|
|Agente|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Agente|`InactiveDurableTopicSubscribersCount`|Usar métricas de assinatura|
|Agente|`TotalMessageCount`|Usar nível de fila/tópico/assinatura `activeMessages`|
|Fila/tópico|`EnqueueCount`|`incomingMessages`|
|Fila/tópico|`DequeueCount`|`outgoingMessages`|
|Fila|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migração

Para migrar seu aplicativo JMS 2,0 existente para interagir com o barramento de serviço, siga as etapas nas várias seções a seguir.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Exportar a topologia de ActiveMQ e criar as entidades no barramento de serviço (opcional)

Para garantir que os aplicativos cliente possam se conectar diretamente com o barramento de serviço, migre a topologia (incluindo filas, tópicos e assinaturas) do Apache ActiveMQ para o barramento de serviço.

> [!NOTE]
> Para aplicativos JMS, você cria filas, tópicos e assinaturas como uma operação de tempo de execução. A maioria dos provedores JMS (Message Brokers) oferece a capacidade de criá-los em tempo de execução. É por isso que essa etapa de exportação é considerada opcional. Para garantir que seu aplicativo tenha as permissões para criar a topologia em tempo de execução, use a cadeia de conexão com permissões de SAS `Manage` .

Para fazer isso:

1. Use as [ferramentas de linha de comando ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) para exportar a topologia.
1. Recrie a mesma topologia usando um modelo de [Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Execute o modelo de Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importar a dependência do Maven para implementação JMS do barramento de serviço

Para garantir a conectividade direta com o barramento de serviço, adicione o `azure-servicebus-jms` pacote como uma dependência ao `pom.xml` arquivo Maven, da seguinte maneira:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Alterações de configuração do servidor de aplicativos

Essa parte é personalizada para o servidor de aplicativos que está hospedando os aplicativos cliente que se conectam ao ActiveMQ.

#### <a name="tomcat"></a>Tomcat

Aqui, você começa com a configuração específica para ActiveMQ, conforme mostrado no `/META-INF/context.xml` arquivo.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Adapte isso para apontar para o barramento de serviço, da seguinte maneira:

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Aplicativos Spring

##### <a name="update-the-applicationproperties-file"></a>Atualizar o `application.properties` arquivo

Se você estiver usando um aplicativo Spring boot para se conectar ao ActiveMQ, você deseja remover as propriedades específicas do ActiveMQ do `application.properties` arquivo.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Em seguida, adicione as propriedades específicas do barramento de serviço ao `application.properties` arquivo.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Substitua `ActiveMQConnectionFactory` por `ServiceBusJmsConnectionFactory`

A próxima etapa é substituir a instância do `ActiveMQConnectionFactory` pelo `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> As alterações de código reais são específicas para o aplicativo e como as dependências são gerenciadas, mas o exemplo a seguir fornece a orientação sobre o que deve ser alterado.
>

Anteriormente, você pode ter feito uma instância de um objeto de `ActiveMQConnectionFactory` , da seguinte maneira:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Agora, você está alterando isso para instanciar um objeto de, da seguinte `ServiceBusJmsConnectionFactory` maneira:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Após a migração

Agora que você modificou o aplicativo para começar a enviar e receber mensagens do barramento de serviço, você deve verificar se ele funciona conforme o esperado. Quando isso for feito, você poderá continuar refinando e modernizando sua pilha de aplicativos.

## <a name="next-steps"></a>Próximas etapas

Use o [iniciador do Spring boot para o barramento de serviço do Azure JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) para integração direta com o barramento de serviço.

Para saber mais sobre o sistema de mensagens do barramento de serviço e JMS, consulte:

* [Barramento de serviço JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)