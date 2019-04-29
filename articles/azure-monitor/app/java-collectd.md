---
title: Monitorar o desempenho do aplicativo Web Java no Linux - Azure | Microsoft Docs
description: Monitoramento estendido do desempenho de aplicativo do seu site Java com o plug-in CollectD para Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 783cef6ff4e107838bb3ff7502fb4a8e9189ec3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60897728"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Métricas de desempenho do Linux no Application Insights


Para explorar as métricas de desempenho do sistema Linux no [Application Insights](../../azure-monitor/app/app-insights-overview.md), instale [collectd](https://collectd.org/) com seu plug-in do Application Insights. Essa solução de software livre reúne várias estatísticas de sistema e de rede.

Normalmente, você usará o collectd se já tiver [instrumentado seu serviço Web Java com o Application Insights][java]. Isso oferece a você mais dados para ajudá-lo a aprimorar o desempenho do aplicativo ou para diagnosticar problemas. 

## <a name="get-your-instrumentation-key"></a>Obter a chave de instrumentação
No [Portal do Microsoft Azure](https://portal.azure.com), abra o recurso [Application Insights](../../azure-monitor/app/app-insights-overview.md) onde você quer que os dados sejam exibidos. (Ou [crie um novo recurso](../../azure-monitor/app/create-new-resource.md ).)

Faça uma cópia da chave de instrumentação que identifica o recurso.

![Procure tudo, abra seu recurso e, no menu suspenso Essentials, selecione e copie a Chave de Instrumentação](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalar o plug-in e collectd
Em seus computadores com o servidor Linux:

1. Instale [collectd](https://collectd.org/) versão 5.4.0 ou posterior.
2. Baixe o [plug-in do gravador collectd do Application Insights](https://aka.ms/aijavasdk). Observe o número de versão.
3. Copie o plug-in JAR em `/usr/share/collectd/java`.
4. Edite `/etc/collectd/collectd.conf`:
   * Verifique se [o plug-in do Java](https://collectd.org/wiki/index.php/Plugin:Java) está habilitado.
   * Atualize o JVMArg para java.class.path de modo a incluir o JAR a seguir. Atualize o número de versão para corresponder àquela que você baixou:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione esse snippet usando a chave de instrumentação do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Veja o exemplo de parte de um arquivo de configuração:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configure outros [plug-ins collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), que possam coletar vários dados de diferentes fontes.

Reinicie o collectd, de acordo com seu [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Exibir os dados no Application Insights
No recurso do Application Insights, abra [métricas e adição de gráficos][metrics], selecionando as métricas que você deseja ver na categoria personalizado.

Por padrão, as métricas são agregadas em todos os computadores host dos quais as métricas foram coletadas. Para exibir as métricas por host, na folha Detalhes do gráfico, ative Agrupamento e escolha Agrupar por CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Excluir o carregamento de estatísticas específicas
Por padrão, o plug-in do Application Insights envia todos os dados coletados por todos os plug-ins collect ‘read’ habilitados. 

Para excluir dados de plug-ins ou fontes de dados específicos:

* Edite o arquivo de configuração. 
* Em `<Plugin ApplicationInsightsWriter>`, adicione linhas diretivas como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados coletados pelo plug-in `disk` |
| `Exclude disk:read,write` |Exclua as fontes denominadas `read` e `write` do plug-in `disk`. |

Diretivas separadas por uma nova linha.

## <a name="problems"></a>Problemas?
*Não vejo dados no portal*

* Abra [Pesquisar][diagnostic] para ver se os eventos brutos aparecem. Às vezes, eles levam mais tempo para aparecer no Metrics Explorer.
* Talvez você precise [definir exceções de firewall para dados de saída](../../azure-monitor/app/ip-addresses.md)
* Habilite o rastreamento no plug-in do Application Insights. Adicione esta linha em `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra um terminal e inicie collectd no modo detalhado para ver todos os problemas que ele está reportando:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plug-in de Gravação do Application Insights é incompatível com determinados plugins de Leitura. Alguns plugins às vezes enviam "NaN" onde o plug-in do Application Insights espera um número de ponto flutuante.

Sintoma: O log collectd mostra erros que incluem "AI: ... SyntaxError: Token N inesperado".

Solução alternativa: Exclua dados coletados pelos plugins de Gravação com problema. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


