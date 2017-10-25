---
title: Mapa de aplicativos no Azure Application Insights | Microsoft Docs
description: "Uma apresentação visual das dependências entre componentes do aplicativo, rotuladas com alertas e KPIs."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 207526b7a675f92134d045ebefb9a372749bce92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-map-in-application-insights"></a>Mapa de Aplicativos no Application Insights
No [Azure Application Insights](app-insights-overview.md), o Mapa de Aplicativos é um layout visual das relações de dependência dos componentes de seu aplicativo. Cada componente mostra KPIs, como carga, desempenho, falhas e alertas, para ajudá-lo a descobrir possíveis componentes que estejam causando uma falha ou um problema de desempenho. Você pode clicar em qualquer componente para obter diagnóstico mais detalhado, como eventos do Application Insights. Se seu aplicativo usar os serviços do Azure, você também poderá clicar no diagnóstico do Azure, como nas recomendações do Assistente do Banco de Dados SQL.

Assim como outros gráficos, você pode fixar um mapa de aplicativos no painel do Azure, onde ele fica totalmente funcional. 

## <a name="open-the-application-map"></a>Abrir o mapa de aplicativos
Abra o mapa na folha de visão geral do seu aplicativo:

![abrir mapa de aplicativos](./media/app-insights-app-map/01.png)

![mapa de aplicativos](./media/app-insights-app-map/02.png)

O mapa mostra:

* Testes de disponibilidade
* Componente do lado do cliente (monitorado com o SDK do JavaScript)
* Componente do lado do servidor
* Dependências dos componentes do cliente e do servidor

Você pode expandir e recolher grupos de link de dependência:

![recolher](./media/app-insights-app-map/03.png)

Se você tiver muitas dependências de um tipo (SQL, HTTP, etc.), elas poderão aparecer agrupadas. 

![dependências agrupadas](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Identificar problemas
Cada nó possui indicadores de desempenho relevantes, como as taxas de carga, de desempenho e de falha do componente. 

Ícones de aviso destacam possíveis problemas. Um aviso laranja significa que existem falhas em solicitações, exibições de página ou chamadas de dependência. Vermelho significa uma taxa de falha acima de 5%. Se você quiser ajustar esses limites, abra Opções.

![ícones de falha](./media/app-insights-app-map/04.png)

Alertas ativos também aparecem: 

![alertas ativos](./media/app-insights-app-map/05.png)

Se você usa o SQL Azure, há um ícone que mostra quando há recomendações sobre como melhorar o desempenho. 

![Recomendações do Azure](./media/app-insights-app-map/06.png)

Clique em um ícone para obter mais detalhes:

![Recomendações do Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Clique para Diagnóstico
Cada um dos nós no mapa oferece cliques direcionados para diagnóstico. As opções variam dependendo do tipo de nó.

![opções de servidor](./media/app-insights-app-map/09.png)

Para componentes que são hospedados no Azure, as opções incluem links diretos para eles.

## <a name="filters-and-time-range"></a>Filtros e o intervalo de tempo
Por padrão, o mapa resume todos os dados disponíveis para o intervalo de tempo escolhido. Mas você pode filtrá-lo para incluir apenas nomes de operação ou dependências específicas.

* Nome da operação: isso inclui tipos de solicitação do lado servidor e exibições de página. Com essa opção, o mapa mostra o KPI no nó do lado do servidor/cliente somente para operações selecionadas. Ele mostra as dependências chamadas no contexto dessas operações específicas.
* Nome base de dependência: isso inclui as dependências de navegador do AJAX e dependências do lado do servidor. Se você relatar telemetria de dependência personalizada com a API TrackDependency, ela também será exibida aqui. Você pode selecionar quais dependências mostrar no mapa. Atualmente, essa seleção não filtra as solicitações do lado do servidor nem as exibições de página do lado do cliente.

![Definir filtros](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Salvar filtros
Para salvar os filtros que você aplicou, fixe na exibição filtrada em um [painel](app-insights-dashboards.md).

![Fixar no painel](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Painel de erros
Quando você clica em um nó no mapa, é exibido no lado direito um painel de erros resumindo as falhas do nó. As falhas são agrupadas primeiro segundo a ID da operação e, em seguida, segundo a ID do problema.

![Painel de erros](./media/app-insights-app-map/error-pane.png)

Clicar em uma falha leva você até a instância mais recente dessa falha.

## <a name="resource-health"></a>Integridade de recursos
Para alguns tipos de recursos, sua integridade é exibida na parte superior do painel de erros. Por exemplo, clicar em um nó do SQL mostrará a integridade do banco de dados e todos os alertas que tiverem sido disparados.

![Integridade de recursos](./media/app-insights-app-map/resource-health.png)

Você pode clicar no nome do recurso para exibir as métricas de visão geral padrão para esse recurso.

## <a name="end-to-end-system-app-maps"></a>Mapas de aplicativos do sistema de ponta a ponta

*Requer o SDK versão 2.3 ou superior*

Se seu aplicativo tiver vários componentes (por exemplo, um serviço de back-end além do aplicativo Web), você poderá mostrá-los em um mapa de aplicativos integrado.

![Definir filtros](./media/app-insights-app-map/multi-component-app-map.png)

O mapa do aplicativo localiza os nós do servidor seguindo qualquer chamada de dependência HTTP feita entre os servidores com o SDK do Application Insights instalado. Presume-se que cada recurso do Application Insights contenha um servidor.

### <a name="multi-role-app-map-preview"></a>Mapa do aplicativo com várias funções (versão prévia)

O recurso de mapa do aplicativo com várias funções, em fase de versão prévia, permite usar o mapa do aplicativo com vários servidores enviando dados à mesma chave de instrumentação/recurso do Application Insights. Os servidores no mapa são segmentados segundo a propriedade cloud_RoleName nos itens de telemetria. Defina *Mapa do Aplicativo com Várias Funções* como *Ativado* na folha Versões prévias para habilitar essa configuração.

Essa abordagem pode ser desejável em um aplicativo de microsserviço ou em outros cenários em que você deseja correlacionar eventos entre vários servidores dentro de um único recurso do Application Insights.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Comentários
Por favor, faça comentários por meio da opção Comentários no portal.

![Imagem de MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Próximas etapas

* [Portal do Azure](https://portal.azure.com)
