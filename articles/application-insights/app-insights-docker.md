---
title: Monitorar aplicativos do Docker no Azure Application Insights | Microsoft Docs
description: "Contadores de desempenho, eventos e exceções do Docker podem ser exibidos no Application Insights, juntamente com a telemetria dos aplicativos contidos."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: b082e345ca1bb3b12c548e05e699474d3aa9306c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitorar aplicativos do Docker no Application Insights
Os eventos de ciclo de vida e os contadores de desempenho dos contêineres [Docker](https://www.docker.com/) podem ser representados em gráfico no Application Insights. Instale a imagem [Application Insights](app-insights-overview.md) em um contêiner em seu host e ele exibirá os contadores de desempenho para o host, bem como para outras imagens.

Com o Docker, você distribui os aplicativos em contêineres leves com todas as dependências. Eles serão executados em qualquer máquina host que executa um Mecanismo de Docker.

Ao executar a [imagem do Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no host do Docker, você obtém estes benefícios:

* A telemetria do ciclo de vida sobre todos os contêineres em execução no host - início, parada e assim por diante.
* Contadores de desempenho para todos os contêineres. CPU, memória, uso da rede e muito mais.
* Se você [instalou o SDK do Application Insights para Java](app-insights-java-live.md) nos aplicativos em execução nos contêineres, toda a telemetria desses aplicativos terá propriedades adicionais que identificam o contêiner e o computador host. Portanto, por exemplo, se você tiver instâncias de um aplicativo em execução em mais de um host, poderá filtrar a telemetria do aplicativo pelo host com facilidade.

![exemplo](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Configurar seu recurso do Application Insights
1. Entre no [portal do Microsoft Azure](https://azure.com) e abra o recurso do Application Insights de seu aplicativo ou [crie um novo](app-insights-create-new-resource.md). 
   
    *Qual recurso devo usar?* Se os aplicativos que estão em execução no host foram desenvolvidos por outra pessoa, você precisa [criar um novo recurso do Application Insights](app-insights-create-new-resource.md). Esse é o local em que você pode exibir e analisar a telemetria. (Selecione 'Geral' para o tipo de aplicativo.)
   
    Mas se você for o desenvolvedor dos aplicativos, esperamos que você tenha [adicionado o SDK do Application Insights](app-insights-java-live.md) a cada um deles. Se eles forem realmente todos os componentes de um único aplicativo de negócios, você poderá configurar todos eles para enviar a telemetria para um recurso e usará esse mesmo recurso para exibir os dados de desempenho e do ciclo de vida do Docker. 
   
    Um terceiro cenário é que você desenvolveu a maioria dos aplicativos, mas está usando recursos separados para exibir a telemetria deles. Nesse caso, provavelmente, você também desejará criar um recurso separado para os dados do Docker. 
2. Adicione o bloco Docker: escolha **Adicionar Bloco**, arraste o bloco Docker a partir da galeria, em seguida, clique em **Concluído**. 
   
    ![exemplo](./media/app-insights-docker/03.png)
3. Clique no menu suspenso **Informações gerais** e copie a Chave de Instrumentação. Você usará isso para informar ao SDK o local em que sua telemetria será enviada.

    ![exemplo](./media/app-insights-docker/02-props.png)

Mantenha essa janela do navegador à mão, pois você voltará a ele em breve para examinar a telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor do Application Insights em seu host
Agora que você tem algum lugar para exibir a telemetria, configure o aplicativo contido que a coletará e enviará.

1. Conecte-se ao seu host do Docker. 
2. Edite a chave de instrumentação nesse comando e, em seguida, execute-a:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem do Application Insights é necessária por host do Docker. Se o seu aplicativo for implantado em vários hosts do Docker, repita o comando em todos os hosts.

## <a name="update-your-app"></a>Atualizar seu aplicativo
Se seu aplicativo for instrumentado com o [SDK do Application Insights para Java](app-insights-java-get-started.md), adicione a seguinte linha ao arquivo ApplicationInsights.xml em seu projeto, sob o elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isso adiciona informações do Docker, como o contêiner e a ID de host, a cada item de telemetria enviado do seu aplicativo.

## <a name="view-your-telemetry"></a>Exibir sua telemetria
Volte ao recurso do Application Insights no Portal do Azure.

Clique por meio do bloco do Docker.

Em breve, você verá os dados recebidos do aplicativo do Docker, especialmente se tiver outros contêineres em execução em seu mecanismo do Docker.

Estas são algumas das exibições que você pode obter.

### <a name="perf-counters-by-host-activity-by-image"></a>Contadores de desempenho por host, atividade por imagem
![exemplo](./media/app-insights-docker/10.png)

![exemplo](./media/app-insights-docker/11.png)

Clique em qualquer nome de imagem ou host para obter mais detalhes.

Para personalizar o modo de exibição, clique em qualquer gráfico, no título da grade ou use Adicionar Gráfico. 

[Saiba mais sobre o Metrics Explorer](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Eventos de contêiner do Docker
![exemplo](./media/app-insights-docker/13.png)

Para investigar os eventos individuais, clique em [Pesquisar](app-insights-diagnostic-search.md). Pesquise e filtre para localizar os eventos desejados. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome do contêiner
![exemplo](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto do Docker adicionado à telemetria do aplicativo
Telemetria da solicitação enviada do aplicativo instrumentado com o SDK do AI, aprimorada com o contexto do Docker:

![exemplo](./media/app-insights-docker/16.png)

Tempo do processador e contadores de desempenho de memória disponíveis, aprimorados e agrupados por nome de contêiner do Docker:

![exemplo](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Perguntas e respostas
*O que o Application Insights me proporciona que eu não consigo obter com o Docker?*

* Análise detalhada dos contadores de desempenho por contêiner e imagem.
* Integração dos dados de contêiner e do aplicativo em um painel.
* [Exporte a telemetria](app-insights-export-telemetry.md) para uma análise adicional em um banco de dados, no Power BI ou em outro painel.

*Como posso obter a telemetria do próprio aplicativo?*

* Instale o SDK do Application Insights no aplicativo. Saiba mais para: [aplicativos Web Java](app-insights-java-get-started.md), [aplicativos Web Windows](app-insights-asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas

* [Application Insights para Java](app-insights-java-get-started.md)
* [Application Insights para Node.js](app-insights-nodejs.md)
* [Application Insights para ASP.NET](app-insights-asp-net.md)
