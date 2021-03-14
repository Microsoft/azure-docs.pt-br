---
title: Explorar os logs de rastreamento do Java no Aplicativo Azure insights
description: Pesquisar rastreamentos Log4J ou Logback no Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4013b60a5718de6c196e806f894503f63ec9dafd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593675"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explore os logs de rastreamento de Java no Application Insights

> [!IMPORTANT]
> A abordagem descrita neste documento não é mais recomendada.
>
> A abordagem recomendada para monitorar aplicativos Java é usar a instrumentação automática sem alterar o código. Siga as diretrizes para [Application insights o agente do Java 3,0](./java-in-process-agent.md).

Se você estiver usando Logback ou Log4J (v 1.2 ou 2.0) para rastreamento, você pode enviar seus logs de rastreamento automaticamente para o Application Insights, no qual você pode explorá-los e pesquisar o conteúdo deles.

> [!TIP]
> Você só precisa definir sua chave de instrumentação Application Insights uma vez para seu aplicativo. Se você estiver usando uma estrutura como o Java Spring, talvez já tenha registrado a chave em outro lugar na configuração do aplicativo.

## <a name="using-the-application-insights-java-agent"></a>Usando o agente Java Application Insights

Por padrão, o Application Insights agente Java captura automaticamente o log executado no `WARN` nível e acima.

Você pode alterar o limite de log que é capturado usando o `AI-Agent.xml` arquivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Você pode desabilitar a captura de log do agente Java usando o `AI-Agent.xml` arquivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Como alternativa (em oposição ao uso do agente Java), você pode seguir as instruções abaixo

### <a name="install-the-java-sdk"></a>Instalar o SDK do Java

Siga as instruções para instalar o [SDK do Application Insights para Java][java] se ainda não tiver feito isso.

### <a name="add-logging-libraries-to-your-project"></a>Adicionar bibliotecas de log ao seu projeto
*Escolha o modo apropriado para seu projeto.*

#### <a name="if-youre-using-maven"></a>Se você estiver usando o Maven...
Se o seu projeto já estiver configurado para usar o Maven para compilação, realize a mesclagem de um dos seguintes snippets de código ao seu arquivo pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se você estiver usando o Gradle...
Se seu projeto já está configurado para usar Gradle para compilação, adicione uma das linhas a seguir para o grupo `dependencies` em seu arquivo build.gradle:

Em seguida, atualize as dependências do projeto para obter os binários baixados.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Caso contrário...
Siga as orientações para instalar manualmente o SDK do Java do Application Insights, baixe o jar (após chegar na Página Central do Maven, clique no link para "jar" na seção de download) correspondente ao appender apropriado e adicione-o ao projeto.

| Agente | Baixar | Biblioteca |
| --- | --- | --- |
| Logback |[Jar do appender de Logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar do appender de Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Jar do appender de Log4J v1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Adicionar o appender à sua estrutura de log
Para começar a obter rastreamentos, mescle o snippet de código relevante ao arquivo de configuração Log4J ou Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Os appenders Application Insights podem ser referenciados por qualquer agente configurado e não necessariamente pelo agente raiz (conforme mostrado nos exemplos de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explorar seus rastreamentos no portal do Application Insights
Agora que você configurou o projeto para enviar os rastreamentos para o Application Insights, é possível exibir e pesquisar esses rastreamentos no portal do Application Insights, na folha [Pesquisa][diagnostic].

Exceções enviadas por meio de agentes serão exibidas no portal como Telemetria de exceção.

![No portal do Application Insights, abra a Pesquisa](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Próximas etapas
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

