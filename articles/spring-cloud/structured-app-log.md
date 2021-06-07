---
title: Log de aplicativo estruturado para o Azure Spring Cloud | Microsoft Docs
description: Este artigo explica como gerar e coletar dados de log de aplicativo estruturados no Azure Spring Cloud.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877735"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Log de aplicativo estruturado para o Azure Spring Cloud

Este artigo explica como gerar e coletar dados de log de aplicativo estruturados no Azure Spring Cloud. Com a configuração adequada, o Azure Spring Cloud fornece consulta e análise de log de aplicativo úteis por meio de Log Analytics.

## <a name="log-schema-requirements"></a>Requisitos de esquema de log
Para melhorar a experiência de consulta de log, um log de aplicativo precisa estar no formato JSON e estar em conformidade com um esquema. O Azure Spring Cloud usa esse esquema para analisar seu aplicativo e transmitir para Log Analytics. 

**Requisitos de esquema JSON:**

| Chave JSON      | Tipo de valor JSON|  Obrigatório | Coluna em Log Analytics| Descrição |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | Cadeia de caracteres      |     Yes   | AppTimestamp    | carimbo de data/hora no formato UTC  |
| agente        | Cadeia de caracteres      |     No    | Agente          | agente                   |
| nível         | Cadeia de caracteres      |     No    | CustomLevel     | nível de log                |
| thread        | Cadeia de caracteres      |     No    | Thread          | thread                   |
| message       | Cadeia de caracteres      |     No    | Mensagem         | mensagem de log              |
| Pilha    | Cadeia de caracteres      |     No    | StackTrace      | rastreamento de pilha de exceção    |
| exceptionClass| Cadeia de caracteres      |     No    | ExceptionClass  | nome da classe de exceção     |
| MDC           | JSON aninhado |     No    |                 | contexto de diagnóstico mapeado|
| MDC. TraceID   | Cadeia de caracteres      |     No    | TraceId         |ID de rastreamento para rastreamento distribuído|
| MDC. spanid    | Cadeia de caracteres      |     No    | Spanid          |ID de extensão para rastreamento distribuído |
|               |             |           |                 |                          |

* O campo "timestamp" é necessário e deve estar no formato UTC, todos os outros campos são opcionais.
* "TraceID" e "spanid" no campo "MDC" são usados para fins de rastreamento.
* Registre cada registro JSON em uma linha. 

**Exemplo de registro de log** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Gerar log JSON em conformidade com o esquema  
Para aplicativos Spring, você pode gerar o formato de log JSON esperado usando [estruturas de log](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)comuns, como [logback](http://logback.qos.ch/) e [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Registrar com logback 
Ao usar os iniciadores do Spring boot, o logback é usado por padrão. Para aplicativos logback, use o [logstash-Encoder](https://github.com/logstash/logstash-logback-encoder) para gerar o log formatado em JSON. Esse método tem suporte no Spring boot versão 2.1 +. 

O procedimento:

1. Adicione a dependência logstash em seu arquivo de pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Atualize seu arquivo de configuração logback.xml para definir o formato JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Registrar com log4j2 

Para aplicativos log4j2, use [JSON-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) para gerar o log formatado em JSON. Esse método tem suporte no Spring boot versão 2.1 +.

O procedimento:

1. Excluir `spring-boot-starter-logging` de `spring-boot-starter` , adicionar dependências `spring-boot-starter-log4j2` , `log4j-layout-template-json` no arquivo de pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Prepare um arquivo de modelo de layout JSON jsonTemplate.jsno seu caminho de classe.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Use este modelo de layout JSON em seu arquivo de configuração log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analisar os logs em Log Analytics

Depois que o aplicativo estiver configurado corretamente, o log do console do aplicativo será transmitido para Log Analytics. A estrutura permite uma consulta eficiente em Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Verificar a estrutura do log no Log Analytics
Use este procedimento:
1. Vá para a página Visão geral do serviço da sua instância de serviço.
2. Clique `Logs` na entrada na `Monitoring` seção.
3. Execute esta consulta.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Os logs de aplicativo retornam conforme mostrado na imagem a seguir:

![Exibição de log JSON](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Mostrar entradas de log contendo erros

Para examinar as entradas de log que têm um erro, execute a seguinte consulta:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Use essa consulta para localizar erros ou modificar os termos da consulta para localizar uma classe de exceção específica ou código de erro. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Mostrar entradas de log para um TraceID específico
Para examinar as entradas de log de uma ID de rastreamento específica "trace_id", execute a seguinte consulta:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a consulta de log, consulte Introdução [às consultas de log no Azure monitor](../azure-monitor/logs/get-started-queries.md)