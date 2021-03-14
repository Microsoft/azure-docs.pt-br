---
title: Painel de Insights do Marketplace na análise do marketplace comercial
description: Acesse um resumo do Marketplace Web Analytics no Partner Center, que permite que você meça o envolvimento do cliente no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553928"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Painel de Insights do Marketplace na análise do marketplace comercial

Este artigo fornece informações sobre o painel Insights do Marketplace no Partner Center. Este painel exibe um resumo do Web Analytics do Marketplace comercial que permite que os editores meçam o envolvimento do cliente para suas respectivas páginas de detalhes do produto listadas nas lojas online do Marketplace comercial: Microsoft AppSource e Azure Marketplace.

Para acessar o painel do **Marketplace insights** no Partner Center, em Marketplace comercial, selecione **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** o  >  **Marketplace insights**.

Para obter definições detalhadas de terminologia de análise, consulte [terminologia de análise do Marketplace comercial e perguntas comuns](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Painel Insights do Marketplace

O painel do Marketplace insights apresenta uma visão geral do Azure Marketplace e do AppSource oferece desempenho de negócios. Este painel fornece uma ampla visão geral do seguinte:

- Tendência de visitas à página
- Plano de tendência de ações
- Visitas à página e chamadas para ações em ofertas, domínios de referência e IDs de campanha
- Informações do Marketplace por geografia
- Tabela de detalhes do Insights do Marketplace

O painel do Marketplace insights fornece dados de cliques, que não devem ser correlacionados com clientes potenciais gerados no ponto de extremidade de destino do cliente potencial.

> [!NOTE]
> A latência máxima entre os usuários que visitam ofertas no Azure Marketplace ou AppSource e relatórios no Partner Center é de 48 horas.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementos do painel do Marketplace insights

O painel do Marketplace insights exibe detalhes de telemetria da Web para o Azure Marketplace e AppSource em duas guias separadas. As seções a seguir descrevem como usar o painel do Marketplace insights e como ler os dados.

### <a name="month-range"></a>Intervalo de meses

Você pode encontrar uma seleção de intervalo de mês no canto superior direito de cada página. Personalize a saída dos gráficos de página do **Marketplace insights** selecionando um intervalo de mês com base nos últimos 6 ou 12 meses ou selecionando um intervalo de mês personalizado com uma duração máxima de 12 meses. O intervalo de mês padrão (período de computação) é de seis meses.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Ilustra os filtros de mês no painel do Marketplace insights.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de computação selecionado pelo usuário.

### <a name="page-visits-trends"></a>Tendências de visitas à página

O gráfico **visitantes** do Marketplace insights exibe uma contagem de _visitas à página_ e _visitantes exclusivos_ para o período de computação selecionado.

**Visitas à página**: esse número representa a contagem de sessões de usuário distintas na página de listagem de oferta (página de detalhes do produto) para um período de computação selecionado. Os indicadores de porcentagem vermelha e verde representam a porcentagem de crescimento de visitas à página. O gráfico de tendência representa a contagem mês a mês de visitas à página.

**Visitantes exclusivos**: esse número representa a contagem de visitantes distinta durante o período de computação selecionado para as ofertas no Azure Marketplace e AppSource. Um visitante que visitou uma ou mais páginas de detalhes do produto será contado como um único visitante.

[![Ilustra o gráfico de visitantes no painel do Marketplace insights.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Plano de tendência de ações

Esse número representa a contagem de cliques de botão de **chamada para ação** concluídas na página de listagem de oferta (página de detalhes do produto). As _chamadas para a ação_ são contadas quando os usuários selecionam os botões **obter agora**, **avaliação gratuita**, **contato me** ou **Test Drive** .

[![Ilustra a chamada para o gráfico de ações no painel do Marketplace insights.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Visitas à página e chamadas para ações em ofertas, domínios de referência e IDs de campanha

**Domínios de referência**: selecionar um domínio de referência específico mostra a tendência mensal de visitas à página e chamadas para ação no gráfico à direita.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Ilustra o gráfico de domínio de referência no painel do Marketplace insights.":::

**Ofertas**: selecione uma oferta específica para ver a tendência mensal de visitas à página e chamadas para ação no gráfico à direita.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Ilustra o gráfico de alias de oferta no painel do Marketplace insights.":::

**IDs de campanha**: selecionando uma ID de campanha específica, você deve ser capaz de entender o sucesso da campanha. Para cada campanha, você deve ser capaz de ver a tendência mensal de visitas à página e chamadas para ação no gráfico à direita.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Ilustra o gráfico de campanha no painel do Marketplace insights.":::

### <a name="marketplace-insights-by-geography"></a>Informações do Marketplace por geografia

Para o período de computação selecionado, o calor exibe a contagem de visitas à página, visitantes exclusivos e chamadas para ação (CTA). A cor de luz para escuro no mapa representa o valor de baixo para alto dos visitantes exclusivos. Selecione um registro na tabela para ampliar um país/região.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Ilustra o gráfico de dispersão geográfica no painel do Marketplace insights.":::

Observe o seguinte:

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O calor tem uma grade suplementar para exibir os detalhes de contagem de clientes, contagem de pedidos e horas de uso normalizadas no local específico.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando o botão **página inicial** no mapa.

### <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Insights do Marketplace

Esta tabela fornece uma exibição de lista das visitas à página e as chamadas para ação de suas páginas ofertas selecionadas por data.

- Os dados podem ser extraídos para um. TSV ou. Arquivo CSV se a contagem de registros for menor que 1.000.
- Se a contagem de registros for superior a 1.000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Filtre os dados por nomes de oferta e nomes de campanha para exibir os dados dos quais você está interessado.

> [!TIP]
> Você pode usar o ícone de download no canto superior direito de qualquer widget para baixar os dados. Você pode fornecer comentários sobre cada um dos widgets clicando no ícone "polegar para cima" ou "polegar para baixo".

| Nome da coluna em<br>interface do usuário | Nome do atributo | Definição | Nome da coluna em programação<br>relatórios do Access |
| ------------ | ------------- | ------------- | ------------- |
| Data | Data de visita | A data de visita da página e/ou CTA clique em geração de eventos na página da oferta no Azure Marketplace e/ou AppSource. | Data |
| Nome da oferta | Nome da oferta | O nome da oferta do Marketplace comercial. | OfferName |
| Domínio de referência | Domínio de referência | O nome do domínio de referência do qual a página foi visitada. Se não houver nenhum domínio de referência capturado para a página, a entrada correspondente será "domínio de referência não presente". |  ReferralDomain |
| Nome do país | Nome do país | O nome do país de onde ocorreu a página de visita. | CountryName |
| Visitas à página | Visitas à página | O número de visitas à página associadas ao nome da oferta para uma determinada data. | PageVisits |
| Obtenha agora | Obtenha agora | O número de cliques para o CTA "obter agora" na página da oferta de uma determinada data. | GetItNow |
| Entre em contato comigo | Entre em contato comigo | O número de cliques para o CTA "entre em contato comigo" na página da oferta de uma determinada data. | ContactMe |
| Test drive | Test drive | O número de cliques para o CTA de "Test Drive" na página da oferta de uma determinada data. | TestDrive |
| Avaliação gratuita | Avaliação gratuita | O número de cliques para o CTA de "avaliação gratuita" na página da oferta de uma determinada data. | FreeTrial |
|||||

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial, consulte [acessar relatórios analíticos para o Marketplace comercial no Partner Center](./partner-center-portal/analytics.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./partner-center-portal/downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e AppSource, consulte [classificações & análises painel de análise no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [terminologia e perguntas comuns sobre a análise do Marketplace comercial](./analytics-faq.md).