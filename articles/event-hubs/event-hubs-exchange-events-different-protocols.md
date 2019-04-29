---
title: Trocar eventos entre aplicativos que usam protocolos diferentes - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo mostra como consumidores e produtores que usam protocolos diferentes (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao usar os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821778"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Trocar eventos entre consumidores e produtores que usam protocolos diferentes: AMQP, Kafka e HTTPS
Os Hubs de Eventos do Azure dão suporte a três protocolos para consumidores e produtores: AMQP, Kafka e HTTPS. Cada um desses protocolos tem sua própria maneira de representar uma mensagem. Portanto, a pergunta a seguir surge naturalmente: se um aplicativo envia eventos a um Hub de Eventos com um protocolo e os consome com um protocolo diferente, como ficam as várias partes e valores do evento quando chegam ao consumidor? Este artigo discute as práticas recomendadas para o produtor e consumidor a fim de garantir que os valores dentro de um evento sejam interpretados corretamente pelo aplicativo de consumo.

O conselho neste artigo aborda especificamente esses clientes, com as versões listadas usadas no desenvolvimento dos trechos de código:

* Cliente Java do Kafka (versão 1.1.1 de https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Cliente dos Hubs de Eventos do Microsoft Azure para Java (versão 1.1.0 de https://github.com/Azure/azure-event-hubs-java)
* Cliente dos Hubs de Eventos do Microsoft Azure para .NET (versão 2.1.0 de https://github.com/Azure/azure-event-hubs-dotnet)
* Barramento de Serviço do Microsoft Azure (versão 5.0.0 de https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (suporte para os produtores apenas)

Outros clientes AMQP podem se comportar de forma ligeiramente diferente. O AMQP tem um sistema de tipo bem definido, mas as particularidades da serialização de tipos específicos de linguagem de e para esse sistema de tipo dependem do cliente, assim como o modo de fornecimento de acesso do cliente às partes de uma mensagem AMQP.

## <a name="event-body"></a>Corpo do evento
Todos os clientes do Microsoft AMQP representam o corpo do evento como um recipiente não interpretado de bytes. Um aplicativo de produção passa uma sequência de bytes para o cliente e um aplicativo de consumo recebe essa mesma sequência do cliente. A interpretação da sequência de bytes acontece dentro do código do aplicativo.

Ao enviar um evento por HTTPS, o corpo do evento é o conteúdo do POST, que também é tratado como bytes não interpretados. É fácil conseguir o mesmo estado em um produtor ou consumidor do Kafka usando o ByteArraySerializer e o ByteArrayDeserializer fornecidos, conforme mostrado no código a seguir:

### <a name="kafka-byte-producer"></a>Produtor de byte[] de Kafka

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Consumidor de bytes[] do Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Esse código cria um pipeline de bytes transparente entre as duas metades do aplicativo e permite que o desenvolvedor do aplicativo serialize e desserialize manualmente da maneira que desejar, incluindo a tomada de decisões sobre desserialização em tempo de execução, por exemplo, com base em informações de tipo ou de remetente em propriedades definidas pelo usuário no evento.

É provável que os aplicativos que tenham um tipo de corpo de evento único e fixo possam usar outros serializadores e desserializadores de Kafka para converter os dados de forma transparente. Por exemplo, considere um aplicativo que usa JSON. A construção e a interpretação da cadeia de caracteres JSON ocorre no nível do aplicativo. No nível dos Hubs de Eventos, o corpo do evento é sempre uma cadeia de caracteres, uma sequência de bytes que representa os caracteres na codificação UTF-8. Nesse caso, o produtor ou consumidor do Kafka pode tirar proveito do StringSerializer ou StringDeserializer fornecido, conforme mostrado no código a seguir:

### <a name="kafka-utf-8-string-producer"></a>Produtor da cadeia de caracteres UTF-8 de Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Consumidor da cadeia de caracteres UTF-8 de Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

No AMQP, Java e .NET fornecem maneiras internas para converter cadeias de caracteres de/para sequências de bytes UTF-8. Os clientes do Microsoft AMQP representam eventos como uma classe chamada EventData. Os exemplos a seguir mostram como serializar uma cadeia de caracteres UTF-8 em um corpo de evento EventData em um produtor do AMQP, e como desserializar um corpo de evento EventData em uma cadeia de caracteres UTF-8 em um consumidor do AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Produtor da cadeia de caracteres UTF-8 AMQP de Java
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Consumidor da cadeia de caracteres UTF-8 AMQP de Java
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Produtor da cadeia de caracteres UTF-8 .NET em C#
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>Consumidor da cadeia de caracteres UTF-8 .NET em C#
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Como o Kafka é de software livre, o desenvolvedor do aplicativo pode inspecionar a implementação de qualquer serializador ou desserializador e implementar o código, que produz ou consome uma sequência compatível de bytes no AMQP.

## <a name="event-user-properties"></a>Propriedades do usuário do evento

É possível definir e recuperar propriedades definidas pelo usuário de ambos os clientes do AMQP (nos clientes Microsoft AMQP, elas são chamadas de propriedades) e do Kafka (onde são chamadas de cabeçalhos). Remetentes HTTPS podem definir propriedades de usuário em um evento fornecendo-os como cabeçalhos HTTP na operação POST. No entanto, o Kafka trata corpos de evento e valores de cabeçalho do evento como sequências de bytes. Enquanto em clientes AMQP, os valores de propriedade têm tipos que são comunicados pela codificação dos valores de propriedade de acordo com o sistema de tipos do AMQP.

HTTPS é um caso especial. No ponto de envio, todos os valores da propriedade são texto UTF-8. O serviço do Hubs de Eventos realiza uma quantidade limitada de interpretação para converter valores de propriedades adequados para inteiros com sinal de 32 bits e 64 bits codificados em AMQP, números de ponto flutuante de 64 bits e boolianos. Qualquer valor de propriedade que se encaixe em um desses tipos será tratado como uma cadeia de caracteres.

A combinação dessas abordagens para a digitação de propriedades significa que um consumidor do Kafka vê a sequência bruta de bytes codificada em AMQP, incluindo as informações de tipo AMQP. No entanto, um consumidor do AMQP vê a sequência de bytes sem tipo enviada pelo produtor do Kafka, aquela que o aplicativo deve interpretar.

Para os consumidores do Kafka que recebem as propriedades de produtores do AMQP ou HTTPS, use a classe AmqpDeserializer, que é modelada após os outros desserializadores no ecossistema do Kafka. Ela interpreta as informações de tipo em sequências de bytes codificadas por AMQP para desserializar os bytes de dados em um tipo de Java.

Recomendamos, como melhor prática, que você inclua uma propriedade em mensagens enviadas por meio de AMQP ou HTTPS. O consumidor do Kafka pode usá-la para determinar se os valores de cabeçalho precisam da desserialização de AMQP. O valor da propriedade não é importante. Ela só precisa de um nome conhecido que o consumidor do Kafka possa localizar na lista de cabeçalhos e ajustar seu comportamento adequadamente.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP para Kafka parte 1: criar e enviar um evento em C# (.NET) com propriedades
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP para Kafka parte 2: usar AmqpDeserializer para desserializar essas propriedades em um consumidor do Kafka
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Se o aplicativo souber o tipo esperado de uma propriedade, há métodos de desserialização que não exigem uma conversão posterior, mas geram um erro se a propriedade não for do tipo esperado.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP para Kafka parte 3: uma maneira diferente de usar AmqpDeserializer em um consumidor do Kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

A outra direção é mais complicada, pois cabeçalhos definidos por um produtor do Kafka sempre são vistos por um consumidor do AMQP como bytes brutos (digite org.apache.qpid.proton.amqp.Binary para o cliente dos Hubs de Eventos do Microsoft Azure para Java ou `System.Byte[]` para clientes do AMQP do .NET da Microsoft). O caminho mais fácil é usar um dos serializadores fornecidos pelo Kafka para gerar os bytes para os valores de cabeçalho no lado do produtor do Kafka e, em seguida, escrever um código de desserialização compatível no lado do consumidor do AMQP.

Assim como acontece no AMQP para Kafka, a melhor prática recomendada é incluir uma propriedade em mensagens enviadas por meio do Kafka. O consumidor do AMQP pode usar a propriedade para determinar se os valores de cabeçalho precisam de desserialização. O valor da propriedade não é importante. Ela só precisa de um nome conhecido que o consumidor do AMQP possa localizar na lista de cabeçalhos e ajustar seu comportamento adequadamente. Se não for possível alterar o produtor do Kafka, o aplicativo de consumo também poderá verificar se o valor da propriedade é de um tipo binário ou de byte e tentar a desserialização com base no tipo.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka para AMQP parte 1: criar e enviar um evento a partir do Kafka com propriedades
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka para AMQP parte 2: desserializar manualmente essas propriedades em C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka para AMQP parte 3: desserializar manualmente essas propriedades em Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como transmitir para os Hubs de Eventos com Kafka habilitado sem alterar seus clientes de protocolo ou seus próprios clusters em execução. Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos nos Hubs de Eventos do Kafka GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para Hubs de Eventos habilitados para Kafka na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Akka Streams](event-hubs-kafka-flink-tutorial.md) ou [Apache Flink](event-hubs-kafka-akka-streams-tutorial.md)
