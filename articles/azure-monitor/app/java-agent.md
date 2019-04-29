---
title: Monitoramento de desempenho de aplicativos Web de Java no Azure Application Insights | Microsoft Docs
description: Desempenho e monitoramento de uso estendidos do seu site Java com o Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900601"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorar dependências, exceções e métodos de tempos de execução em aplicativos Web em Java


Se você [instrumentou seu aplicativo Web em Java com o Application Insights][java], será possível usar o Agente Java para obter uma visão mais aprofundada, sem nenhuma alteração de código:

* **Dependências:** Dados sobre chamadas de seu aplicativo a outros componentes, incluindo:
  * **Chamadas REST** feitas por meio de HttpClient, OkHttp e RestTemplate (Spring) são capturadas.
  * **Redis** feitas por meio do cliente Jedis são capturadas.
  * **[Chamadas JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  - comandos MySQL, SQL Server e Oracle DB são automaticamente capturados. Para MySQL, se a chamada levar mais de 10 segundos, o agente relatará o plano de consulta.
* **Exceções capturadas:** Informações sobre exceções que são manipuladas pelo seu código.
* **Tempo de execução do método:** Informações sobre o tempo necessário para executar métodos específicos.

Para usar o agente Java, instale-o no servidor. Seus aplicativos Web devem ser instrumentados com o [SDK do Java do Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente do Application Insights para Java
1. No computador que está executando o servidor Java, [baixe o agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Certifique-se de baixar a mesma versão do Agente Java que o núcleo e os pacotes da Web do SDK de Java do Application Insights.
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

## <a name="additional-config-spring-boot"></a>Configuração adicional (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para serviços de aplicativo do Azure faça o seguinte:

* Selecione Configurações > Configurações do Aplicativo
* Em configurações do aplicativo, adicione um novo par de chave/valor:

Chave: `JAVA_OPTS` Valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Para obter a versão mais recente do agente Java verificar as versões [aqui](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

O agente deve ser empacotado como um recurso em seu projeto, de modo que ele acaba em d: / home/site/wwwroot/diretório. Você pode confirmar que o agente está no diretório correto do serviço de aplicativo acessando **ferramentas de desenvolvimento** > **ferramentas avançadas** > **Console de depuração**e examinando o conteúdo do diretório de sites.    

* Salvar as configurações e reinicie o aplicativo. (Essas etapas se aplicam somente aos serviços de aplicativos em execução no Windows.)

> [!NOTE]
> IA-Agent.xml e o arquivo jar do agente devem estar na mesma pasta. Frequentemente, eles são colocados juntos na pasta `/resources` do projeto.  

### <a name="spring-rest-template"></a>Modelo REST do Spring

Para que o Application Insights instrumente com êxito as chamadas HTTP feitas com o Modelo REST do Spring, o uso do Cliente Apache HTTP é obrigatório. Por padrão, o Modelo REST do Spring não está configurado para usar o Cliente Apache HTTP. Ao especificar [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) no construtor de um Modelo REST do Spring, ele usará o Apache HTTP.

Veja a seguir um exemplo de como fazer isso com o Spring Beans. Esse é um exemplo muito simples que usa as configurações padrão da classe de alocador.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Habilite o rastreamento distribuído do W3C

Adicione o seguinte ao AI-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade com versões anteriores está habilitado por padrão e o parâmetro enableW3CBackCompat é opcional e deve ser usado apenas quando você quiser desativá-lo. 

Idealmente, esse seria o caso quando todos os seus serviços fossem atualizados para a versão mais recente dos SDKs com suporte para o protocolo W3C. É altamente recomendável migrar para a versão mais recente dos SDKs do W3C com suporte assim que possível.

Verifique se **ambas as configurações de [entrada](correlation.md#w3c-distributed-tracing) e saídas (agente)** são exatamente as mesmas.

## <a name="view-the-data"></a>Exibir os dados
No recurso do Application Insights, a dependência remota e os tempos de execução do método agregados aparecem [no bloco Desempenho][metrics].

Para procurar instâncias individuais de dependência, exceções e relatórios de método, abra [Pesquisar][diagnostic].

[Diagnosticando problemas de dependência – Saiba mais](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Perguntas? Problemas?
* Não há dados? [Definir exceções de firewall](../../azure-monitor/app/ip-addresses.md)
* [Solucionar problemas de Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
