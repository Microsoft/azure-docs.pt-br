---
title: Usar a Pesquisa no Azure Application Insights | Microsoft Docs
description: Pesquise e filtre telemetria bruta enviada pelo seu aplicativo Web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: dfbaabd3d27804909334a7a370bcc89115e625c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899922"
---
# <a name="using-search-in-application-insights"></a>Usar a Pesquisa no Application Insights
A Pesquisa é um recurso do [Application Insights](../../azure-monitor/app/app-insights-overview.md) que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

(Para consultas mais complexas sobre os dados, use o [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Onde você vê o Search?

### <a name="in-the-azure-portal"></a>No portal do Azure

Você pode abrir a pesquisa de diagnóstico explicitamente na folha Visão Geral do Application Insights do seu aplicativo:

![Abra a pesquisa de diagnóstico](./media/diagnostic-search/001.png)

![Captura de tela dos grafos de pesquisa de diagnóstico](./media/diagnostic-search/002.png)

O corpo principal da Pesquisa de diagnóstico é uma lista de itens de telemetria - solicitações ao servidor, visualizações de página, eventos personalizados que você codificou e assim por diante. Na parte superior da lista, há um gráfico de resumo mostrando contagens de eventos ao longo do tempo.

Clique em Atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela da Pesquisa do Application Insights. É mais útil exibir eventos de telemetria gerados pelo aplicativo que você está depurando. Contudo, ele também pode mostrar os eventos coletados do seu aplicativo publicado no Portal do Azure.

Abra a janela Pesquisar no Visual Studio:

![O Visual Studio abre a pesquisa do Application Insights](./media/diagnostic-search/32.png)

A janela Pesquisar tem os mesmos recursos que o portal da Web:

![Janela de pesquisa do Visual Studio Application Insights](./media/diagnostic-search/34.png)

A guia Controlar Operação está disponível ao abrir uma solicitação ou uma exibição de página. Uma “operação” é uma sequência de eventos associada a uma única solicitação ou exibição de página. Por exemplo, chamadas de dependência, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia Controlar Operação mostra graficamente o tempo e a duração desses eventos em relação à exibição de solicitação ou página. 

## <a name="inspect-individual-items"></a>Inspecionar itens individuais

Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados.

![Captura de tela de uma solicitação de dependência individual](./media/diagnostic-search/003.png)

Isso iniciará a exibição de detalhes da transação de ponta a ponta:

![Captura de tela da exibição de detalhes da transação de ponta a ponta.](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtrar tipos de evento
Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)

![Escolha o filtro e selecione os tipos de telemetria](./media/diagnostic-search/02-filter-req.png)

Os tipos de evento são:

* **Controlar** - [Logs de diagnóstico](../../azure-monitor/app/asp-net-trace-logs.md), incluindo chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Solicitar** -Solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Exibição da página** - [Telemetria enviada pelo cliente da Web](../../azure-monitor/app/javascript.md), usada para criar relatórios de exibição de página. 
* **Evento Personalizado** ‑ Se você tiver inserido chamadas TrackEvent() para [monitorar o uso](../../azure-monitor/app/api-custom-events-metrics.md), você poderá pesquisá-las aqui.
* **Exceção** ‑ [Exceções não percebidas no servidor](../../azure-monitor/app/asp-net-exceptions.md) e aquelas que você registra usando TrackException().
* **Dependência** - [Chamadas do aplicativo para servidores](../../azure-monitor/app/asp-net-dependencies.md) para outros serviços, como APIs REST ou bancos de dados e chamadas AJAX do seu [código de cliente](../../azure-monitor/app/javascript.md).
* **Disponibilidade** ‑ Resultados de [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar pelos valores de propriedade
Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. 

Por exemplo, escolha solicitações com um código de resposta específicos. 

![Expanda uma propriedade e escolha um valor](./media/diagnostic-search/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Ele desativará a filtragem nessa propriedade.

### <a name="narrow-your-search"></a>Reduzir o escopo de sua pesquisa
Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado. 

Neste exemplo, está claro que a solicitação “Rpt/Employees” resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade
Localize todos os itens com o mesmo valor da propriedade:

![Clique com o botão direito em uma propriedade](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra o [**Analytics**](../../azure-monitor/log-query/get-started-portal.md) na parte superior da folha Pesquisa.
> 

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso será especialmente útil se você tiver gravado [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md) com valores de propriedade. 

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas. 

![Abra a pesquisa de diagnóstico](./media/diagnostic-search/appinsights-311search.png)

Pesquisar por palavras inteiras, não subcadeias de caracteres. Use aspas para delimitar caracteres especiais.

| string | *não* é encontrada por | porém, pode ser encontrada por |
| --- | --- | --- |
| ControladorInicial.Sobre |inicial<br/>controlador<br/>obre | homecontroller<br/>about<br/>"homecontroller.about"|
|Estados Unidos|Uni<br/>dos|unidos<br/>estados<br/>estados AND unidos<br/>“estados unidos”

Estas são algumas expressões de pesquisa que você pode usar:

| Exemplo de consulta | Efeito |
| --- | --- |
| `apple` |Encontrar todos os eventos no período cujos campos incluem a palavra “maçã” |
| `apple AND banana` <br/>`apple banana` |Encontrar eventos que contêm as duas palavras. Use "AND” em letras maiúsculas, e não "and". <br/>Forma abreviada. |
| `apple OR banana` |Encontrar eventos que contêm uma das duas palavras. Use "OR", e não "or". |
| `apple NOT banana` |Encontre eventos que contêm uma das palavras, mas não a outra. |

## <a name="sampling"></a>amostragem
Se o seu aplicativo gerar muita telemetria (e você estiver usando o SDK do ASP.NET versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. 

[Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Criar um item de trabalho
Você pode criar um bug no GitHub ou Azure DevOps com os detalhes de qualquer item de telemetria. 

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/42.png)

Na primeira vez que fizer isso, será solicitado que você configure um link para sua conta e projeto do Azure DevOps.

![Preencha a URL do Azure DevOps Services e do Nome do projeto, e clique em Autorizar](./media/diagnostic-search/41.png)

(Você também pode configurar o link na folha Itens de Trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para o Application Insights
Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página. 
* [Escrever código](../../azure-monitor/app/api-custom-events-metrics.md) para enviar eventos personalizados, visualizações de página e exceções. 

[Saiba como enviar logs e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>Perguntas e respostas
### <a name="limits"></a>Que quantidade de dados é mantida?

Veja o [Resumo de limites](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?
Nós não registramos os dados de POST automaticamente, mas você pode usar [chamadas de log ou TrackTrace](../../azure-monitor/app/asp-net-trace-logs.md). Coloque os dados de POSTAGEM no parâmetro de mensagem. Não é possível filtrar a mensagem da mesma maneira que as propriedades, mas o limite de tamanho é maior.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Próximas etapas
* [Escrever consultas complexas no Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Enviar logs e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
