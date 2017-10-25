---
title: Usar a Pesquisa no Azure Application Insights | Microsoft Docs
description: Pesquise e filtre telemetria bruta enviada pelo seu aplicativo Web.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: e2d12f807756b778a64920b12a66fba184a99844
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-search-in-application-insights"></a>Usar a Pesquisa no Application Insights
A Pesquisa é um recurso do [Application Insights](app-insights-overview.md) que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

(Para consultas mais complexas sobre os dados, use o [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Onde você vê a Pesquisa?
### <a name="in-the-azure-portal"></a>No portal do Azure
Você pode abrir a pesquisa de diagnóstico explicitamente na folha Visão Geral do Application Insights do seu aplicativo:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Ela também é aberta quando você clica em alguns gráficos e itens de grade. Nesse caso, os filtros dessa pesquisa são predefinidos para concentrar-se no tipo de item selecionado por você. 

Por exemplo, na folha Visão Geral, há um gráfico de barras das solicitações classificadas pelo tempo de resposta. Clique em um intervalo de desempenho para ver uma lista de solicitações individuais desse intervalo de tempo de resposta:

![Clique no desempenho de solicitação](./media/app-insights-diagnostic-search/07-open-from-filters.png)

O corpo principal da Pesquisa de diagnóstico é uma lista de itens de telemetria - solicitações ao servidor, visualizações de página, eventos personalizados que você codificou e assim por diante. Na parte superior da lista, há um gráfico de resumo mostrando contagens de eventos ao longo do tempo.

Clique em Atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela da Pesquisa do Application Insights. É mais útil exibir eventos de telemetria gerados pelo aplicativo que você está depurando. Contudo, ele também pode mostrar os eventos coletados do seu aplicativo publicado no Portal do Azure.

Abra a janela Pesquisar no Visual Studio:

![O Visual Studio abre a pesquisa do Application Insights](./media/app-insights-diagnostic-search/32.png)

A janela Pesquisar tem os mesmos recursos que o portal da Web:

![Janela de pesquisa do Visual Studio Application Insights](./media/app-insights-diagnostic-search/34.png)

A guia Controlar Operação está disponível ao abrir uma solicitação ou uma exibição de página. Uma “operação” é uma sequência de eventos associada a uma única solicitação ou exibição de página. Por exemplo, chamadas de dependência, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia Controlar Operação mostra graficamente o tempo e a duração desses eventos em relação à exibição de solicitação ou página. 

## <a name="inspect-individual-items"></a>Inspecionar itens individuais
Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados. Se você quiser ver o conjunto completo de campos, clique em "...". 

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Filtrar tipos de evento
Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)

![Escolha o filtro e selecione os tipos de telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)

Os tipos de evento são:

* **Controlar** - [Logs de diagnóstico](app-insights-asp-net-trace-logs.md), incluindo chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Solicitar** -Solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Exibição da página** - [Telemetria enviada pelo cliente da Web](app-insights-javascript.md), usada para criar relatórios de exibição de página. 
* **Evento Personalizado** ‑ Se você tiver inserido chamadas TrackEvent() para [monitorar o uso](app-insights-api-custom-events-metrics.md), você poderá pesquisá-las aqui.
* **Exceção** ‑ [Exceções não percebidas no servidor](app-insights-asp-net-exceptions.md) e aquelas que você registra usando TrackException().
* **Dependência** - [Chamadas do aplicativo para servidores](app-insights-asp-net-dependencies.md) para outros serviços, como APIs REST ou bancos de dados e chamadas AJAX do seu [código de cliente](app-insights-javascript.md).
* **Disponibilidade** ‑ Resultados de [testes de disponibilidade](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar pelos valores de propriedade
Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. 

Por exemplo, escolha solicitações com um código de resposta específicos. 

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Ele desativará a filtragem nessa propriedade.

### <a name="narrow-your-search"></a>Reduzir o escopo de sua pesquisa
Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado. 

Neste exemplo, está claro que a solicitação “Rpt/Employees” resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade
Localize todos os itens com o mesmo valor da propriedade:

![Clique com o botão direito em uma propriedade](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra o [**Analytics**](app-insights-analytics-tour.md) na parte superior da folha Pesquisa.
> 

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso será especialmente útil se você tiver gravado [eventos personalizados](app-insights-api-custom-events-metrics.md) com valores de propriedade. 

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas. 

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/appinsights-311search.png)

Pesquisar por palavras inteiras, não subcadeias de caracteres. Use aspas para delimitar caracteres especiais.

| string | *não* é encontrada por | porém, pode ser encontrada por |
| --- | --- | --- |
| ControladorInicial.Sobre |inicial<br/>controlador<br/>obre | homecontroller<br/>sobre<br/>"homecontroller.about"|
|Estados Unidos|Uni<br/>dos|unidos<br/>estados<br/>estados AND unidos<br/>“estados unidos”

Estas são algumas expressões de pesquisa que você pode usar:

| Exemplo de consulta | Efeito |
| --- | --- |
| `apple` |Encontrar todos os eventos no período cujos campos incluem a palavra “maçã” |
| `apple AND banana` |Encontrar eventos que contêm as duas palavras. Use "AND” em letras maiúsculas, e não "and". |
| `apple OR banana`<br/>`apple banana` |Encontrar eventos que contêm uma das duas palavras. Use "OR", e não "or".<br/>Forma abreviada. |
| `apple NOT banana` |Encontre eventos que contêm uma das palavras, mas não a outra. |



## <a name="sampling"></a>amostragem
Se o seu aplicativo gerar muita telemetria (e você estiver usando o SDK do ASP.NET versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. 

[Saiba mais sobre amostragem](app-insights-sampling.md).



## <a name="create-work-item"></a>Criar um item de trabalho
Você pode criar um bug no GitHub ou Visual Studio Team Services com os detalhes de qualquer item de telemetria. 

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/42.png)

Na primeira vez que fizer isso, será solicitado que você configure um link para sua conta e projeto do Team Services.

![Preencha a URL do servidor do Team Services e o Nome do projeto e, em seguida, clique em Autorizar](./media/app-insights-diagnostic-search/41.png)

(Você também pode configurar o link na folha Itens de Trabalho.)

## <a name="save-your-search"></a>Salvar sua pesquisa
Quando você definiu todos os filtros que deseja, você pode salvar a pesquisa como um favorito. Se você trabalha em uma conta organizacional, você pode optar por compartilhá-la com outros membros da equipe.

![Clique em Favorito, defina o nome e clique em Salvar](./media/app-insights-diagnostic-search/08-favorite-save.png)

Para ver a pesquisa novamente, **vá até a folha de visão geral** e abra Favoritos:

![Bloco Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se você os salvou com o intervalo de tempo Relativo, a folha reaberta contém os dados mais recentes. Se você os salvou com o intervalo de tempo Absoluto, consulte os mesmos dados, sempre. (Se “Intervalo de Tempo” não estiver disponível quando você desejar salvar um favorito, clique em Intervalo de tempo no cabeçalho e defina um período que não seja um intervalo personalizado.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para o Application Insights
Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET](app-insights-asp-net-trace-logs.md) ou [Java](app-insights-java-trace-logs.md). Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página. 
* [Escrever código](app-insights-api-custom-events-metrics.md) para enviar eventos personalizados, visualizações de página e exceções. 

[Saiba como enviar logs e telemetria personalizada para o Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>Perguntas e respostas
### <a name="limits"></a>Que quantidade de dados é mantida?

Veja o [Resumo de limites](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?
Nós não registramos os dados de POST automaticamente, mas você pode usar [chamadas de log ou TrackTrace](app-insights-asp-net-trace-logs.md). Coloque os dados de POSTAGEM no parâmetro de mensagem. Não é possível filtrar a mensagem da mesma maneira que as propriedades, mas o limite de tamanho é maior.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Próximas etapas
* [Escrever consultas complexas no Analytics](app-insights-analytics-tour.md)
* [Enviar logs e telemetria personalizada para o Application Insights](app-insights-asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](app-insights-monitor-web-app-availability.md)
* [Solução de problemas](app-insights-troubleshoot-faq.md)
