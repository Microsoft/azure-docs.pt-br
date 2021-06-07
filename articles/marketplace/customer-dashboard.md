---
title: Painel de clientes no Microsoft Commercial Marketplace Analytics no Partner Center, no Azure Marketplace e no Microsoft AppSource
description: Saiba como acessar informações sobre seus clientes, incluindo tendências de crescimento, usando o painel clientes na análise de Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c5c50787ef3e287d164e051ece26da4e83199d47
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555628"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Painel de clientes na análise de Marketplace comercial

Este artigo fornece informações sobre o painel clientes no Partner Center. Esse painel exibe informações sobre os clientes, incluindo tendências de crescimento, apresentadas em um formato gráfico para download.

Para acessar o painel clientes no Partner Center, em **Marketplace comercial** , selecione **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **clientes**.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [terminologia de análise do Marketplace comercial e perguntas comuns](./analytics-faq.md).

## <a name="customers-dashboard"></a>Painel dos clientes

O painel clientes exibe dados para clientes que adquiriram suas ofertas. Você pode ver as representações gráficas dos seguintes itens:

- Tendência de clientes ativos e com rotatividade
- Tendência de crescimento do cliente, incluindo clientes existentes, novos e com rotatividade
- Clientes por pedidos e uso
- Percentil de clientes 
- Tipo de cliente por pedidos e uso
- Clientes por região
- Tabela de detalhes de clientes
- Filtros de página de clientes

> [!NOTE]
> A latência máxima entre a aquisição do cliente e os relatórios no Partner Center é de 48 horas.

## <a name="elements-of-the-customers-dashboard"></a>Elementos do painel clientes

As seções a seguir descrevem como usar o painel clientes e como ler os dados.

### <a name="month-range"></a>Intervalo de meses

Você pode encontrar uma seleção de intervalo de mês no canto superior direito de cada página. Personalize a saída dos gráficos de páginas de **clientes** selecionando um intervalo de meses com base nos últimos 6 ou 12 meses ou selecionando um intervalo de mês personalizado com uma duração máxima de 12 meses. O intervalo de mês padrão (período de computação) é de seis meses.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Ilustra os filtros de mês na página clientes.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de computação selecionado pelo usuário.

### <a name="active-and-churned-customers-trend"></a>Tendência de clientes ativos e com rotatividade

Nesta seção, você encontrará a tendência de crescimento de seus clientes para o período de computação selecionado. As tendências de métrica e de crescimento são representadas por um gráfico de linhas e exibem o valor de cada mês passando o mouse sobre a linha no gráfico. O valor percentual abaixo **dos clientes ativos** no widget representa a quantidade de crescimento durante o período de computação selecionado. Por exemplo, a captura de tela a seguir mostra um crescimento de 0,92% para o período de computação selecionado.

[![Ilustra o widget clientes na página clientes.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Há três _tipos de clientes_: novos, existentes e rotativos.

- Um cliente novo adquiriu uma ou mais ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais ofertas antes do mês selecionado.
- Um cliente rotativo cancelou todas as ofertas adquiridas anteriormente. Os clientes com rotatividade são representados no eixo negativo no widget de tendência.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Tendência de crescimento do cliente, incluindo clientes existentes, novos e com rotatividade

Nesta seção, você encontrará a tendência e a contagem de todos os clientes, incluindo novos, existentes e com variação, com uma tendência de crescimento mensal.

- O gráfico de linhas representa as porcentagens de crescimento geral do cliente.
- A coluna Mês representa a contagem de clientes empilhados por clientes novos, existentes e rotativos.
- A contagem de clientes com variação é exibida na direção negativa do eixo X.
- Você pode selecionar itens de legenda específicos para exibir exibições mais detalhadas. Por exemplo, selecione novos clientes na legenda para exibir somente novos clientes.
- Passar o mouse sobre uma coluna no gráfico exibe detalhes somente para esse mês.

[![Ilustra o widget tendência de clientes na página clientes.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Clientes por pedidos/uso

O gráfico de **pedidos/uso do cliente** tem três guias: pedidos, uso normalizado e uso bruto. Selecione a guia **pedidos** para mostrar os detalhes da ordem.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Ilustra a guia Orders do widget Customers by Orders and Usage na página Customers.":::

Observe o seguinte:

- O quadro líder apresenta detalhes dos clientes classificados por contagem de pedidos. Depois de selecionar um cliente, os detalhes do cliente são apresentados nas seções "detalhes", "pedidos por SKUs" e "SKUs por estações" adjacentes.
- Os detalhes do perfil do cliente são exibidos nesse espaço quando os Publicadores são conectados com uma função de proprietário. Se os Publicadores estiverem conectados com uma função de colaborador, os detalhes nesta seção não estarão disponíveis.
- O gráfico de rosca **pedidos por SKUs** exibe a divisão de pedidos comprados para planos. Os cinco principais planos com a contagem de pedidos mais alto são exibidos, enquanto o restante dos pedidos é agrupado em **repouso All**.
- O gráfico de rosca **SKUs por estações** exibe a divisão de estações solicitadas para planos. Os cinco principais planos com as estações mais altas são exibidos, enquanto o restante dos pedidos é agrupado em **REST todos**.

Você também pode selecionar a guia **uso normalizado** ou **uso bruto** para exibir os detalhes de uso.

- O quadro principal apresenta detalhes dos clientes classificados por contagem de horas de uso. Depois de selecionar um cliente, os detalhes do cliente são apresentados na seção "detalhes" adjacentes, "uso normalizado por ofertas" e "uso normalizado por tamanho de VM (máquina virtual)".
- Os detalhes do perfil do cliente são exibidos nesse espaço quando os editores estão conectados com uma função de proprietário. Se os editores estiverem conectados com uma função de colaborador, os detalhes nesta seção não estarão disponíveis.
- O gráfico de rosca **uso normalizado por ofertas** exibe a divisão do uso consumido pelas ofertas. Os cinco principais planos com a contagem de uso mais alta são exibidos, enquanto o restante das ofertas é agrupado em **repouso All**.
- O gráfico de rosca **uso normalizado por tamanho de VM** exibe a divisão de uso consumida por diferentes tamanhos de VM. Os cinco principais tamanhos de VM com o uso normalizado mais alto são exibidos, enquanto o restante do uso é agrupado em **REST todos**.

### <a name="top-customers-percentile"></a>Principais clientes percentil

O gráfico **principal do percentil de clientes** tem três guias, "pedidos", "uso normalizado" e "uso bruto". O _principal percentual do cliente_ é exibido ao longo do eixo x, conforme determinado pelo número de pedidos. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (grafo de linhas) exibe a porcentagem cumulativa do número total de pedidos. Você pode exibir os detalhes passando o mouse sobre os pontos ao longo do gráfico de linhas.

[![Ilustra a guia Orders do widget top Customer percentil na página Customers.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Tipo de cliente por pedidos e uso

O gráfico **Pedidos/uso por tipo de cliente** exibe o número de pedidos, uso normalizado e horas de uso bruto divididos entre clientes novos e existentes, com base na seleção de pedidos, uso normalizado e uso bruto no gráfico, respectivamente.

Este gráfico mostra o seguinte:

- Um cliente novo adquiriu uma ou mais ofertas ou relatou o uso pela primeira vez no mesmo mês do calendário (eixo y).
- Um cliente existente já adquiriu uma oferta de você ou relatou o uso anterior ao mês do calendário relatado (no eixo y).

[![Ilustra a guia pedidos do widget pedidos por tipo de cliente na página clientes.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Clientes por região

Para o período de computação selecionado, o calor exibe o número total de clientes e a porcentagem de clientes recém-adicionados em relação à dimensão de Geografia. A cor de clara a escura no mapa representa o valor de baixo a alto da contagem de clientes. Selecione um registro na tabela para ampliar um país ou região.

[![Ilustra a guia pedidos do widget pedidos por geografia na página clientes.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Observe o seguinte:

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O mapa de calor possui uma grade suplementar para exibir os detalhes da contagem de clientes, contagem de pedidos, horas normalizadas de uso na localização específica.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando o botão **página inicial** no mapa.

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **detalhes do cliente** exibe uma lista numerada dos principais clientes 1.000 classificados pela data em que adquiriram primeiro uma de suas ofertas. Você pode expandir uma seção selecionando o ícone de expansão na faixa de seleção detalhes.

Observe o seguinte:

- As informações pessoais do cliente são disponibilizadas se o cliente consentir. Você só poderá exibir essas informações se tiver entrado com um nível de função de proprietário de permissões.
- Cada coluna na grade pode ser classificada.
- Os dados podem ser extraídos para um. CSV ou. Arquivo TSV se a contagem dos registros for menor que 1.000.
- Se o número de registros for maior que 1.000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Aplique filtros à tabela para exibir apenas os dados nos quais você está interessado. Filtrar dados por nome da empresa, ID do cliente, ID da assinatura do Marketplace, tipo de licença do Azure, data de aquisição, data de perda, email do cliente, país/região/estado/cidade/zip, idioma do cliente e assim por diante.
- Quando uma oferta é adquirida por um cliente protegido, as informações em **Dados detalhados do cliente** são mascaradas (* * * * * * * * * * * *).
- Os detalhes de dimensão do cliente, como o nome da empresa, o nome do cliente e o email do cliente, estão em um nível de ID da organização, não no nível de transação do Azure Marketplace ou do Microsoft AppSource.

_**Tabela 1: dicionário de termos de dados**_

| Nome da coluna em<br>interface do usuário | Nome do atributo | Definição | Nome da coluna em programação<br>relatórios do Access |
| ------------ | ------------- | ------------- | ------------- |
| ID da assinatura do Marketplace | ID da assinatura do Marketplace | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace comercial. Para ofertas de infraestrutura, esse é o GUID de assinatura do Azure do cliente. Para ofertas de SaaS, isso é mostrado como zeros, pois as compras de SaaS não exigem uma assinatura do Azure. | MarketplaceSubscriptionId |
| DateAcquired | Data da aquisição | A primeira data em que o cliente comprou qualquer oferta que você publicou. | DateAcquired |
| DateLost | Data da perda | A última data em que o cliente cancelou o último de todas as ofertas adquiridas anteriormente. | DateLost |
| Nome do Provedor | Nome do Provedor | O nome do provedor envolvido na relação entre a Microsoft e o cliente. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP. | ProviderName |
| Email do provedor | Email do provedor | O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP. | ProviderEmail |
| FirstName | Nome do cliente | O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na assinatura do Azure de um cliente. | FirstName |
| LastName | Sobrenome do cliente | O último nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na assinatura do Azure de um cliente. | LastName |
| Email | Email do cliente | O endereço de email fornecido pelo cliente final. O email pode ser diferente do endereço de email na assinatura do Azure de um cliente. | Email |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura do Azure de um cliente. | Nome do CustomerCompany |
| Cidade do cliente | Cidade do cliente | O nome da cidade fornecido pelo cliente. A cidade pode ser diferente da cidade na assinatura do Azure de um cliente. | Cidade do cliente |
| Código postal do cliente | Código postal do cliente | O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na assinatura do Azure de um cliente. | Código CustomerPostal |
| Cultura de comunicação do cliente | Idioma de comunicação do cliente | O idioma preferido pelo cliente para comunicação. | Cultura de comunicação do cliente |
| Região/país do cliente | País/região do cliente | O nome do país/região fornecido pelo cliente. O país/região pode ser diferente do país/região na assinatura do Azure de um cliente. | Região/país do cliente |
| AzureLicenseType | Tipo de licença do Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como _canal_. Os valores possíveis são:<ul><li>Provedor de soluções de nuvem</li><li>Enterprise</li><li>Enterprise por meio do revendedor</li><li>Pré-pago</li></ul> | AzureLicenseType |
| PromotionalCustomers | Aceitou contato promocional | O valor indicará que o cliente aceitou proativamente o contato promocional dos fornecedores. Não estamos apresentando a opção aos clientes no momento; portanto, indicamos "Não" de forma generalizada. Depois que esse recurso for implantado, começaremos a atualizar de acordo. | PromotionalCustomers |
| Customerstate | Estado do cliente | O estado de residência fornecido pelo cliente. O estado pode ser diferente do estado fornecido na assinatura do Azure de um cliente. | Customerstate |
| CommerceRootCustomer | Cliente raiz do Commerce | Uma ID de conta de cobrança pode ser associada a várias IDs de cliente.<br>Uma combinação de uma ID de conta de cobrança e uma ID de cliente pode ser associada a várias assinaturas do Marketplace comercial.<br>O cliente raiz de comércio significa o nome do cliente da assinatura. | CommerceRootCustomer |
| ID do Cliente | ID do Cliente | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace. | CustomerId |
| ID da conta de cobrança | ID da conta de cobrança | O identificador da conta na qual a cobrança é gerada. Mapeie a **ID da conta de cobrança** para **CustomerID** para conectar seu relatório de transação de pagamento com os relatórios de cliente, de pedido e de uso. | BillingAccountId |
|||||

### <a name="customers-page-filters"></a>Filtros de página de clientes

Os filtros da página Clientes são aplicados no nível da página Clientes. Você pode selecionar vários filtros para renderizar o gráfico para os critérios escolhidos para exibição e os dados que deseja ver na grade/exportação de "dados de pedidos detalhados". Os filtros são aplicados nos dados extraídos para o intervalo de meses que você selecionou no canto superior direito da página clientes.

> [!TIP]
> Você pode usar o ícone de download no canto superior direito de qualquer widget para baixar os dados. Você pode fornecer comentários sobre cada um dos widgets clicando no ícone "polegar para cima" ou "polegar para baixo".

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial, consulte [acessar relatórios analíticos para o Marketplace comercial no Partner Center](./partner-center-portal/analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./partner-center-portal/downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e Microsoft AppSource, consulte [classificações & análises painel de análise no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [terminologia e perguntas comuns sobre a análise do Marketplace comercial](./analytics-faq.md).
