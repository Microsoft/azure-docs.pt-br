---
title: Painel de uso na análise do Marketplace comercial | Azure Marketplace
description: Saiba como acessar todas as métricas de uso e cobrança limitada para ofertas publicadas no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 765f4f358d593cd5fcc021fbf4ec2276c422c4d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555118"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Painel de uso na análise do marketplace comercial

Este artigo fornece informações sobre o painel de uso no Partner Center. Este painel exibe todas as máquinas virtuais (VM) oferece uso normalizado, uso bruto e métricas de cobrança limitada em três guias separadas: uso normalizado da VM, uso bruto da VM e uso de cobrança medido.

Para acessar o painel de uso no Partner Center, em **Marketplace comercial**, selecione **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **uso**.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [terminologia de análise do Marketplace comercial e perguntas comuns](./analytics-faq.md).

## <a name="usage-dashboard"></a>Painel de uso

O painel de **uso** no menu **analisar** exibe os pedidos atuais para todas as ofertas de SaaS (software como serviço). Você pode ver as representações gráficas dos seguintes itens:

- Tendência de uso
- Uso normalizado por ofertas
- Uso normalizado por outras dimensões: tamanho da VM, canais de vendas e tipos de oferta
- Uso por geografia
- Tabela de uso detalhada
- Filtros de página de pedidos

> [!NOTE]
> A latência máxima entre a geração de eventos de uso e os relatórios no Partner Center é de 48 horas.

## <a name="elements-of-the-usage-dashboard"></a>Elementos do painel de uso

As seções a seguir descrevem como usar o painel uso e como ler os dados.

### <a name="month-range"></a>Intervalo de meses

Você pode encontrar uma seleção de intervalo de mês no canto superior direito de cada página. Personalize a saída dos gráficos de página de **uso** selecionando um intervalo de mês com base nos últimos 6 ou 12 meses ou selecionando um intervalo de mês personalizado com uma duração máxima de 12 meses. O intervalo de mês padrão (período de computação) é de seis meses.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Ilustra os filtros de mês no painel uso.":::

### <a name="usage-trend"></a>Tendência de uso

Nesta seção, você encontrará horas de uso total e tendência para todas as suas ofertas que são consumidas por seus clientes durante o período de computação selecionado. As tendências de métrica e de crescimento são representadas por um gráfico de linhas. Mostre o valor de cada mês passando o mouse sobre a linha no gráfico. O valor percentual abaixo das métricas de uso no widget representa a quantidade de crescimento ou declínio durante o período de computação selecionado.

Há duas representações de horas de uso: uso normalizado da VM e uso bruto da VM.

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- Horas de uso brutos são definidas como a quantidade de tempo que as VMs estão em execução em termos de horas.

[![Ilustra o uso normalizado e os dados de uso brutos no painel uso.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Uso normalizado por ofertas

Esta seção fornece as horas de uso total e a tendência para suas ofertas baseadas em uso no Azure Marketplace. O gráfico de uso normalizado por ofertas é descrito abaixo.

- O gráfico de colunas empilhadas de **uso normalizado** exibe uma divisão de horas de uso normalizadas das cinco principais ofertas de acordo com o período de computação selecionado. As cinco principais ofertas são exibidas em um grafo, enquanto o restante é agrupado na categoria **REST All** .
- O gráfico de colunas empilhadas representa uma tendência de crescimento mês a mês para o intervalo de datas selecionado. As colunas de mês representam horas de uso das ofertas com as horas de uso mais alto para o respectivo mês. O gráfico de linhas mostra a tendência de percentual de crescimento plotada no eixo Y secundário.
- Você pode selecionar ofertas específicas na legenda para exibir somente essas ofertas no grafo.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Ilustra o uso normalizado que oferece dados no painel uso.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para exibir a tendência de uso de mês a mês para a oferta e os SKUs selecionados.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Ilustra as ofertas de uso normalizadas e os dados de SKU no painel de uso.":::

### <a name="orders-by-offers-and-skus"></a>Pedidos por ofertas e SKUs

O gráfico **pedidos por ofertas e SKU** mostra as medidas e as tendências de todas as ofertas. Observe o seguinte:

- As principais ofertas são exibidas no grafo e o restante das ofertas é agrupado como **REST**.
- Você pode selecionar ofertas específicas na legenda para exibir somente essas ofertas no grafo.
- Passar o mouse sobre uma fatia no grafo exibe o número de pedidos e a porcentagem dessa oferta em comparação com o número total de pedidos em todas as ofertas.
- A **tendência de pedidos por ofertas** exibe as tendências de crescimento mês a mês. A coluna de mês representa o número de pedidos por nome de oferta. O gráfico de linhas exibe a tendência de percentual de crescimento plotada no eixo z.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Ilustra o gráfico pedidos por ofertas e SKUs no painel uso.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para exibir a tendência de mês a mês para a oferta, SKUs e estações.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Ilustra o gráfico pedidos por ofertas e SKUs no painel uso. A tendência da oferta, tendência de SKUs e tendências de estações são mostradas.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Uso normalizado por outras dimensões: tamanho da VM, canais de vendas e tipo de oferta

Há três guias para as dimensões: tamanho da VM, canais de vendas e tipo de oferta. Você pode ver as métricas de uso e a tendência de mês a mês em relação a cada uma dessas dimensões.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Ilustra o gráfico de outras dimensões de uso normalizado no painel uso.":::

### <a name="usage-by-geography"></a>Uso por geografia

Para o período de computação selecionado, o calor exibe o uso total em relação à dimensão de Geografia. A cor de clara a escura no mapa representa o valor de baixo a alto da contagem de clientes. Selecione um registro na tabela para ampliar um país/região.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Ilustra o gráfico de país de uso normalizado no painel uso.":::

Observe o seguinte:

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O calor tem uma grade suplementar para exibir os detalhes de contagem de clientes, contagem de pedidos e horas de uso normalizadas no local específico.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando o botão **página inicial** no mapa.

### <a name="usage-details-table"></a>Tabela de detalhes de uso

A tabela **detalhes de uso** exibe uma lista numerada dos principais registros de uso 1.000 classificados por uso. Observe o seguinte:

- Cada coluna na grade pode ser classificada.
- Os dados podem ser extraídos para um. TSV ou. Arquivo CSV se a contagem dos registros for menor que 1.000.
- Se a contagem de registros for superior a 1.000, os dados de exportação serão colocados de forma assíncrona em uma página de downloads que estará disponível para os próximos 30 dias.
- Aplique filtros a **dados de uso detalhados** para exibir somente os dados nos quais você está interessado. Filtrar dados por país/região, canal de vendas, tipo de licença do Marketplace, tipo de uso, nome da oferta, tipo de oferta, avaliações gratuitas, ID de assinatura do Marketplace, ID do cliente e nome da empresa.

_**Tabela 1: dicionário de termos de dados**_

| Nome da coluna em<br>interface do usuário | Nome do atributo | Definição | Nome da coluna em programação<br>relatórios do Access |
| ------------ | ------------- | ------------- | ------------- |
| ID da assinatura do Marketplace | ID da assinatura do Marketplace | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace comercial. Anteriormente, a identificação era o GUID da assinatura do Azure. | MarketplaceSubscriptionId |
| MonthStartDate | Data de início do mês | Data de início do mês representa o mês de compra. | MonthStartDate |
| Tipo de Oferta | Tipo de Oferta | O tipo de oferta do Marketplace comercial. | OfferType |
| Tipo de licença do Azure | Tipo de licença do Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como canal. Os valores possíveis são:<ui><li>Provedor de soluções de nuvem</li><li>Enterprise</li><li>Enterprise por meio do revendedor</li><li>Pré-pago</li></ul> | AzureLicenseType |
| Tipo de licença do Marketplace | Tipo de licença do Marketplace | O método de cobrança da oferta do Marketplace comercial. Os valores possíveis são:<ul><li>Cobrado por meio do Azure</li><li>Traga sua própria licença</li><li>Gratuita</li><li>Microsoft como revendedor</li></ul> | MarketplaceLicenseType |
| SKU | SKU | O plano associado à oferta. | SKU |
| País/Região do cliente | País/região do cliente | O nome do país/região fornecido pelo cliente. O país/região pode ser diferente do país/região na assinatura do Azure de um cliente. | CustomerCountry |
| É SKU de versão prévia | É SKU de versão prévia | O valor mostra se você marcou a SKU como "visualização". O valor será "Sim" se o SKU tiver sido devidamente marcado e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem. O valor será "Não" se o SKU não tiver sido identificado como "versão prévia". | IsPreviewSKU |
| Tipo de cobrança da SKU | Tipo de cobrança da SKU | O tipo de cobrança associado a cada SKU na oferta. Os valores possíveis são:<ul><li>Grátis</li><li>Pago</li></ul> | SKUBillingType |
| Que IsInternal | Preterido | Preterido | Preterido |
| Tamanho da VM | Tamanho da Máquina Virtual | Para tipos de oferta baseados em VM, essa entidade representa o tamanho da VM associada à SKU da oferta. | VMSize |
| Nome da instância de nuvem | Nome da instância de nuvem | A Microsoft Cloud em que ocorreu uma implantação de VM. | Nome da instância na nuvem |
| Mesmo Planoname | Preterido | Preterido (mesma definição que o SKU) | Mesmo Planoname |
| Nome da oferta | Nome da oferta | O nome da oferta do Marketplace comercial. | OfferName |
| DeploymentMethod | Preterido | Preterido (mesma definição que o tipo de oferta) | DeploymentMethod |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente do nome na assinatura do Azure de um cliente. | CustomerCompanyName |
| Data de Uso | Data de Uso | A data de geração de evento de uso para ativos baseados em uso. | UsageDate |
| IsMultisolution | É multisolução | Significa se a oferta é um tipo de oferta de multisolução. | IsMultisolution |
| É cliente novo | Preterido | Preterido | IsNewCustomer |
| Tamanho do núcleo | Tamanho do núcleo | Número de núcleos associados à oferta baseada em VM. | Redimensionar |
| Tipo de uso | Tipo de uso | Significa se o evento de uso associado à oferta é um dos seguintes:<ul><li>Uso normalizado</li><li>Uso bruto</li><li>Uso limitado</li></ul> | UsageType |
| Data de término da avaliação gratuita | Data de término da avaliação gratuita | A data em que o período de avaliação gratuita deste pedido terminará ou terminou. | TrialEndDate |
| CC (Moeda do cliente) | Moeda do cliente | A moeda usada pelo cliente para a transação do Marketplace comercial. | CustomerCurrencyCC |
| Preço (CC) | Preço | Preço unitário da SKU mostrada na moeda do cliente. | PriceCC |
| PC (Moeda de pagamento) | Moeda do pagamento | O Publicador é pago pelos eventos de uso associados ao ativo na moeda configurada pelo Publicador. | PayoutCurrencyPC |
| Preço estimado (PC) | Preço estimado | Preço unitário da SKU na moeda configurada pelo Publicador. | EstimatedPricePC |
| Referência do uso | Referência do uso | Um GUID concatenado que é usado para conectar o relatório de uso (na análise do Marketplace comercial) com o relatório de transação de pagamento. A referência de uso é conectada a OrderId e LineItemId no relatório de transação de pagamento. | UsageReference |
| Unidade de uso | Unidade de uso | Unidade de consumo associada à SKU. | UsageUnit |
| ID do Cliente | ID do Cliente | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace. | CustomerId |
| ID da conta de cobrança | ID da conta de cobrança | O identificador da conta na qual a cobrança é gerada. Mapeie a **ID da conta de cobrança** para **CustomerID** para conectar seu relatório de transação de pagamento com os relatórios de cliente, de pedido e de uso. | BillingAccountId |
| Quantidade de uso | Quantidade de uso | As unidades de uso total consumidas pelo ativo implantado pelo cliente.<br>Isso se baseia no item de tipo de uso. Por exemplo, se o tipo de uso for uso normalizado, a quantidade de uso será para uso normalizado. | UsageQuantity |
| NormalizedUsage | Uso normalizado | O total de unidades de uso normalizadas consumidas pelo ativo que é implantado pelo cliente.<br>As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM]. | NormalizedUsage |
| MeteredUsage | Uso medido | As unidades de uso total consumidas pelos medidores configurados com a oferta implantada pelo cliente. | MeteredUsage |
| RawUsage | Uso bruto | As unidades de uso bruto total consumidas pelo ativo que é implantado pelo cliente.<br>Horas de uso brutos são definidas como a quantidade de tempo que as VMs estão em execução em termos de unidades de uso. | RawUsage |
| Custo estendido estimado (CC) | Encargo estendido estimado na moeda do cliente | Significa os encargos associados ao uso. A coluna é o produto de preço (CC) e quantidade de uso. | EstimatedExtendedChargeCC |
| Custo estendido estimado (PC) | Encargo estendido estimado na moeda do pagamento | Significa os encargos associados ao uso. A coluna é o produto de preço estimado (PC) e quantidade de uso. | EstimatedExtended ChargePC |
|||||

### <a name="usage-page-filters"></a>Filtros de página de uso

Os filtros de página de **uso** são aplicados no nível de página pedidos. Você pode selecionar um ou vários filtros para renderizar o gráfico para os critérios escolhidos para exibição e os dados que deseja ver na grade/exportação dos dados dos pedidos de uso. Os filtros são aplicados nos dados extraídos para o intervalo de meses que você selecionou no canto superior direito da página uso.

O relatório de widgets e de exportação para uso bruto da VM são semelhantes ao uso normalizado da VM com as seguintes distinções:

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- Horas de uso brutos são definidas como a quantidade de tempo que as VMs estão em execução em termos de unidades de uso.

### <a name="metered-billing-usage"></a>Uso de cobrança medido

A guia **uso medido** mostra informações de uso para os tipos de oferta em que o uso é medido pela dimensão por medidor. O tipo de oferta de SaaS excedente é apresentado no momento. A guia apresenta representações gráficas de tendências excedentes para o uso de cobrança limitada de SaaS:

- **Tendência excedente por dimensão de medidor**: Exibe a tendência excedente mensal para a dimensão de medidor selecionada de uma oferta. O eixo X representa o mês e o eixo Y representa a quantidade de uso excedente. A unidade de medida do medidor personalizado também é exibida no eixo Y.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Ilustra o gráfico de uso de VM normalizado no painel uso.":::

- **Tendência de excedente por SKU**: representa a tendência de quantidade de uso da dimensão de medidor selecionada por SKU/planos. Os cinco principais planos com a maior quantidade de uso da oferta selecionada são exibidos.

- **Tendência de excedente por clientes**: o quadro líder do cliente representa uma listagem empilhada de clientes com as horas de uso mais altas e são exibidas em uma _placa líder_, classificada pelo uso mais alto do medidor personalizado. Selecione um cliente no quadro de preenchimento para exibir a tendência de uso excedente de uma dimensão de medidor selecionada.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Ilustra o gráfico uso medido por clientes no painel uso.":::

Se você tiver várias ofertas que usam medidores personalizados, o relatório uso de cobrança medido mostrará informações de uso para todas as suas ofertas, de acordo com suas dimensões de medidor personalizadas.

> [!TIP]
> Você pode usar o ícone de download no canto superior direito de qualquer widget para baixar os dados. Você pode fornecer comentários sobre cada um dos widgets clicando no ícone "polegar para cima" ou "polegar para baixo".

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial, consulte [acessar relatórios analíticos para o Marketplace comercial no Partner Center](./partner-center-portal/analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md)
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](usage-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./partner-center-portal/downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e Microsoft AppSource, consulte [classificações & análises painel de análise no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [terminologia e perguntas comuns sobre a análise do Marketplace comercial](./analytics-faq.md).
