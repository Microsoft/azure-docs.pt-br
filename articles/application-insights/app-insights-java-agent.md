---
title: Monitoramento de desempenho de aplicativos Web de Java no Azure Application Insights | Microsoft Docs
description: Desempenho e monitoramento de uso estendidos do seu site Java com o Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: bwren
ms.openlocfilehash: 4e56998382610ad3d7224e6a8de5aee5419ebe43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Monitorar dependências, exceções e tempos de execução em aplicativos Web em Java


Se você [instrumentou seu aplicativo Web em Java com o Application Insights][java], será possível usar o Agente Java para obter uma visão mais aprofundada, sem nenhuma alteração de código:

* **Dependências:** dados sobre chamadas de seu aplicativo a outros componentes, incluindo:
  * **Chamadas REST** feitas por meio de HttpClient, OkHttp e RestTemplate (Spring).
  * **Redis** feitas por meio do cliente Jedis. Se a chamada demorar mais de 10 segundos, o agente também buscará os argumentos da chamada.
  * **[Chamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** – MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB ou Apache Derby DB. Há suporte para chamadas “executeBatch”. Para MySQL e PostgreSQL, se a chamada levar mais de 10 segundos, o agente relatará o plano de consulta.
* **Exceções capturadas:** dados sobre exceções que são manipuladas pelo código.
* **Tempo de execução do método:** dados sobre o tempo necessário para executar métodos específicos.

Para usar o agente Java, instale-o no servidor. Seus aplicativos Web devem ser instrumentados com o [SDK do Java do Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente do Application Insights para Java
1. No computador que está executando o servidor Java, [baixe o agente](https://aka.ms/aijavasdk).
2. Edite o script de inicialização do servidor de aplicativos e adicione a seguinte JVM:
   
    `javaagent:`*caminho completo para o arquivo JAR do agente*
   
    Por exemplo, no Tomcat em um computador Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicativos.

## <a name="configure-the-agent"></a>Configurar o agente
Crie um arquivo chamado `AI-Agent.xml` e coloque-o na mesma pasta que o arquivo JAR do agente.

Defina o conteúdo do arquivo xml. Edite o exemplo a seguir para incluir ou omitir os recursos desejados.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Você precisa habilitar a exceção de relatórios e o tempo de método para métodos individuais.

Por padrão, `reportExecutionTime` é true e `reportCaughtExceptions` é false.

## <a name="view-the-data"></a>Exibir os dados
No recurso do Application Insights, a dependência remota e os tempos de execução do método agregados aparecem [no bloco Desempenho][metrics].

Para procurar instâncias individuais de dependência, exceções e relatórios de método, abra [Pesquisar][diagnostic].

[Diagnosticando problemas de dependência – Saiba mais](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Perguntas? Problemas?
* Não há dados? [Definir exceções de firewall](app-insights-ip-addresses.md)
* [Solucionar problemas de Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
