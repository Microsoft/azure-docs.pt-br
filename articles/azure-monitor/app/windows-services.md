---
title: Application Insights do Azure para funções de trabalho e de servidor do Windows | Microsoft Docs
description: Adicione manualmente o SDK do Application Insights ao aplicativo ASP.NET para analisar o uso, a disponibilidade e o desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 85764c0ee5b8ed117fb191657d54abe5bd10a703
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784360"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Configurar manualmente o Application Insights para aplicativos .NET

Você pode configurar o [Application Insights](../../azure-monitor/app/app-insights-overview.md) para monitorar uma ampla variedade de aplicativos ou funções de aplicativo, componentes ou microsserviços. Para aplicativos Web e serviços, o Visual Studio oferece [configuração em uma etapa](../../azure-monitor/app/asp-net.md). Para outros tipos de aplicativos .NET, como funções de servidor de back-end ou aplicativos de área de trabalho, você pode configurar o Application Insights manualmente.

![Gráficos de exemplo de monitoramento de desempenho](./media/windows-services/10-perf.png)

#### <a name="before-you-start"></a>Antes de começar

Você precisa de:

* Uma assinatura do [Microsoft Azure](https://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](https://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a>1. Escolher um recurso Application Insights

O ‘recurso’ é onde os dados são coletados e exibidos no portal do Azure. Você precisa decidir se deseja criar um recurso novo ou compartilhar um existente.

### <a name="part-of-a-larger-app-use-existing-resource"></a>Parte de um aplicativo maior: Usar o recurso existente

Se seu aplicativo Web tem vários componentes, por exemplo, um aplicativo Web de front-end e um ou mais serviços de back-end, você deve enviar telemetria de todos os componentes para o mesmo recurso. Isso permite que sejam exibidos em um mapa de aplicativo único e possibilita rastrear uma solicitação de um componente para outro.

Assim, se você já estiver monitorando outros componentes desse aplicativo, use o mesmo recurso.

Abra o grupo de recursos no [portal do Azure](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Aplicativo autossuficiente: Criar um novo recurso

Se o novo aplicativo não está relacionado a outros aplicativos, ele deve ter seu próprio recurso.

Entre no [Portal do Azure](https://portal.azure.com/)e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicativo.

![Clique em Novo, Application Insights](./media/windows-services/01-new-asp.png)

A escolha do tipo de aplicativo define o conteúdo padrão das folhas do recurso.

## <a name="2-copy-the-instrumentation-key"></a>2. Copiar a chave de instrumentação
A chave identifica o recurso. Você instalará em breve no SDK a fim de direcionar os dados para o recurso.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Instale o pacote do Application Insights em seu aplicativo
A instalação e a configuração do pacote do Application Insights varia dependendo da plataforma em que você está trabalhando. 

1. No Visual Studio, clique com o botão direito do mouse em seu projeto e escolha **Gerenciar pacotes NuGet**.
   
    ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/windows-services/03-nuget.png)
2. Instale o pacote do Application Insights para aplicativos do Windows Server, "Microsoft.ApplicationInsights.WindowsServer".
   
    ![Pesquise "Application Insights"](./media/windows-services/04-ai-nuget.png)
   
    *Qual versão?*

    Marque a opção **Incluir pré-lançamento** se você quiser experimentar nossos recursos mais recentes. Os documentos relevantes ou blogs indicam se você precisa de uma versão de pré-lançamento.
    
    *É possível usar outros pacotes?*
   
    Sim. Escolha “Microsoft.ApplicationInsights” se deseja usar a API para enviar sua próprias telemetrias. O pacote do Windows Server inclui a API e vários outros pacotes, como coleta do contador de desempenho e monitoramento de dependência. 

### <a name="to-upgrade-to-future-package-versions"></a>Para atualizar para versões futuras do pacote
Lançamos uma nova versão do SDK de tempos em tempos.

Para atualizar para uma [nova versão do pacote](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), abra o Gerenciador de pacotes do NuGet e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.WindowsServer** e **Atualizar**.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia antes de atualizar e, depois, mescle suas alterações à nova versão.

## <a name="4-send-telemetry"></a>4. Enviar telemetria
**Se você instalou apenas o pacote de API:**

* Defina a chave de instrumentação no código, por exemplo, em `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *sua chave* `";` 
* [Escreva sua própria telemetria usando a API](../../azure-monitor/app/api-custom-events-metrics.md#ikey).

**Se tiver instalado outros pacotes do Application Insights,** se preferir, você poderá usar o arquivo .config para definir a chave de instrumentação:

* Edite o ApplicationInsights.config (que foi adicionado pela instalação do NuGet). Insira isto logo antes da marca de fechamento:
  
    `<InstrumentationKey>` *a chave de instrumentação que você copiou* `</InstrumentationKey>`
* Verifique se as propriedades de ApplicationInsights.config no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.

A definição da chave de instrumentação no código será útil se você quiser [alternar a chave para configurações de compilação diferentes](../../azure-monitor/app/separate-resources.md). Se você definir a chave no código, não será necessário defini-la no arquivo `.config`.

## <a name="run"></a> Execute seu projeto
Use a tecla **F5** para executar o aplicativo e experimente: abrir páginas diferentes para gerar telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![Contagem de eventos no Visual Studio](./media/windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Exibir sua telemetria
Volte para o [Portal do Azure](https://portal.azure.com/) e navegue até o seu recurso do Application Insights.

Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo: 

![Clique por mais dados](./media/windows-services/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.](../../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>Não há dados?
* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Abra o bloco [Pesquisar](../../azure-monitor/app/diagnostic-search.md) para ver eventos individuais. Às vezes, os eventos demoram um pouco mais para passar pelo pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos se atualizam periodicamente, mas você pode atualizá-los manualmente se estiver aguardando para alguns dados serem exibidos.
* Consulte [Solucionar problemas](../../azure-monitor/app/troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicar seu aplicativo
Agora, implante o aplicativo em seu servidor ou no Azure e veja os dados se acumularem.

![Usar o Visual Studio para publicar seu aplicativo](./media/windows-services/15-publish.png)

Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo na configuração de Versão, os dados acumulam mais lentamente.

### <a name="no-data-after-you-publish-to-your-server"></a>Nenhum dado depois de publicar no servidor?
Abra estas portas para tráfego de saída no firewall do servidor. Confira [essa página](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) para obter a lista de endereços necessários 

### <a name="trouble-on-your-build-server"></a>Problemas no servidor de compilação?
Consulte [este item de solução de problemas](../../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Se o seu aplicativo gerar muita telemetria, o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. 
> [Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).
> 
> 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas
* [Adicione mais telemetria](../../azure-monitor/app/asp-net-more.md) para obter uma visão de 360 graus completa de seu aplicativo.

