---
title: Como usar o micrômetro com o Java SDK do Azure Application Insights
description: Um guia passo a passo sobre o uso do Micrômetro com seus aplicativos Spring Insights do Application Insights e Boot que não são do Spring.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: 8dba7280f6abd6026fabdde500dc76b73129d557
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589758"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Como usar o micrometer com o SDK do Java do insights Aplicativo Azure (não recomendado)

> [!IMPORTANT]
> A abordagem descrita neste documento não é mais recomendada.
> 
> A abordagem recomendada para monitorar aplicativos Java é usar a instrumentação automática sem alterar o código. A telemetria micrometer é coletada automaticamente com o Application Insights agente Java 3,0-siga as diretrizes para [Application insights o agente java 3,0](./java-in-process-agent.md).

> [!NOTE]
> Application Insights SDK do Java não dá suporte ao Spring webfluxo, use [Application insights o agente Java 3,0](./java-in-process-agent.md) . 
>
> O webfluxo e o micrometer têm suporte em [Application insights agente do Java 3,0](./java-on-premises.md) que não requer instrumentação. 

O monitoramento de aplicativos de micrômetros mede as métricas para código de aplicativo baseado em JVM e permite exportar os dados para seus sistemas de monitoramento favoritos. Este artigo ensinará como usar o Micrômetro com o Application Insights para aplicativos Spring Boot e não-Spring Boot.

## <a name="using-spring-boot-15x"></a>Usando o Spring Boot 1.5x
Adicione as seguintes dependências ao seu arquivo pom.xml ou build.gradle: 
* [Application insights Spring-boot-Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 ou posterior
* Micrometer Azure Registry 1.1.0 ou superior
* [Micrometry Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 ou superior (este backporta o código autoconfig na estrutura Spring).
* [Recurso do Application Insights](./create-new-resource.md)

Etapas

1. Atualize o arquivo pom.xml do seu aplicativo Spring Boot e inclua as seguintes dependências nele:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Atualize o arquivo application.properties ou yml com a chave Instrumentation do Application Insights usando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Construa seu aplicativo e execute
2. As informações acima devem prepará-lo para o funcionamento com o recolhimento automático de métricas pré-agregadas para o Monitor do Azure. Para obter detalhes sobre como ajustar o iniciador do Spring Insights do Application Insights, consulte o [readme no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Uso do Spring 2.x

Adicione as seguintes dependências ao seu arquivo pom.xml ou build.gradle:

* Application Insights Spring-boot-starter 2.1.2 ou superior
* Azure-Spring-boot-métricas-inicializadores 2.0.7 ou posterior
* [Application Insights recurso](./create-new-resource.md)

Etapas:

1. Atualize o arquivo pom.xml do aplicativo Spring Boot e inclua a seguinte dependência nele:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Atualize o arquivo application.properties ou yml com a chave Instrumentation do Application Insights usando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Construa seu aplicativo e execute
4. As opções acima devem fazer com que você execute automaticamente as medições pré-agregadas coletadas no Azure Monitor. Para obter detalhes sobre como ajustar o iniciador do Spring Insights do Application Insights, consulte o [readme no GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métricas padrão:

*    Métricas configuradas automaticamente para Tomcat, JVM, Métricas de Logback, Métricas de Log4J, Métricas de Tempo de Atividade, Métricas do Processador, FileDescriptorMetrics.
*    Por exemplo, se Netflix Hystrix estiver presente no caminho de classe, obteremos essas métricas também. 
*    As métricas a seguir podem estar disponíveis adicionando os respectivos beans. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metrics 
        - Kafka Metrics 



Como desativar a coleta automática de métricas: 

- Métricas JVM: 
    - management.metrics.binders.jvm.enabled = false 
- Métricas Logback: 
    - management.metrics.binders.logback.enabled = false
- Métricas de tempo de atividade: 
    - management.metrics.binders.uptime.enabled = false 
- Métricas de processador:
    -  Management.Metrics.binders.Processor.Enabled=False 
- FileDescriptorMetrics:
    - Management.Metrics.binders.Files.Enabled=False 
- Métricas de Hystrix se biblioteca no classpath: 
    - Management.Metrics.binders.hystrix.Enabled=False 
- Métricas do AspectJ se biblioteca no classpath: 
    - Spring.AOP.Enabled=False 

> [!NOTE]
> Especifique as propriedades acima no arquivo application.properties ou application.yml do seu aplicativo Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Use micrômetro com aplicativos da web que não sejam do Spring Boot

Adicione as seguintes dependências ao seu arquivo pom.xml ou build.gradle:

* Application Insights Web auto 2.5.0 ou posterior
* Micrometer Azure Registry 1.1.0 ou superior
* [Application Insights recurso](./create-new-resource.md)

Etapas:

1. Inclua as seguintes dependências em seu arquivo pom.xml ou build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Coloque `ApplicationInsights.xml` o arquivo na pasta de recursos:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>

       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>

    </ApplicationInsights>
    ```

3. Classe de servlet de amostra (emite uma métrica de timer):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {

          private static final long serialVersionUID = -4751096228274971485L;

          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {

            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");

        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }

        }

    ```

4. Classe de configuração de amostra:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {

           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {

         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}

             @Override
             public boolean enabled() {
                 return false;
             }
         };

      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);

             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);

           }

           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {

           }
         }
    ```

Para saber mais sobre métricas, consulte a [documentação do Micrometer](https://micrometer.io/docs/).

Outros códigos de exemplo sobre como criar diferentes tipos de métricas podem ser encontrados no[repositório GitHub oficial do micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Como associar a coleção de métricas adicionais

### <a name="springbootspring"></a>SpringBoot/Spring

Crie um bean da respectiva categoria de métrica. Por exemplo, digamos que precisamos de métricas de cache de goiabada:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existem várias métricas que não estão ativadas por padrão, mas podem ser vinculadas da maneira acima. Para obter uma lista completa, consulte [o repositório GitHub oficial do micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplicativos não-Spring
Adicione o seguinte código de ligação ao arquivo de configuração:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o micrometer, consulte a [documentação](https://micrometer.io/docs)oficial do micrometer.
* Para saber mais sobre o Spring no Azure, consulte a documentação oficial do [Spring on Azure](/java/azure/spring-framework/).
