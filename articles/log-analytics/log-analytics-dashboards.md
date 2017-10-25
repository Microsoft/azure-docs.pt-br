---
title: Criar um painel personalizado no Azure Log Analytics | Microsoft Docs
description: "Este guia ajuda a entender como os painéis do Log Analytics podem visualizar todas as pesquisas de log salvas, dando a você uma lente única para exibir seu ambiente."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a90d9c620221bffbb225fb060b997af2f5e90390
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Criar um painel personalizado para uso no Log Analytics

>[!NOTE]
> Se o seu espaço de trabalho tiver sido atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), você não poderá criar novos painéis ou editar os painéis existentes. 

Este guia ajuda a entender como os painéis do Log Analytics podem visualizar todas as pesquisas de log salvas, dando a você uma lente única para exibir seu ambiente.

![Painel de Exemplo](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Todos os painéis personalizados que você cria no portal do OMS também estão disponíveis no aplicativo móvel do OMS. Consulte as páginas a seguir para obter mais informações sobre os aplicativos.

* [Aplicativo móvel do OMS na Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Aplicativo móvel do OMS no Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![painel móvel](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Como crio meu painel?
Para começar, vá para a página Visão Geral do OMS. Você verá o bloco **Meu Painel** à esquerda. Clique para fazer uma busca detalhada do painel.

![Visão geral](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Adicionando um bloco
Nos painéis, os blocos são ativados pelas pesquisas de log salvas. O OMS vem com muitas pesquisas de log salvas previamente, portanto, você pode começar imediatamente. Use as seguintes etapas que descrevem como começar.

No modo de exibição Meu Painel, basta clicar em **Personalizar** para entrar no modo de personalização.

![Ilustração](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 O painel que é aberto no lado direito da página mostra todas as pesquisas de log salvas do seu espaço de trabalho. Para visualizar uma pesquisa de log salva como um bloco, passe o mouse sobre uma pesquisa salva e, em seguida, clique no símbolo de **mais**.

![Adicionar Blocos 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Quando você clicar no símbolo de **mais**, um novo bloco será exibido na exibição Meu Painel.

![Adicionar Blocos 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Editar um bloco
Na exibição Meu Painel, basta clicar  em **Personalizar** para entrar no modo de personalização. Clique no bloco que você deseja editar. O painel direito muda para editar e fornece uma seleção de opções:

![Editar Bloco](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Editar Bloco](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Visualizações do bloco
Há três tipos de visualização do bloco para escolher:

| tipo de gráfico | o que ele faz |
| --- | --- |
| ![Gráfico de Barras](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Exibe uma linha do tempo dos resultados salvos da pesquisa de log como um gráfico de barras ou uma lista de resultados por campo, dependendo se sua pesquisa de log agrega os resultados por campo ou não. |
| ![métrica](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Exibe as visitas totais dos resultados da pesquisa de log como um número em um bloco. Os blocos da métrica permitem que você defina um limite que destacará o bloco quando o limite for atingido. |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |Exibe uma linha do tempo das ocorrências dos resultados da pesquisa de log salva com valores como um gráfico de linhas. |

### <a name="threshold"></a>Limite
Você pode criar um limite em um bloco usando a visualização Métrica. Selecione para criar um valor de limite no bloco. Escolha se é para destacar o bloco quando o valor fica acima ou abaixo do limite escolhido, então, defina o valor de limite abaixo.

## <a name="organizing-the-dashboard"></a>Organizando o painel
Para organizar seu painel, navegue até a exibição Meu Painel e clique em **Personalizar** para entrar no modo de personalização. Clique e arraste o bloco que você deseja mover e mova-o para onde deseja que fique.

![Organizar seu Painel](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Remover um bloco
Para remover um bloco, navegue até a exibição Meu Painel e clique em **Personalizar** para entrar no modo de personalização. Selecione o bloco que deseja remover e clique em **Remover Bloco**no painel direito.

![Remover um bloco](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Próximas etapas
* Crie [alertas](log-analytics-alerts.md) no Log Analytics para gerar notificações e para corrigir problemas.
