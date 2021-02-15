---
title: Azure Monitor Application Insights Java
description: Monitoramento do desempenho de aplicativos para aplicativos Java em execução em qualquer ambiente sem a necessidade de modificação de código. Rastreamento distribuído e mapa de aplicativos.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 811827c1053349d4fa80a25e5cf362331e5d87bc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383170"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Azure Monitor de monitoramento de aplicativos com código Java Application Insights

O monitoramento de aplicativos sem código Java é bastante simples – não há alterações de código e o agente Java pode ser habilitado com apenas algumas alterações de configuração.

 O agente do Java funciona em qualquer ambiente e permite que você monitore todos os seus aplicativos Java. Em outras palavras, se você estiver executando seus aplicativos Java em VMs, no local, no AKS, no Windows, no Linux, você deve nomeá-lo, o agente do Java 3,0 monitorará seu aplicativo.

Adicionar o SDK do Java Application Insights ao seu aplicativo não é mais necessário, pois o agente 3,0 coleta automaticamente solicitações, dependências e logs por conta própria.

Você ainda pode enviar telemetria personalizada do seu aplicativo. O agente 3,0 irá rastreá-lo e correlacioná-lo junto com toda a telemetria coletada automaticamente.

O agente 3,0 dá suporte a Java 8 e superior.

## <a name="quickstart"></a>Início Rápido

**1. baixar o agente**

> [!WARNING]
> **Se você estiver atualizando da versão prévia do 3,0**
>
> Examine todas as [Opções de configuração](./java-standalone-config.md) com cuidado, pois a estrutura JSON foi completamente alterada, além do próprio nome de arquivo, que ficou com todas as letras minúsculas.

Baixe o [applicationinsights-Agent-3.0.2. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar)

**2. aponte a JVM para o agente**

Adicionar `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` aos argumentos JVM de seu aplicativo

Os argumentos de JVM típicos incluem `-Xmx512m` e `-XX:+UseG1GC` . Portanto, se você souber onde adicioná-los, já saberá onde adicioná-los.

Para obter ajuda adicional sobre como configurar os argumentos JVM de seu aplicativo, consulte [dicas para atualizar seus argumentos JVM](./java-standalone-arguments.md).

**3. aponte o agente para o recurso de Application Insights**

Se você ainda não tiver um recurso de Application Insights, poderá criar um novo seguindo as etapas no guia de criação de [recursos](./create-new-resource.md).

Aponte o agente para o recurso de Application Insights, definindo uma variável de ambiente:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Ou criando um arquivo de configuração chamado `applicationinsights.json` e colocando-o no mesmo diretório que `applicationinsights-agent-3.0.2.jar` o, com o seguinte conteúdo:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Você pode encontrar a cadeia de conexão em seu recurso de Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights cadeia de conexão":::

**4. isso é tudo!**

Agora, inicie seu aplicativo e vá para o recurso de Application Insights no portal do Azure para ver os dados de monitoramento.

> [!NOTE]
> Pode levar alguns minutos para que os dados de monitoramento sejam exibidos no Portal.


## <a name="configuration-options"></a>Opções de configuração

No `applicationinsights.json` arquivo, você também pode configurar:

* Nome da função de nuvem
* Instância de função de nuvem
* amostragem
* Métricas JMX
* Dimensões personalizadas
* Processadores de telemetria (visualização)
* Registro em log coletado automaticamente
* Métricas de micrometer coletadas automaticamente (incluindo as métricas do acionador do Spring boot)
* Pulsação
* Proxy HTTP
* Autodiagnóstico

Consulte [Opções de configuração](./java-standalone-config.md) para obter detalhes completos.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Solicitações, dependências, logs e métricas coletados automaticamente

### <a name="requests"></a>Requests

* Consumidores JMS
* Consumidores de Kafka
* De sub-rede/webfluxo
* Servlets
* Agendamento Spring

### <a name="dependencies-with-distributed-trace-propagation"></a>Dependências com propagação de rastreamento distribuído

* Apache HttpClient e HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Cliente de sub-rede
* OkHttp

### <a name="other-dependencies"></a>Outras dependências

* Cassandra
* JDBC
* MongoDB (assíncrono e sincronizado)
* Redis (lettuce e Jedis)

### <a name="logs"></a>Logs

* java.util.logging
* Log4J (incluindo propriedades MDC)
* SLF4J/Logback (incluindo propriedades MDC)

### <a name="metrics"></a>Métricas

* Micrometer (incluindo as métricas do acionador do Spring boot)
* Métricas JMX

## <a name="send-custom-telemetry-from-your-application"></a>Enviar telemetria personalizada do seu aplicativo

Nossa meta no 3.0 + é permitir que você envie sua telemetria personalizada usando APIs padrão.

Damos suporte a micrometer, estruturas de log populares e o SDK Application Insights Java 2. x até agora.
Application Insights Java 3,0 captura automaticamente a telemetria enviada por essas APIs e a correlaciona com telemetria coletada automaticamente.

### <a name="supported-custom-telemetry"></a>Telemetria personalizada com suporte

A tabela a seguir representa os tipos de telemetria personalizados com suporte no momento que você pode habilitar para complementar o agente do Java 3,0. Para resumir, há suporte para métricas personalizadas por meio de micrometer, exceções e rastreamentos personalizados podem ser habilitados por meio de estruturas de log e qualquer tipo de telemetria personalizada tem suporte por meio do [SDK Application insights Java 2. x](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrometer | Log4J, logback, JUL | SDK 2. x |
|---------------------|------------|---------------------|---------|
| **Eventos personalizados**   |            |                     |  Sim    |
| **Métricas personalizadas**  |  Sim       |                     |  Sim    |
| **Dependências**    |            |                     |  Sim    |
| **Exceções**      |            |  Sim                |  Sim    |
| **Exibições de página**      |            |                     |  Sim    |
| **Solicitações**        |            |                     |  Sim    |
| **Rastreamentos**          |            |  Sim                |  Sim    |

Não estamos planejando lançar um SDK com o Application Insights 3,0 no momento.

Application Insights Java 3,0 já está escutando a telemetria que é enviada para o SDK Application Insights Java 2. x. Essa funcionalidade é uma parte importante da história de atualização para usuários existentes de 2. x e preenche uma lacuna importante em nosso suporte de telemetria personalizado até que a API OpenTelemetry seja GA.

### <a name="send-custom-metrics-using-micrometer"></a>Enviar métricas personalizadas usando micrometer

Adicione micrometer ao seu aplicativo:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Use o [registro global](https://micrometer.io/docs/concepts#_global_registry) micrometer para criar um medidor:

```java
static final Counter counter = Metrics.counter("test_counter");
```

e usá-lo para registrar as métricas:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Enviar rastreamentos e exceções personalizados usando sua estrutura de log favorita

Log4J, Logback e Java. util. Logging são instrumentados automaticamente e o registro em log realizado por meio dessas estruturas de log é coletado automaticamente como telemetria de rastreamento e exceção.

Por padrão, o registro em log só é coletado quando esse log é executado no nível de informações ou acima.
Consulte as [Opções de configuração](./java-standalone-config.md#auto-collected-logging) para saber como alterar esse nível.

Se você quiser anexar dimensões personalizadas a seus logs, você pode usar [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4J 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)ou [Logback MDC](http://logback.qos.ch/manual/mdc.html)e Application insights o Java 3,0 irá capturar automaticamente essas propriedades MDC como dimensões personalizadas em seu rastreamento e telemetria de exceção.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Enviar telemetria personalizada usando o SDK 2. x

Adicione `applicationinsights-core-2.6.2.jar` ao seu aplicativo (todas as versões 2. x têm suporte pelo Application insights Java 3,0, mas vale a pena usar a mais recente se você tiver uma opção):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Criar um TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usá-lo para enviar telemetria personalizada:

##### <a name="events"></a>Eventos

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Métricas

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Dependências

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Logs

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Exceções

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Adicionar dimensões personalizadas de solicitação usando o SDK 2. x

> [!NOTE]
> Esse recurso só está no 3.0.2 e posterior

Adicione `applicationinsights-web-2.6.2.jar` ao seu aplicativo (todas as versões 2. x têm suporte pelo Application insights Java 3,0, mas vale a pena usar a mais recente se você tiver uma opção):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e adicione dimensões personalizadas em seu código:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Definir o user_Id de telemetria de solicitação usando o SDK 2. x

> [!NOTE]
> Esse recurso só está no 3.0.2 e posterior

Adicione `applicationinsights-web-2.6.2.jar` ao seu aplicativo (todas as versões 2. x têm suporte pelo Application insights Java 3,0, mas vale a pena usar a mais recente se você tiver uma opção):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e defina o `user_Id` em seu código:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Substituir o nome da telemetria de solicitação usando o SDK 2. x

> [!NOTE]
> Esse recurso só está no 3.0.2 e posterior

Adicione `applicationinsights-web-2.6.2.jar` ao seu aplicativo (todas as versões 2. x têm suporte pelo Application insights Java 3,0, mas vale a pena usar a mais recente se você tiver uma opção):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e defina o nome em seu código:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```
