---
title: Atualizando suas visualizações do painel de Log Analytics
description: Saiba como atualizar suas visualizações de Log Analytics Dashboard com consultas que podem fornecer ideias poderosas.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030929"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Atualizando suas visualizações do painel de Log Analytics

Em fevereiro de 2020, apresentamos uma tecnologia de visualização aprimorada. Aprimorando e aprimorando sua capacidade de visualizar os resultados da consulta e alcançar ideias poderosas, rapidamente. 

Você pode ler mais sobre essa atualização nesta [atualização do Azure](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Essa nova tecnologia de visualização é abrindo o caminho para experiências novas e aprimoradas em relação ao conjunto de resultados da consulta. 

## <a name="dashboards-in-azure"></a>Painéis no Azure

Os painéis do Azure são uma maneira de visualizar o status de toda a área de superfície do Azure. Elas foram projetadas para fornecer um único painel de vidro para o status de imóveis do Azure e permitir uma variedade de atalhos para ações comuns. 

Para obter mais informações, consulte [painéis do Azure](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Atualizando Log Analytics partes do painel

A nova tecnologia de visualização aborda alguns problemas comuns com a antiga implementação e apresenta alguns recursos novos para Log Analytics partes fixadas: 

- **Mesmos tipos disponíveis** – todos os tipos de visualização disponíveis no log Analytics estão disponíveis como partes fixas em painéis.

- **Aparência e sensação consistentes** – a aparência da visualização para partes fixadas agora é quase idêntica àquela em log Analytics. As diferenças são devido a otimizações que exigem diferenças sutis no conteúdo de dados do Visual. Consulte a parte considerações deste documento para obter mais informações sobre essas diferenças.

- **Dicas de ferramentas e rótulos** – novas visualizações log Analytics dão suporte a dicas de ferramentas. Os gráficos de pizza e de rosca agora dão suporte a rótulos.

- **Legendas interativas** – clicar na legenda de visualização permite adicionar/remover dimensões do Visual fixado como em log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Estágio 1-mensagem de atualização de aceitação

Quando uma parte fixa Log Analytics é capaz de ser atualizada, uma nova notificação de *aceitação* é exibida em log Analytics partes fixadas nos dashboards, permitindo que os usuários atualizem sua visualização. Se você quiser experimentar as novas visualizações para atualizar as visualizações selecionadas em seu painel.

 
![Barra lateral](media/dashboard-upgrade/update-message-1.png)
 
![Captura de tela que mostra como atualizar a visualização do bloco.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Depois que o painel é publicado, a atualização é irreversível. No entanto, as alterações serão descartadas se você navegar para fora do painel sem publicar novamente.  

Depois de clicar, a visualização será atualizada para a nova tecnologia. Diferenças sutis na visualização podem ocorrer para alinhar-se à sua aparência no Log Analytics.

Depois que as visualizações forem atualizadas, você precisará republicar seu painel para que a alteração entre em vigor.

![Captura de tela que mostra as visualizações atualizadas.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Estágio 2-migração de todos os painéis

Depois que um período de aceitação inicial terminar, a equipe de Log Analytics atualizará todos os painéis no sistema. Alinhar todos os painéis do Azure permite que a equipe apresente mais visualizações e aprimoramentos de experiência em todo o quadro.

## <a name="considerations"></a>Considerações

Log Analytics visualizações fixadas em um painel têm algum comportamento específico projetado para uma experiência ideal. Examine as considerações de design a seguir ao fixar uma visualização em um painel.

### <a name="query-time-scope---30-day-limit"></a>Escopo do tempo de consulta-limite de 30 dias

Como os painéis podem conter várias visualizações de várias consultas, o escopo de tempo para uma consulta fixada única é limitado a 30 dias. Uma única consulta pode ser executada apenas em um período menor ou igual a 30 dias. Essa limitação é para garantir um tempo de carregamento razoável do painel.

### <a name="query-data-values---25-values-and-other-grouping"></a>Valores de dados de consulta-25 valores e outros agrupamentos

Os painéis podem ser visualmente densos e complexos. Para reduzir a carga cognitiva ao exibir um painel, otimizamos as visualizações limitando a exibição a 25 tipos de dados diferentes. Quando há mais de 25, Log Analytics otimiza os dados. Ele mostra individualmente os 25 tipos com a maioria dos dados como separados e, em seguida, agrupa os valores restantes em um valor "other". O gráfico a seguir mostra esse caso.  

![Captura de tela que mostra um painel com 25 tipos de dados diferentes.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Atualização do painel ao carregar

Os painéis são atualizados na carga. Todas as consultas relacionadas a visualizações de Log Analytics fixadas pelo Dashboard são executadas e o painel é atualizado quando é carregado. Se a página do painel permanecer aberta, os dados no painel serão atualizados a cada 60 minutos.

## <a name="next-steps"></a>Próximas etapas

[Criar e compartilhar dashboards no Log Analytics](../visualize/tutorial-logs-dashboards.md)