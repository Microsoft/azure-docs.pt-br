---
title: Painel de pedidos do Partner Center na análise do Marketplace comercial | Microsoft AppSource e o Azure Marketplace
description: Saiba como acessar relatórios analíticos sobre seus pedidos de oferta do Marketplace comercial em um formato gráfico e baixável.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561374"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Painel de pedidos na análise do marketplace comercial

Este artigo fornece informações sobre o painel Pedidos no Partner Center. Este painel exibe informações sobre seus pedidos, incluindo tendências de crescimento, apresentadas em um formato gráfico e baixável.

Para acessar o painel pedidos no Partner Center, em **Marketplace comercial**, selecione **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **pedidos**.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [terminologia de análise do Marketplace comercial e perguntas comuns](./analytics-faq.md).

## <a name="orders-dashboard"></a>Painel de pedidos

O painel pedidos exibe os pedidos atuais para todas as ofertas de SaaS (software como serviço). Você pode ver as representações gráficas dos seguintes itens:

- Tendência de pedidos
- Classificações por local e tendências do site
- Pedidos por ofertas e SKUs
- Pedidos por geografia
- Tabela de pedidos detalhado
- Filtros de página de pedidos

> [!NOTE]
> A latência máxima entre a aquisição do cliente e os relatórios no Partner Center é de 48 horas.

## <a name="elements-of-the-orders-dashboard"></a>Elementos do painel pedidos

As seções a seguir descrevem como usar o painel Orders e como ler os dados.

### <a name="month-range"></a>Intervalo de meses

Você pode encontrar uma seleção de intervalo de mês no canto superior direito de cada página. Personalize a saída dos gráficos de páginas de **pedidos** selecionando um intervalo de meses com base nos últimos 6 ou 12 meses ou selecionando um intervalo de mês personalizado com uma duração máxima de 12 meses. O intervalo de mês padrão (período de computação) é de seis meses.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Ilustra os filtros de mês no painel Orders.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de computação selecionado pelo usuário.

### <a name="orders-trend"></a>Tendência de pedidos

Nesta seção, você encontrará o gráfico de **pedidos** que mostra a tendência de suas ordens ativas e canceladas para o período de computação selecionado. As tendências de métrica e de crescimento são representadas por um gráfico de linhas e exibirão o valor de cada mês passando o mouse sobre a linha no gráfico. O valor percentual abaixo das métricas de pedidos no widget representa a quantidade de crescimento ou declínio durante o período de computação selecionado.

Há dois contadores de pedidos: _ativo_ e _cancelado_.

- **Ativo** é igual ao número de pedidos que estão sendo usados atualmente por clientes durante o intervalo de datas selecionado.
- **Cancelado** é igual ao número de pedidos que foram adquiridos anteriormente e cancelados durante o intervalo de datas selecionado.

[![Ilustra o widget Orders no painel Orders que mostra a tendência de pedidos ativos e cancelados.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Pedidos por tendência por estação e site

O gráfico de linhas **order by por estação e baseado em site** representa a métrica e a tendência de SaaS por site e pedidos de SaaS por estação adquiridos pelos clientes (este gráfico inclui pedidos cancelados).

[![Ilustra o widget Orders no painel Orders que mostra as ordens por estação e a tendência do site.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

As ofertas de SaaS podem usar um dos dois modelos de preços com cada plano: uma taxa fixa (baseada em site) ou por usuário (baseada em estação).

- **Taxa fixa**: habilite o acesso à sua oferta com um único preço de taxa mensal ou anual. Isso é, às vezes, chamado de preço baseado em site.
- **Por usuário**: habilite o acesso à sua oferta com um preço com base no número de usuários que podem acessar a oferta ou ocupar estações. Com esse modelo baseado em uso, você pode definir o número mínimo e máximo de usuários com suporte no plano. Você pode criar vários planos para configurar diferentes pontos de preço com base no número de usuários. Esses campos são opcionais. Se deixado desmarcado, o número de usuários será interpretado como não tendo um limite (mínimo de 1 e máximo de quantos o serviço pode dar suporte). Esses campos podem ser editados como parte de uma atualização para seu plano.
- **Cobrança limitada**: além do preço da taxa fixa. Com esse modelo de preços, opcionalmente, você pode definir planos medidos que usam a API do serviço de medição do Marketplace para cobrar os clientes pelo uso que não está coberto pela taxa fixa.

Para obter mais detalhes sobre o assento, o site e a cobrança baseada em medição, consulte [como planejar uma oferta de SaaS para o Marketplace comercial](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Pedidos por ofertas e SKUs

O gráfico pedidos por ofertas e SKU mostra as medidas e as tendências de todas as ofertas:

- As principais ofertas são exibidas no grafo e o restante das ofertas é agrupado como **REST**.
- Você pode selecionar ofertas específicas na legenda para exibir apenas essa oferta e os SKUs associados no grafo.
- Passar o mouse sobre uma fatia no grafo exibe o número de pedidos e a porcentagem dessa oferta em comparação com o número total de pedidos em todas as ofertas.
- A **tendência de pedidos por ofertas** exibe as tendências de crescimento mês a mês. A coluna de mês representa o número de pedidos por nome de oferta. O gráfico de linhas exibe a tendência de percentual de crescimento plotada em um eixo z.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Ilustra o gráfico pedidos por ofertas no painel pedidos.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para exibir a tendência de mês a mês para a oferta, SKUs e estações.

### <a name="orders-by-geography"></a>Pedidos por geografia

Para o período de computação selecionado, o calor exibe o número total de pedidos e a porcentagem de crescimento de pedidos recém-adicionados em uma geografia.  A cor de clara a escura no mapa representa o valor de baixo a alto da contagem de clientes. Selecione um registro na tabela para ampliar um país ou uma região específica.

[![Ilustra o gráfico de dispersão geográfica no painel pedidos.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Observe o seguinte:

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O calor tem uma grade suplementar para exibir os detalhes de contagem de clientes, contagem de pedidos e horas de uso normalizadas para o local específico.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando o botão **página inicial** no mapa.

### <a name="orders-details-table"></a>Tabela de detalhes de pedidos

A tabela detalhes do pedido exibe uma lista numerada dos 1.000 principais pedidos classificados por data de aquisição.

- Cada coluna na grade pode ser classificada.
- Os dados podem ser extraídos para um. CSV ou. Arquivo TSV se a contagem dos registros for menor que 1.000.
- Se o número de registros acima de 1.000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Aplique filtros à tabela **detalhes do pedido** para exibir apenas os dados dos quais você está interessado. Filtrar por país/região, tipo de licença do Azure, tipo de licença do Marketplace comercial, tipo de oferta, status do pedido, trilhas gratuitas, ID da assinatura do Marketplace comercial, ID do cliente e nome da empresa.
- Quando um pedido é adquirido por um cliente protegido, as informações em **pedidos dados detalhados** são mascaradas (* * * * * * * * * * * *).

***Tabela 1: dicionário de termos de dados***

| Nome da coluna em<br>interface do usuário | Nome do atributo | Definição | Nome da coluna em programação<br>relatórios do Access |
| ------------ | ------------- | ------------- | ------------- |
| ID da assinatura do Marketplace | ID da assinatura do Marketplace | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace comercial. Para ofertas de infraestrutura, esse é o GUID de assinatura do Azure do cliente. Para ofertas de SaaS, isso é mostrado como zeros, pois as compras de SaaS não exigem uma assinatura do Azure. | ID da assinatura do Marketplace |
| MonthStartDate | Data de início do mês | Data de início do mês representa o mês de compra. O formato é aaaa-mm-dd. | MonthStartDate |
| Tipo de Oferta | Tipo de Oferta | O tipo de oferta do Marketplace comercial. | OfferType |
| Tipo de licença do Azure | Tipo de licença do Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como Canal. Os valores possíveis são:<ul><li>Provedor de soluções de nuvem</li><li>Enterprise</li><li>Enterprise por meio do revendedor</li><li>Pré-pago</li></ul> | AzureLicenseType |
| Tipo de licença do Marketplace | Tipo de licença do Marketplace | O método de cobrança da oferta do Marketplace comercial. Os valores diferentes são:<ul><li>Cobrado por meio do Azure</li><li>Traga sua própria licença</li><li>Gratuita</li><li>Microsoft como revendedor</li></ul> | MarketplaceLicenseType |
| SKU | SKU | O plano associado à oferta | SKU |
| País/Região do cliente | País/região do cliente | O nome do país/região fornecido pelo cliente. O país/região pode ser diferente do país/região na assinatura do Azure de um cliente. | CustomerCountry |
| É SKU de versão prévia | É SKU de versão prévia | O valor informará se você marcou o SKU como "versão prévia". O valor será "Sim" se o SKU tiver sido devidamente marcado e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem. O valor será "Não" se o SKU não tiver sido identificado como "versão prévia". | IsPreviewSKU |
| ID do pedido | ID do pedido | O identificador exclusivo da ordem do cliente para seu serviço do Marketplace comercial. As ofertas baseadas no uso da máquina virtual não estão associadas a um pedido. | OrderId |
| Order Quantity | Order Quantity | Número de ativos associados à ID do pedido para pedidos ativos | OrderQuantity |
| Nome da instância de nuvem | Nome da instância de nuvem | A Microsoft Cloud em que ocorreu uma implantação de VM. | Nome da instância na nuvem |
| É cliente novo | É cliente novo | O valor identifica se um novo cliente adquiriu uma ou mais de suas ofertas pela primeira vez. O valor será "Sim" se estiver no mesmo mês do calendário que a "data de aquisição". O valor será "não" se o cliente tiver comprado uma das suas ofertas antes do mês do calendário informado. | IsNewCustomer |
| Status do pedido | Status do pedido | O status de uma ordem de Marketplace comercial no momento em que os dados foram atualizados pela última vez. | OrderStatus |
| Data de cancelamento do pedido | Data de cancelamento do pedido | A data em que a ordem do Marketplace comercial foi cancelada. | OrderCancelDate |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura do Azure de um cliente. | CustomerCompanyName |
| Data de compra do pedido | Data de compra do pedido | A data em que a ordem do Marketplace comercial foi criada. O formato é aaaa-mm-dd. | OrderPurchaseDate |
| Nome da oferta | Nome da oferta | O nome da oferta do Marketplace comercial. | OfferName |
| Data de término da avaliação gratuita | Data de término da avaliação gratuita | A data em que o período de avaliação gratuita deste pedido terminará ou terminou. | TrialEndDate |
| ID do Cliente | ID do Cliente | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace. | CustomerId |
| ID da conta de cobrança | ID da conta de cobrança | O identificador da conta na qual a cobrança é gerada. Mapeie a **ID da conta de cobrança** para **CustomerID** para conectar seu relatório de transação de pagamento com os relatórios de cliente, de pedido e de uso. | BillingAccountId |
| AssetCount | Contagem de ativos | O número de ativos associados à ID do pedido. | Preterido |
|||||

### <a name="orders-page-filters"></a>Filtros de página de pedidos

Os filtros de página **pedidos** são aplicados no nível de página pedidos. Você pode selecionar um ou vários filtros para renderizar o gráfico para os critérios escolhidos para exibição e os dados que deseja ver na grade/exportação de dados de pedidos detalhados. Os filtros são aplicados nos dados extraídos para o intervalo de meses que você selecionou no canto superior direito da página pedidos.

> [!TIP]
> Você pode usar o ícone de download no canto superior direito de qualquer widget para baixar os dados. Você pode fornecer comentários sobre cada um dos widgets clicando no ícone "polegar para cima" ou "polegar para baixo".

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial, consulte [acessar relatórios analíticos para o Marketplace comercial no Partner Center](./partner-center-portal/analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre os pedidos em um formato gráfico para download, confira [Painel de pedidos na análise do marketplace comercial](orders-dashboard.md).
- Para as métricas de cobrança de uso e de medição de VM (máquina virtual), confira [Painel de Uso em análises de marketplace comercial](./usage-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./partner-center-portal/downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e AppSource, consulte [classificações & análises painel de análise no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [terminologia e perguntas comuns sobre a análise do Marketplace comercial](./analytics-faq.md).