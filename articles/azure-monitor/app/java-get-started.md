---
title: 'Início rápido: análise de aplicativo Web Java com o Aplicativo Azure insights'
description: 'Monitoramento de desempenho de aplicativos usando o Application Insights para aplicativos Web Java. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 115e1ec347cdcd80904b47a0c8798206360d0dad
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131773"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Guia de Início Rápido: Introdução ao Application Insights em um projeto Web Java


> [!CAUTION]
> A partir de novembro de 2020, para monitorar aplicativos Java, recomendamos a instrumentação automática usando o Azure Monitor Application Insights o agente Java 3,0. Para obter mais informações sobre como começar, consulte [Application insights o agente do Java 3,0](./java-in-process-agent.md).

Neste guia de início rápido, você usa Application Insights SDK para instrumentar solicitação, controlar dependências e coletar contadores de desempenho, diagnosticar problemas de desempenho e exceções e escrever código para controlar o que os usuários fazem com seu aplicativo.

Application Insights é um serviço de análise extensível para desenvolvedores da Web que ajuda você a entender o desempenho e o uso de seu aplicativo em tempo real. O Application Insights oferece suporte a aplicativos Java em execução no Windows, no Unix ou no Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Um aplicativo Java em funcionamento.

## <a name="get-an-application-insights-instrumentation-key"></a>Obter uma chave de instrumentação do Application Insights

> [!IMPORTANT]
> Novas regiões do Azure **exigem** o uso de cadeias de conexão em vez de chaves de instrumentação. A [cadeia de conexão](./sdk-connection-string.md?tabs=java) identifica o recurso ao qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.
1. Entre no [portal do Azure](https://portal.azure.com/).
2. No portal do Azure, crie um recurso Application Insights. Defina o tipo de aplicativo para aplicativo Web Java.

3. Localize a chave de instrumentação do novo recurso. Você precisará colar essa chave no código de seu projeto em breve.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Adicionar o SDK do Application Insights para Java a seu projeto

*Escolha o tipo de projeto.*

# <a name="maven"></a>[Maven](#tab/maven)

Se o seu projeto já estiver configurado para usar o Maven para compilação, mescle o código a seguir ao arquivo de *pom.xml* .

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Se o projeto já estiver configurado para usar o gradle para compilação, mescle o código a seguir ao arquivo *Build. gradle* .

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>Perguntas
* *Qual é a relação entre os `-web-auto` `-web` componentes e `-core` ?*
  * `applicationinsights-web-auto` fornece métricas que rastreiam contagens de solicitação e tempos de resposta do servlet HTTP, registrando automaticamente o filtro de Application Insights servlet em tempo de execução.
  * `applicationinsights-web` também fornece métricas que controlam as contagens de solicitação e os tempos de resposta do servlet HTTP, mas exigem o registro manual do filtro de Application Insights servlet em seu aplicativo.
  * `applicationinsights-core` oferece apenas a API Bare, por exemplo, se seu aplicativo não for baseado em servlet.
  
* *Como fazer para atualizar o SDK para a versão mais recente?*
  * A partir de novembro de 2020, para monitorar aplicativos Java, recomendamos a instrumentação automática usando o Azure Monitor Application Insights o agente Java 3,0. Para obter mais informações sobre como começar, consulte [Application insights o agente do Java 3,0](./java-in-process-agent.md).

## <a name="add-an-applicationinsightsxml-file"></a>Adicionar um arquivo de *ApplicationInsights.xml*
Adicione *ApplicationInsights.xml* à pasta de recursos em seu projeto ou verifique se ele foi adicionado ao caminho de classe de implantação do seu projeto. Copie o XML a seguir nele.

Substitua a chave de instrumentação por aquela que você obteve da portal do Azure.

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

Opcionalmente, o arquivo de configuração pode estar em qualquer local acessível ao seu aplicativo.  A propriedade System `-Dapplicationinsights.configurationDirectory` especifica o diretório que contém *ApplicationInsights.xml*. Por exemplo, um arquivo de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.
* O componente de solicitação HTTP é opcional. Ele envia automaticamente a telemetria sobre solicitações e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de solicitação HTTP. Ele atribui um identificador a cada solicitação recebida pelo servidor. Em seguida, ele adiciona esse identificador como uma propriedade a cada item de telemetria como a propriedade ' Operation.Id '. Ele permite que você correlacione a telemetria associada com cada solicitação, definindo um filtro na [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas para definir a chave de instrumentação
O SDK do Application Insights procura a chave nesta ordem:

1. Propriedade do sistema:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Variável de ambiente: APPINSIGHTS_INSTRUMENTATIONKEY
3. Arquivo de configuração: *ApplicationInsights.xml*

Você também pode [defini-lo no código](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Adicionar agente

[Instale o agente Java](java-agent.md) para capturar chamadas http de saída, consultas JDBC, log de aplicativo e melhor nomenclatura de operação.

## <a name="run-your-application"></a>Execute seu aplicativo.
Execute-o no modo de depuração no computador de desenvolvimento ou publique em seu servidor.

## <a name="view-your-telemetry-in-application-insights"></a>Exibir sua telemetria no Application Insights
Retorne para seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Dados de solicitações HTTP são exibidos na folha de visão geral. (Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Captura de tela de visão geral de dados de exemplo](./media/java-get-started/overview-graphs.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![Painel de falhas Application Insights com gráficos](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>Dados de instância
Clique em um tipo de solicitação específica para ver instâncias individuais.

![Analisar uma exibição de exemplo específica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Análise: linguagem de consulta poderosa
À medida que acumular mais dados, você poderá executar consultas para agregar dados e localizar instâncias individuais.  [Análise](../log-query/log-query-overview.md) é uma ferramenta poderosa para entender o desempenho e o uso e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Instalar aplicativo no servidor
Agora, publique seu aplicativo no servidor, permita que as pessoas o usem e observe a telemetria mostrada no portal.

* Verifique se o firewall permite que seu aplicativo envie telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída precisar passar por um firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores Windows, instale:

  * [Microsoft Visual C++ redistribuível](https://www.microsoft.com/download/details.aspx?id=40784)

    (Esse componente habilita contadores de desempenho.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App serviço, AKS, configuração de VMs

A melhor e mais fácil abordagem para monitorar seus aplicativos em execução em qualquer um dos provedores de recursos do Azure é usar Application Insights instrumentação automática por meio do [agente do Java 3,0](./java-in-process-agent.md).


## <a name="exceptions-and-request-failures"></a>Falhas de solicitação e exceções
As exceções não tratadas e as falhas de solicitação são coletadas automaticamente pelo filtro da Web Application Insights.

Para coletar dados sobre outras exceções, você pode [inserir chamadas para trackexception () em seu código][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registrar métodos internos especificados e chamadas feitas por meio de JDBC, com dados de tempo.

E para a nomenclatura de operação automática.

## <a name="w3c-distributed-tracing"></a>Rastreamento distribuído do W3C

O SDK do Java do Application Insights passou a dar suporte ao [rastreamento distribuído do W3C](https://w3c.github.io/trace-context/).

A configuração do SDK de entrada será mais bem explicada em nosso artigo sobre [correlação](correlation.md).

A configuração do SDK de saída é definida no arquivo [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Contadores de desempenho
Abra **investigar**, **métricas**, para ver um intervalo de contadores de desempenho.

![Captura de tela do painel de métricas com bytes particulares de processo selecionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a coleta do contador de desempenho
Para desabilitar a coleta do conjunto padrão de contadores de desempenho, adicione o seguinte código sob o nó raiz do arquivo de *ApplicationInsights.xml* :

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
* `type` (opcional)-o tipo de atributo do objeto JMX:
  * Padrão: um tipo simples como “int” ou “long”.
  * `composite`: os dados do contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados do contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](/windows/win32/perfctrs/performance-counters-portal) é membro de uma categoria (do mesmo modo que um campo é um membro de uma classe). Categorias podem ser globais, ou podem ter instâncias numeradas ou nomeadas.

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

## <a name="send-your-own-telemetry"></a>Enviar sua própria telemetria
Agora que você instalou o SDK, você pode usar a API para enviar sua próprias telemetrias.

* [Acompanhe eventos personalizados e métricas][api] para saber o que os usuários estão fazendo com seu aplicativo.
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
* Gravar [telemetria personalizada](./api-custom-events-metrics.md) para controlar o uso no navegador ou no servidor.
* Use a  [análise](../log-query/log-query-overview.md) para consultas poderosas sobre a telemetria do seu aplicativo
* Para saber mais, visite [Azure para desenvolvedores Java](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md
[usage]: javascript.md