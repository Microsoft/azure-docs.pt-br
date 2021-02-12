---
title: Visualizar dados no Time Series Insights Explorer-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre os recursos e as opções disponíveis no Azure Time Series Insights Explorer.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 55e102cc7e27cbcd9a65999f590c8264f1ad4434
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736845"
---
# <a name="azure-time-series-insights-explorer"></a>Gerenciador do Azure Time Series Insights

Este artigo descreve os vários recursos e opções disponíveis no [ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo)do Azure Time Series insights Gen2.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Time Series Insights Explorer, você deve:

* Ter um ambiente de Azure Time Series Insights Gen2 provisionado. Saiba mais sobre como provisionar uma instância lendo o tutorial [Azure Time Series insights Gen2](./tutorials-set-up-tsi-environment.md) .
* [Forneça acesso a dados](./concepts-access-policies.md) para o ambiente de Azure Time Series insights Gen2 que você criou para a conta. Você pode fornecer acesso a outras pessoas, bem como para si mesmo.
* Adicione uma origem do evento ao ambiente Azure Time Series Insights Gen2 para enviar dados por push para o ambiente:
  * Saiba [como se conectar a um hub de eventos](./how-to-ingest-data-event-hub.md)
  * Saiba [como se conectar a um hub IOT](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Explorar o Azure Time Series Insights Explorer

O Azure Time Series Insights Explorer consiste nos seguintes sete elementos:

[![Visão geral do Azure Time Series Insights Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Painel de ambiente](#1-environment-panel): exibe todos os seus ambientes de Azure Time Series insights Gen2.
1. [Barra de navegação](#2-navigation-bar): permite alternar entre as páginas **analisar** e **modelo** .
1. [Árvore de hierarquia e painel de pesquisa](#3-hierarchy-tree-and-search-panel): permite que você selecione e pesquise elementos de dados específicos para serem gráficos.
1. [Compartimento de série temporal](#4-time-series-well): mostra todos os elementos de dados selecionados no momento.
1. [Painel de gráfico](#5-chart-panel): exibe seu gráfico de trabalho atual.
1. [Linha do tempo](#6-time-editor-panel): permite que você modifique seu período de tempo de trabalho.
1. [Barra de aplicativos](#7-app-bar): contém as opções de gerenciamento de usuário (como o locatário atual) e permite que você as altere e as configurações de idioma.

## <a name="1-environment-panel"></a>1. painel de ambiente

O painel ambiente exibe todos os Azure Time Series Insights ambientes Gen2 aos quais você tem acesso. A lista inclui ambientes Gen2, bem como ambientes Gen1. Basta selecionar o ambiente que você deseja usar para ser usado imediatamente.

1. Selecione a seta suspensa ao lado do seu ambiente exibido.

   [![Painel de ambiente](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Em seguida, selecione o ambiente desejado.

## <a name="2-navigation-bar"></a>2. barra de navegação

  [![A barra de navegação](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Use a barra de navegação para selecionar entre duas exibições:

* **Analise**: Use-o no gráfico e execute análises avançadas em seus dados de série temporal modelados ou não modelados.
* **Modelo**: Use-o para enviar por push novos Azure Time Series insights tipos, hierarquias e instâncias do Gen2 para seu modelo de série temporal.

### <a name="model-authoring"></a>Criação de modelos

O Azure Time Series Insights Gen2 dá suporte a operações CRUD (criação, leitura, atualização e exclusão) completas no seu modelo de série temporal.

[![O painel de pesquisa de modelo](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo de modelo de série temporal**: você pode usar tipos de modelo de série temporal para definir variáveis ou fórmulas para fazer cálculos. Eles estão associados a uma determinada instância de modelo de série temporal. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia de modelos de série temporal**: as hierarquias são organizações sistemáticas de seus dados. As hierarquias do representam as relações entre diferentes instâncias em seu modelo de série temporal.
* **Instância de modelo de série temporal**: as instâncias são a própria série temporal. Na maioria dos casos, eles são **DeviceID** ou **AssetID**, que é o identificador exclusivo do ativo no ambiente.

Para saber mais sobre o modelo de série temporal, leia os [modelos de série Times](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. árvore de hierarquia e painel de pesquisa

A árvore de hierarquia e o painel de pesquisa permitem pesquisar e navegar facilmente na hierarquia do [modelo de série temporal](./concepts-model-overview.md) para localizar as instâncias de série temporal específicas que você deseja exibir no gráfico. Quando você seleciona suas instâncias, elas não são adicionadas apenas ao gráfico atual, mas também são adicionadas ao bem de dados.

[![Árvore de hierarquia e painel de pesquisa](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

O painel resultados da pesquisa também permite exibir os resultados em uma exibição de hierarquia ou em uma exibição de lista, facilitando a localização das instâncias que você deseja exibir.

## <a name="4-time-series-well"></a>4. série temporal de tempo

O bem exibe os campos de instância e outros metadados associados às instâncias de modelo de série temporal selecionadas. Ao marcar as caixas de seleção no lado direito, você pode ocultar ou exibir instâncias específicas do gráfico atual.

  [![O Gen2 também](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Você pode remover elementos de dados específicos de seus dados atuais bem, selecionando o controle vermelho **delete** (lixeira) no lado esquerdo do elemento. O também permite que você controle como cada elemento é exibido no gráfico. Você pode optar por adicionar sombras mínimas/máximas, pontos de dados, deslocar o elemento no tempo e visualizar a instância de uma maneira de nível.

Além disso, o controle explorações permite que você crie facilmente turnos de tempo e dispersão.  

  [![Opções de layout bem](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se a seguinte mensagem for exibida, a instância não terá nenhum dado durante o período de tempo selecionado. Para resolver o problema, aumente o período de tempo ou confirme se a instância está enviando dados por push.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. painel de gráfico

O gráfico permite que você exiba instâncias de série temporal como linhas. Você pode recolher o painel de ambiente, o modelo de dados e o painel de controle de intervalo de tempo, clicando em controles da web para tornar o gráfico maior.

  [![Visão geral do gráfico de Gen2](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo de gráfico**: controla quais elementos de dados estão disponíveis para visualização.

1. **Tamanho do intervalo**: a ferramenta controle deslizante de tamanho do intervalo permite que você amplie e reduza os intervalos no mesmo período de tempo. Isso fornece um controle mais preciso da movimentação entre grandes fatias de tempo que mostram tendências suaves para fatias tão pequenas quanto o milissegundo, permitindo que você examine os cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição ideal dos dados de sua seleção; resolução de balanceamento, velocidade de consulta e granularidade.

1. **Zoom e panorâmica**: Selecione este controle para aplicar zoom e panorâmica do gráfico.

1. **Controle do eixo y**: percorre as opções de exibição do eixo y disponíveis:

    * `Stacked`: Cada linha tem um eixo Y individual.
    * `Overlap`: Use-o para empilhar várias linhas no mesmo eixo Y, com os dados do eixo Y mudando com base na linha selecionada.
    * `Shared`: Todos os dados do eixo Y exibidos juntos.

1. **Elemento Marker**: o elemento de dados selecionado no momento e seus detalhes associados.

Você pode detalhar ainda mais uma fatia de dados específica **clicando com** o botão esquerdo do mouse em um ponto de dados no grafo atual enquanto mantém o rato pressionado e arrastando a área selecionada para o ponto de extremidade de sua escolha. **Clique com o botão direito do mouse** na área azul selecionada e, em seguida, selecione **zoom** , conforme mostrado abaixo. Você também pode exibir e baixar os eventos de telemetria no período de tempo selecionado.

  [![Zoom do gráfico de Gen2](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Depois de executar a ação de **zoom** , o conjunto de dados selecionado será exibido. Selecione o controle de formato para percorrer as três representações do eixo y de seus dados.

  [![Eixo y do gráfico de Gen2](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Aqui, um exemplo de um **gráfico sobreposto** é fornecido:

  [![Opção de gráfico sobreposto](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

O **botão mais ações** se expande para exibir o **download como CSV**, **conectar-se a Power bi**, **Mostrar dados do gráfico como uma tabela** e explorar as opções de **eventos brutos** .

  [![Opção de mais ações](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Saiba mais sobre a opção **conectar a Power bi** no [conector Power bi](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. painel de editor de tempo

Ao trabalhar com Azure Time Series Insights Gen2, primeiro você selecionará um período de tempo. O período de tempo selecionado controlará o conjunto de dados que está disponível para manipulação com os widgets de atualização do Azure Time Series Insights Gen2.

  [![Painel de seleção de tempo](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Uma parte da linha do tempo é realçada em uma cor âmbar ou laranja para indicar o intervalo de dados que está disponível na loja a quente.

Os seguintes controles da Web estão disponíveis em Azure Time Series Insights Gen2 para selecionar seu período de tempo de trabalho.

  [![Controle bem explorativo](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Controle deslizante de intervalo de datas interna**: Use os dois controles de ponto de extremidade arrastando-os sobre o período de tempo desejado. Esse intervalo de datas interna é restrito pelo controle deslizante de intervalo de datas externa.

1. **Botões aumentar e diminuir intervalo de datas**: aumente ou diminua seu período selecionando um dos botões para o intervalo desejado.

1. **Controle de recolhimento** de intervalo de tempo: esse controle da Web permite que você oculte todos os controles, exceto a ferramenta de controle deslizante intervalo de datas internas.

1. **Controle deslizante de intervalo de datas externas**: Use os controles de ponto de extremidade para selecionar o intervalo de datas externas, que estará disponível para o controle de intervalo de datas interna.

1. **Controle deslizante de intervalo de tempo**: Use-o para alternar rapidamente entre as seleções de intervalo de tempo predefinido, como os últimos **30 minutos**, as **últimas 12 horas** ou um **intervalo personalizado**. A alteração desse valor também muda os intervalos de intervalo disponíveis discutidos na ferramenta de controle deslizante de tamanho do intervalo.

   [![De e para o painel de seleção](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. barra de aplicativos

O painel de navegação do Azure Time Series Insights Gen2 aparece na parte superior do seu aplicativo. Ele fornece as seguintes funcionalidades:

### <a name="current-session-share-link-control"></a>Controle de link de compartilhamento de sessão atual

  [![Ícone Compartilhar](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecione o ícone novo **compartilhamento** para compartilhar um link de URL com sua equipe.

  [![Compartilhar a URL da instância](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Seção de Locatário

  [![Seleção de locatário](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Exibe as informações atuais da conta de entrada do Azure Time Series Insights Gen2.
* Use-o para alternar entre os temas disponíveis.
* Use-o para exibir o [ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo)do Gen2.

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, selecione o ícone de perfil localizado no canto superior direito. Em seguida, selecione **alterar tema**.

  [![Seleção de tema](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A seleção de idioma também está disponível selecionando o ícone de perfil.

O Azure Time Series Insights Explorer dá suporte a dois temas:

* **Tema claro**: o tema padrão mostrado em todo este documento.
* **Tema escuro**: renderiza o Gerenciador como mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Controles de ambiente Gen1

### <a name="gen2-terms-panel"></a>Painel de termos do Gen2

Esta seção se aplica somente a ambientes Gen1 existentes que tentam usar o Gerenciador na interface do usuário atualizada. Talvez você queira usar o produto Gen1 e o produto Gen2 em combinação. Adicionamos algumas funcionalidades da interface do usuário existente ao Gerenciador atualizado, mas você pode obter a experiência de interface do usuário completa para um ambiente Gen1 no novo Azure Time Series Insights Explorer.

Em vez da hierarquia, o painel Azure Time Series Insights termos Gen2 é exibido. O painel de termos permite que você defina consultas em seu ambiente. Use-o para filtrar também os dados com base em um predicado.

  [![Painel de consulta de onde](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

O painel do editor de termos do Azure Time Series Insights Gen2 usa os seguintes parâmetros:

**Em que**: Use a cláusula WHERE para filtrar rapidamente seus eventos usando o conjunto de operandos listados na tabela a seguir. Caso você realize uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem o seguinte:

| Operação    | Tipos com suporte    | Observações |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL. |
| `HAS` | String | Somente literais de cadeia de caracteres constantes são permitidos no lado direito. Cadeia de caracteres vazia e NULL não são permitidos. |

Para saber mais sobre os tipos de dados e operações de consulta com suporte, consulte [TSX (expressão de série temporal)](/rest/api/time-series-insights/reference-time-series-expression-syntax).

### <a name="examples-of-where-clauses"></a>Exemplos de cláusulas WHERE

  [![Exemplos de cláusula WHERE](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Medida**: uma lista suspensa que exibe todas as colunas numéricas (**duplos**) que você pode usar como elementos para seu gráfico atual.

**Dividir por**: essa lista suspensa exibe todas as colunas categóricas disponíveis (cadeias de caracteres) em seu modelo no qual você pode agrupar seus dados. Você pode adicionar até cinco termos para exibir no mesmo eixo x. Insira os parâmetros desejados e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  [![Exibição consultada e filtrada um](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Você pode mostrar e ocultar elementos no painel de gráfico selecionando o ícone visível, conforme mostrado na imagem a seguir. Para remover completamente as consultas, selecione o **X** vermelho.

  [![Cancelar uma opção consultada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [ingestão de dados](./concepts-ingestion-overview.md) em seu ambiente.

* Confira o artigo sobre [armazenamento](concepts-storage.md).

* Leia sobre [modelagem de dados](./concepts-model-overview.md) no Azure Time Series insights Gen2.

* Saiba [como diagnosticar e solucionar problemas](./how-to-diagnose-troubleshoot.md) de seu ambiente.
