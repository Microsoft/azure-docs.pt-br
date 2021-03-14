---
title: Usar a Pesquisa no Azure Application Insights | Microsoft Docs
description: Pesquise e filtre telemetria bruta enviada pelo seu aplicativo Web.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: f7c2229a2e497fb052db79d61fcfeff140897798
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579548"
---
# <a name="using-search-in-application-insights"></a>Usar a Pesquisa no Application Insights

A Pesquisa é um recurso do [Application Insights](./app-insights-overview.md) que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

(Para consultas mais complexas sobre os dados, use o [Analytics](../logs/log-analytics-tutorial.md).)

## <a name="where-do-you-see-search"></a>Onde você vê o Search?

### <a name="in-the-azure-portal"></a>No portal do Azure

Você pode abrir a pesquisa de diagnóstico na guia Visão geral Application Insights do seu aplicativo (localizada na barra superior) ou em investigar à esquerda.

![Guia Pesquisar](./media/diagnostic-search/view-custom-events.png)

Vá para o menu suspenso tipos de evento para ver uma lista de itens de telemetria-solicitações de servidor, exibições de página, eventos personalizados que você tenha codificado e assim por diante. Na parte superior da lista de resultados, é um gráfico de resumo mostrando contagens de eventos ao longo do tempo.

Clique no menu suspenso ou em atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela da Pesquisa do Application Insights. É mais útil exibir eventos de telemetria gerados pelo aplicativo que você está depurando. Contudo, ele também pode mostrar os eventos coletados do seu aplicativo publicado no Portal do Azure.

Abra a janela Pesquisar no Visual Studio:

![O Visual Studio abre a pesquisa do Application Insights](./media/diagnostic-search/32.png)

A janela Pesquisar tem os mesmos recursos que o portal da Web:

![Janela de pesquisa do Visual Studio Application Insights](./media/diagnostic-search/34.png)

A guia Controlar Operação está disponível ao abrir uma solicitação ou uma exibição de página. Uma “operação” é uma sequência de eventos associada a uma única solicitação ou exibição de página. Por exemplo, chamadas de dependência, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia Controlar Operação mostra graficamente o tempo e a duração desses eventos em relação à exibição de solicitação ou página.

## <a name="inspect-individual-items"></a>Inspecionar itens individuais

Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados.

![Captura de tela de uma solicitação de dependência individual](./media/diagnostic-search/telemetry-item.png)

Isso iniciará a exibição de detalhes da transação completa.

## <a name="filter-event-types"></a>Filtrar tipos de evento

Abra o menu suspenso tipos de eventos e escolha os tipos de eventos que você deseja ver. (Se, posteriormente, você quiser restaurar os filtros, clique em Redefinir.)

Os tipos de evento são:

* **Rastrear**  -  [Logs de diagnóstico](./asp-net-trace-logs.md) , incluindo chamadas TrackTrace, log4net, NLog e System. Diagnostics. Trace.
* **Solicitar** -Solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Exibição**  -  de página [Telemetria enviada pelo cliente Web](./javascript.md), usada para criar relatórios de exibição de página.
* **Evento Personalizado** ‑ Se você tiver inserido chamadas TrackEvent() para [monitorar o uso](./api-custom-events-metrics.md), você poderá pesquisá-las aqui.
* **Exceção** -exceções não capturadas [no servidor](./asp-net-exceptions.md)e aquelas que você registra usando trackexception ().
* **Dependência**  -  de [Chamadas de seu aplicativo de servidor](./asp-net-dependencies.md) para outros serviços, como APIs REST ou bancos de dados, e chamadas AJAX do seu [código de cliente](./javascript.md).
* **Disponibilidade** ‑ Resultados de [testes de disponibilidade](./monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar pelos valores de propriedade

Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. Clique no ícone de filtro ![Ícone de filtro](./media/diagnostic-search/filter-icon.png) para iniciar.

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Ele desativará a filtragem nessa propriedade.

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade

Para localizar todos os itens com o mesmo valor de propriedade, digite-os na barra de pesquisa ou clique na caixa de seleção ao examinar propriedades na guia filtro.

![Clique na caixa de seleção de uma propriedade na guia filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para gravar consultas mais complexas, abra [**logs (análise)**](../logs/log-analytics-tutorial.md) na parte superior da folha de pesquisa.
>

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso será útil se você tiver escrito [eventos personalizados](./api-custom-events-metrics.md) com valores de propriedade.

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas.

![Abra a pesquisa de diagnóstico](./media/diagnostic-search/search-property.png)

Pesquisar por palavras inteiras, não subcadeias de caracteres. Use aspas para delimitar caracteres especiais.

| String | *Não* encontrado | Encontrado |
| --- | --- | --- |
| ControladorInicial.Sobre |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Estados Unidos|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Estas são algumas expressões de pesquisa que você pode usar:

| Exemplo de consulta | Efeito |
| --- | --- |
| `apple` |Encontrar todos os eventos no período cujos campos incluem a palavra “maçã” |
| `apple AND banana` <br/>`apple banana` |Encontrar eventos que contêm as duas palavras. Use "AND” em letras maiúsculas, e não "and". <br/>Forma abreviada. |
| `apple OR banana` |Encontrar eventos que contêm uma das duas palavras. Use "OR", e não "or". |
| `apple NOT banana` |Encontre eventos que contêm uma das palavras, mas não a outra. |

## <a name="sampling"></a>amostragem

Se seu aplicativo gerar uma grande quantidade de telemetria (e você estiver usando o ASP.NET SDK versão 2.0.0-Beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume que é enviado ao portal enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados.

[Saiba mais sobre amostragem](./sampling.md).

## <a name="create-work-item"></a>Criar um item de trabalho

Você pode criar um bug no GitHub ou Azure DevOps com os detalhes de qualquer item de telemetria.

Vá para a exibição de detalhes da transação de ponta a ponta clicando em qualquer item de telemetria e, em seguida, selecione **Criar item de trabalho**.

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/work-item.png)

Na primeira vez que fizer isso, será solicitado que você configure um link para sua conta e projeto do Azure DevOps.

(Você também pode configurar o link na guia itens de trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para o Application Insights

Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET](./asp-net-trace-logs.md) ou [Java](./java-trace-logs.md). Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página.
* [Escrever código](./api-custom-events-metrics.md) para enviar eventos personalizados, visualizações de página e exceções.

[Saiba como enviar logs e telemetria personalizada para o Application Insights](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>P & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?

Veja o [Resumo de limites](./pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?

Nós não registramos os dados de POST automaticamente, mas você pode usar [chamadas de log ou TrackTrace](./asp-net-trace-logs.md). Coloque os dados de POSTAGEM no parâmetro de mensagem. Não é possível filtrar a mensagem da mesma maneira que as propriedades, mas o limite de tamanho é maior.

## <a name="next-steps"></a><a name="add"></a>Próximas etapas

* [Escrever consultas complexas no Analytics](../logs/log-analytics-tutorial.md)
* [Enviar logs e telemetria personalizada para o Application Insights](./asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](./monitor-web-app-availability.md)
* [Solução de problemas](../faq.md)