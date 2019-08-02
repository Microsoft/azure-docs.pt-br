---
title: Análise de aplicativo Web Java com o Azure Application Insights | Microsoft Docs
description: 'Monitoramento de desempenho de aplicativos usando o Application Insights para aplicativos Web Java. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: d3edfa1ca63560f447d2c9ea3da3588e069b7af1
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226834"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Introdução ao Application Insights em um projeto Web Java

[Application Insights](https://azure.microsoft.com/services/application-insights/) é um serviço de análise extensível para desenvolvedores da Web que ajuda você a entender o desempenho e o uso de seu aplicativo em tempo real. Use-o para [instrumentar automaticamente a solicitação, controlar as dependências e coletar contadores de desempenho](auto-collect-dependencies.md#java), diagnosticar problemas de desempenho e exceções e [escrever código][api] para controlar o que os usuários fazem com seu aplicativo. 

![Captura de tela de visão geral de dados de exemplo](./media/java-get-started/overview-graphs.png)

O Application Insights oferece suporte a aplicativos Java em execução no Windows, no Unix ou no Linux.

Você precisa de:

* JRE versão 1.7 ou 1.8
* Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/).

Se você preferir a estrutura Spring tente o [guia para configurar um aplicativo inicializador Spring Boot para usar o Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).
2. Crie um recurso Application Insights. Defina o tipo de aplicativo para aplicativo Web Java.

3. Localize a chave de instrumentação do novo recurso. Você precisará colar essa chave no código de seu projeto em breve.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Adicionar o SDK do Application Insights para Java a seu projeto
*Escolha o modo apropriado para seu projeto.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Se você estiver usando o Maven... <a name="maven-setup" />
Se o seu projeto já estiver configurado para usar o Maven para compilação, realize a mesclagem do código a seguir ao seu arquivo pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Erros de build ou validação de soma de verificação?* Tente usar uma versão específica, como: `<version>2.0.n</version>`. Você encontrará a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou nos [artefatos Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Precisa atualizar para um novo SDK?* Atualize as dependências do seu projeto.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Se você estiver usando o Gradle... <a name="gradle-setup" />
Se o seu projeto já estiver configurado para usar o Gradle para compilação, realize a mesclagem do trecho de código a seguir ao seu arquivo build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Se você está usando o Eclipse para criar um projeto Web dinâmico...
Use o [SDK do Application insights para o plug-in Java][eclipse]. Observação: embora o uso desse plug-in deixe o Application Insights pronto mais rapidamente (supondo que você não esteja usando Maven/Gradle), ele não é um sistema de gerenciamento de dependência. Como tal, o plug-in de atualização não atualizará automaticamente as bibliotecas do Application Insights no seu projeto.

* *Erros de build ou validação de soma de verificação?* Tente usar uma versão específica, como: `version:'2.0.n'`. Você encontrará a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou nos [artefatos Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Para atualizar para um novo SDK* Atualize as dependências do seu projeto.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Caso contrário, se você estiver gerenciando as dependências manualmente...
Baixe a [versão mais recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copie os arquivos necessários para o projeto substituindo as versões anteriores.

### <a name="questions"></a>Perguntas...
* *Qual é a relação entre os componentes `-core` e `-web`?*
  * `applicationinsights-core` fornece a API básica. Você sempre precisa desse componente.
  * `applicationinsights-web` fornece métricas que rastreiam as contagens de solicitação de HTTP e tempos de resposta. Você poderá omitir esse componente se não quiser que a telemetria seja coletada automaticamente. Por exemplo, se quiser escrevê-la você mesmo.
  
* *Como fazer para atualizar o SDK para a versão mais recente?*
  * Se você estiver usando Gradle ou Maven...
    * Atualize seu arquivo de build para especificar a versão mais recente ou use a sintaxe de curinga do Gradle/Maven para incluir a versão mais recente automaticamente. Em seguida, atualize as dependências do projeto. A sintaxe de curinga pode ser vista nos exemplos anteriores para [Gradle](#gradle-setup) ou [Maven](#maven-setup).
  * Se você estiver gerenciando as dependências manualmente...
    * Baixe o [SDK do Application Insights para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) mais recente e substitua os antigos. As alterações descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Adicionar um arquivo Application Insights.xml
Adicione o ApplicationInsights.xml à pasta de recursos em seu projeto; caso contrário, verifique se ele é adicionado ao caminho de classe de implantação do projeto. Copie o XML a seguir nele.

Substitua a chave de instrumentação que você obteve no Portal do Azure.

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

Opcionalmente, o arquivo de configuração pode residir em qualquer local que possa ser acessado pelo aplicativo.  A propriedade do sistema `-Dapplicationinsights.configurationDirectory` especifica o diretório que contém ApplicationInsights.xml. Por exemplo, um arquivo de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.
* O componente de solicitação HTTP é opcional. Ele envia automaticamente a telemetria sobre solicitações e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de solicitação HTTP. Ele atribui um identificador a cada solicitação recebida pelo servidor e adiciona esse identificador como uma propriedade para cada item de telemetria, como a propriedade “Operation.Id”. Ele permite que você correlacione a telemetria associada com cada solicitação, definindo um filtro na [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas para definir a chave de instrumentação
O SDK do Application Insights procura a chave nesta ordem:

1. Propriedade do sistema: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. Variável de ambiente: APPLICATION_INSIGHTS_IKEY
3. Arquivo de configuração: ApplicationInsights.xml

Você também pode [defini-lo no código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

Observe que as [métricas em tempo real](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) não dão suporte à leitura da chave de instrumentação do código.

## <a name="4-add-an-http-filter"></a>4. Adicionar um filtro HTTP
A última etapa de configuração permite que o componente de solicitação HTTP registre cada solicitação da Web. (Não obrigatório se você quiser apenas a API vazia.)

### <a name="spring-boot-applications"></a>Aplicativos do Spring Boot
Registre o `WebRequestTrackingFilter` do Application Insights na sua classe de Configuração:

```Java
package <yourpackagename>.configurations;

import javax.servlet.Filter;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import com.microsoft.applicationinsights.TelemetryConfiguration;
import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;

@Configuration
public class AppInsightsConfig {

    @Bean
    public String telemetryConfig() {
        String telemetryKey = System.getenv("<instrumentation key>");
        if (telemetryKey != null) {
            TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
        }
        return telemetryKey;
    }

    /**
     * Programmatically registers a FilterRegistrationBean to register WebRequestTrackingFilter
     * @param webRequestTrackingFilter
     * @return Bean of type {@link FilterRegistrationBean}
     */
    @Bean
    public FilterRegistrationBean webRequestTrackingFilterRegistrationBean(WebRequestTrackingFilter webRequestTrackingFilter) {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(webRequestTrackingFilter);
        registration.addUrlPatterns("/*");
        registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
        return registration;
    }


    /**
     * Creates bean of type WebRequestTrackingFilter for request tracking
     * @param applicationName Name of the application to bind filter to
     * @return {@link Bean} of type {@link WebRequestTrackingFilter}
     */
    @Bean
    @ConditionalOnMissingBean

    public WebRequestTrackingFilter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
        return new WebRequestTrackingFilter(applicationName);
    }


}
```

> [!NOTE]
> Se estiver usando o Spring Boot 1.3.8 ou mais antigo, substitua o FilterRegistrationBean pela linha abaixo

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

Essa classe configurará o `WebRequestTrackingFilter` para ser o primeiro filtro na cadeia de filtro de http. Ela também obterá a chave de instrumentação da variável de ambiente do sistema operacional caso esteja disponível.

> Estamos usando a configuração de filtro http da Web em vez da configuração de Spring MVC porque este é um aplicativo Spring Boot e tem sua própria configuração Spring MVC. Consulte as seções abaixo para configuração específica de Spring MVC.

### <a name="applications-using-webxml"></a>Aplicativos que usam o web.xml
Localize e abra o arquivo web.xml em seu projeto. Em seguida, mescle o código a seguir com o nó do aplicativo Web no qual seus filtros de aplicativo estão configurados.

Para obter os resultados mais precisos, o filtro deve ser mapeado antes de todos os outros filtros.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

   <!-- This listener handles shutting down the TelemetryClient when an application/servlet is undeployed. -->
    <listener>
      <listener-class>com.microsoft.applicationinsights.web.internal.ApplicationInsightsServletContextListener</listener-class>
    </listener>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Se você estiver usando Spring Web MVC 3.1 ou posterior
Edite estes elementos em *-servlet.xml para incluir o pacote do Application Insights:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Se você estiver usando o Struts 2
Adicione este item ao arquivo de configuração do Struts (geralmente chamado de struts.xml ou struts-default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Se você tem interceptores definidos em uma pilha padrão, o interceptor pode ser adicionado a essa pilha.

## <a name="5-run-your-application"></a>5. Execute seu aplicativo.
Execute-o no modo de depuração no computador de desenvolvimento ou publique em seu servidor.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Exibir sua telemetria no Application Insights
Retorne para seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Dados de solicitações HTTP são exibidos na folha de visão geral. (Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Captura de tela de visão geral de dados de exemplo](./media/java-get-started/overview-graphs.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![Painel de falhas Application Insights com gráficos](./media/java-get-started/006-barcharts.png)

> O Application Insights presume que o formato de solicitações HTTP para aplicativos MVC seja: `VERB controller/action`. Por exemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` são agrupados em `GET Home/Product`. Esse agrupamento habilita agregações significativas de solicitações, como o número de solicitações e o tempo médio de execução para solicitações.
>
>

### <a name="instance-data"></a>Dados de instância
Clique em um tipo de solicitação específica para ver instâncias individuais.

![Analisar uma exibição de exemplo específica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: Linguagem de consulta poderosa
À medida que acumular mais dados, você poderá executar consultas para agregar dados e localizar instâncias individuais.  [Análise](../../azure-monitor/app/analytics.md) é uma ferramenta poderosa para entender o desempenho e o uso e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalar aplicativo no servidor
Agora, publique seu aplicativo no servidor, permita que as pessoas o usem e observe a telemetria mostrada no portal.

* Verifique se o firewall permite que seu aplicativo envie telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída precisar passar por um firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores Windows, instale:

  * [Microsoft Visual C++ redistribuível](https://www.microsoft.com/download/details.aspx?id=40784)

    (Esse componente habilita contadores de desempenho.)

## <a name="azure-app-service-config-spring-boot"></a>Configuração do serviço de Azure App (Spring boot)

Os aplicativos Spring boot em execução no Windows exigem configuração adicional para serem executados em serviços Azure Apps. Modifique **Web. config** e adicione o seguinte:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Falhas de solicitação e exceções
As exceções sem tratamento são coletadas automaticamente.

Para coletar dados em outras exceções, você tem duas opções:

* [Inserir chamadas para trackexception () em seu código][apiexceptions].
* [Instalar o Agente Java em seu servidor](java-agent.md). Especifique os métodos que deseja inspecionar.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registrar métodos internos especificados e chamadas feitas por meio de JDBC, com dados de tempo.

## <a name="w3c-distributed-tracing"></a>Rastreamento distribuído do W3C

O SDK do Java do Application Insights passou a dar suporte ao [rastreamento distribuído do W3C](https://w3c.github.io/trace-context/).

A configuração do SDK de entrada será mais bem explicada em nosso artigo sobre [correlação](correlation.md#w3c-distributed-tracing).

A configuração do SDK de saída é definida no arquivo [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>contadores de desempenho
Abra **investigar**, **métricas**, para ver um intervalo de contadores de desempenho.

![Captura de tela do painel de métricas com bytes particulares de processo selecionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a coleta do contador de desempenho
Para desabilitar a coleta do conjunto padrão de contadores de desempenho, adicione o seguinte trecho no nó raiz do arquivo ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Coletar contadores de desempenho adicionais
Você pode especificar contadores de desempenho adicionais a serem coletados.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos pela Máquina Virtual Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – o nome exibido no portal do Application Insights.
* `objectName` – o nome do objeto JMX.
* `attribute` – o atributo do nome do objeto JMX a buscar
* `type` (opcional) - o tipo do atributo do objeto JMX:
  * Padrão: um tipo simples como “int” ou “long”.
  * `composite`: os dados do contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados do contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (do mesmo modo que um campo é um membro de uma classe). Categorias podem ser globais, ou podem ter instâncias numeradas ou nomeadas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName - o nome exibido no portal do Application Insights.
* categoryName – a categoria de contador de desempenho (objeto de desempenho) a qual este contador de desempenho está associado
* counterName – o nome do contador de desempenho
* instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia de caracteres vazia (""), se a categoria contém uma única instância. Se categoryName é o processo, e o contador de desempenho que você gostaria de coletar faz parte do processo atual da JVM em que seu aplicativo está sendo executado, especifique `"__SELF__"`.

### <a name="unix-performance-counters"></a>Contadores de desempenho do Unix
* [Instale o collectd com o plug-in do Application Insights](java-collectd.md) para obter uma ampla variedade de dados de sistema e rede.

## <a name="get-user-and-session-data"></a>Obter dados de usuário e de sessão
OK, você está enviando a telemetria do seu servidor Web. Agora, para ver o panorama completo do seu aplicativo, você pode adicionar um monitoramento mais:

* [Adicione telemetria às suas páginas da Web][usage] para monitorar exibições de página e métricas de usuário.
* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.

## <a name="capture-log-traces"></a>Capturar rastreamentos de log
Você pode usar o Application Insights para fracionar e dividir logs de Log4J, Logback ou outras estruturas de registros. Você pode correlacionar os logs de solicitações HTTP e outras telemetrias. [Saiba como][javalogs].

## <a name="send-your-own-telemetry"></a>Enviar sua própria telemetria
Agora que você instalou o SDK, você pode usar a API para enviar sua próprias telemetrias.

* [Acompanhe eventos e métricas personalizados][api] para saber o que os usuários estão fazendo com seu aplicativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes de disponibilidade na Web
O Application Insights pode testar seu site em intervalos regulares para verificar ele está operante e respondendo bem.

[Saiba mais sobre como configurar testes da Web de disponibilidade.][availability]

## <a name="questions-problems"></a>Perguntas? Problemas?
[Solucionar problemas de Java](java-troubleshoot.md)

## <a name="next-steps"></a>Próximas etapas
* [Monitorar chamadas de dependência](java-agent.md)
* [Monitorar os contadores de desempenho do Unix](java-collectd.md)
* Adicionar [monitoramento a suas páginas da Web](javascript.md) para monitorar tempos de carregamento de página, chamadas AJAX e exceções do navegador.
* Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) para controlar o uso no navegador ou no servidor.
* Usar [Análise](../../azure-monitor/app/analytics.md) para consultas avançadas por telemetria a o aplicativo
* Para saber mais, visite [Azure para desenvolvedores Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: ../../azure-monitor/learn/java-quick-start.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
