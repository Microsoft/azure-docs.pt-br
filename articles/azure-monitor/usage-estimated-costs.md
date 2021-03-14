---
title: Monitorar o uso e os custos estimados no Azure Monitor
description: Visão geral do processo de utilização da página de custos estimados e uso do Azure Monitor
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.openlocfilehash: c4aded73334e38539e1c671831fe812a9525698c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048668"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorar o uso e os custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure. Os artigos relacionados para componentes específicos do Azure Monitor incluem:
> - [Gerenciar o uso e os custos com logs de Azure monitor](logs/manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar o uso de dados.
> - [Gerenciar o uso e os custos de Application insights](app/pricing.md) descreve como analisar o uso de dados no Application insights.

## <a name="azure-monitor-pricing-model"></a>Modelo de preços de Azure Monitor

O modelo de cobrança de Azure Monitor básica é um preço baseado em consumo, compatível com a nuvem ("pré-pago"). Você paga apenas pelo que usa. Os detalhes de preços estão disponíveis para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Além do modelo pago conforme o uso para dados de log, Log Analytics tem reservas de capacidade, o que permite que você economize até 25% em comparação com o preço pago conforme o uso. O preço de reserva de capacidade habilita você a comprar uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa de Pagamento Conforme o Uso. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços de reserva de capacidade.

Alguns clientes terão acesso aos [tipos de preço log Analytics herdados](logs/manage-cost-storage.md#legacy-pricing-tiers) e ao [tipo de preço de Application insights empresarial herdado](app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Entendendo seus custos de Azure Monitor

Há duas fases para entender os custos. A primeira é ao considerar Azure Monitor como sua solução de monitoramento. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Estimativa dos custos para gerenciar seu ambiente

Se você ainda não estiver usando logs de Azure Monitor, poderá usar a [calculadora de preços de Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso de Azure monitor. Para começar, insira "Azure Monitor" na caixa de Pesquisa e clique no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione uma das opções na lista suspensa Tipo:

- Consultas e alertas de métricas  
- Log Analytics
- Application Insights

Em cada um deles, a calculadora de preços o ajudará a estimar os custos prováveis com base na utilização esperada.

Por exemplo, com Log Analytics você pode inserir o número de VMs e os GB de dados que espera coletar de cada VM. Normalmente, 1 GB a 3 GB de dados de mês é ingerido de uma VM do Azure típica. Caso já esteja avaliando os logs do Azure Monitor, você poderá usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitoradas](logs/manage-cost-storage.md#understanding-nodes-sending-data) e o [volume de dados que seu workspace está ingerindo](logs/manage-cost-storage.md#understanding-ingested-data-volume).

Da mesma forma, para Application Insights, se você habilitar a funcionalidade "estimar volume de dados com base na atividade do aplicativo", poderá fornecer entradas sobre seu aplicativo (solicitações por mês e exibições de página por mês, caso você colete a telemetria do lado do cliente) e, em seguida, a calculadora informará a quantidade mediana e 90 º percentil de dados coletados por aplicativos similares Esses aplicativos abrangem o intervalo de configuração do Application Insights (por exemplo, alguns têm amostragem padrão, alguns não têm amostragem etc.), portanto, você ainda tem o controle para reduzir o volume de dados que está ingerindo muito abaixo do nível mediano usando a amostragem. Mas esse é um ponto de partida para entender o que outros clientes semelhantes estão vendo. [Saiba mais](app/pricing.md#estimating-the-costs-to-manage-your-application) sobre estimativa de custos para Application insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Compreendendo o uso e os custos estimados

É importante entender e controlar seu uso uma vez usando Azure Monitor, e há um conjunto avançado de ferramentas para facilitar isso. 

O Azure fornece um grande controle da funcionalidade útil no hub [Gerenciamento de Custos do Azure + Cobrança](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json). Depois de abrir o **Gerenciamento de custos do Azure +** Hub de cobrança, clique em **Gerenciamento de custos** e selecione o [escopo](../cost-management-billing/costs/understand-work-scopes.md) (o conjunto de recursos a serem investigados). 

Em seguida, para ver os custos de Azure Monitor dos últimos 30 dias, clique no bloco **custos diários** , escolha "últimos 30 dias" em datas relativas e adicione um filtro que selecione os nomes de serviço:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight e Análise

Isso resulta em uma exibição como:

![Captura de tela de gerenciamento de custos do Azure](./media/usage-estimated-costs/010.png)

A partir daqui, você pode analisar esse Resumo de custo acumulado para obter os detalhes mais detalhados na exibição "custo por recurso". Nos tipos de preços atuais, os dados de log do Azure são cobrados no mesmo conjunto de medidores se eles se originam de Log Analytics ou Application Insights. Para separar os custos de seu Log Analytics ou Application Insights uso, você pode adicionar um filtro no **tipo de recurso**. Para ver todos os custos de Application Insights, filtre o tipo de recurso para "Microsoft. insights/Components" e, para obter Log Analytics custos, filtre tipo de recurso para "Microsoft. operationalinsights/workspaces". 

Mais detalhes do seu uso estão disponíveis [baixando seu uso do portal do Azure](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso diário por recurso do Azure. Nessa planilha do Excel, o uso de seus recursos do Application Insights pode ser encontrado ao filtrar primeiro a coluna "Categoria do Medidor" para mostrar "Application Insights" e "Log Analytics" e, em seguida, adicionar um filtro na coluna "ID da Instância", que é "contains microsoft.insights/components".  A maior parte do uso do Application Insights é relatada em medidores com a Categoria de Medidor do Log Analytics, já que há um único back-end de logs para todos os componentes do Azure Monitor.  Somente recursos do Application Insights em tipos de preço herdados e testes na Web de várias etapas são relatados com uma Categoria de Medidor do Application Insights.  O uso é mostrado na coluna "Quantidade Consumida", e a unidade de cada entrada é mostrada na coluna "Unidade de Medida".  Há mais detalhes disponíveis para ajudar você a [entender sua fatura do Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> Usar o **Gerenciamento de custos** no gerenciamento de custos **do Azure +** Hub de cobrança é a abordagem preferida para compreender amplamente os custos de monitoramento.  As experiências de **uso e custos estimadas** para [log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs)  e [Application insights](app/pricing.md#understand-your-usage-and-estimate-costs) fornecem informações mais aprofundadas para cada uma dessas partes do Azure monitor.

Outra opção para exibir o uso de Azure Monitor é a página **uso e custos estimados** no Hub do monitor. Isso mostra o uso dos principais recursos de monitoramento, como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics do Azure](https://azure.microsoft.com/pricing/details/log-analytics/)e [insights de aplicativo Azure](https://azure.microsoft.com/pricing/details/application-insights/). Para clientes nos planos de preços disponíveis antes de abril de 2018, isso também inclui o uso do Log Analytics adquirido por meio das ofertas do Insights e do Analytics.

Nesta página, os usuários podem exibir o uso de recursos para os últimos 31 dias, agregados por assinatura. `Drill-ins` mostrar tendências de uso no período de 31 dias. São necessários vários dados para se juntar e fazer essa estimativa, portanto, seja paciente enquanto a página é carregada.

Este exemplo mostra o uso de monitoramento e uma estimativa dos custos resultantes:

![Captura de tela do portal do uso e custos estimados](./media/usage-estimated-costs/001.png)

Selecione o link na coluna de uso mensal para abrir um gráfico que mostra as tendências de uso nos últimos 31 dias: 

![Incluído por captura de tela do gráfico de barras do nó](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Direitos de assinatura do Operations Management Suite

Clientes que adquiriram o Microsoft Operations Management Suite E1 e E2 estão qualificados para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](app/pricing.md). Para receber esses direitos para workspaces Log Analytics ou recursos Application Insights em uma determinada assinatura: 

- Workspaces do Log Analytics devem usar o tipo de preço "por nó (OMS)".
- Application Insights recursos devem usar o tipo de preço "Enterprise".

Dependendo do número de nós do conjunto que sua organização adquiriu, mover algumas assinaturas para um tipo de preço pago conforme o uso (por GB) pode ser vantajoso, mas isso requer uma consideração cuidadosa.

> [!WARNING]
> Se a sua organização tem o atual Microsoft Operations Management Suite E1 e E2, geralmente é melhor manter seus espaços de trabalho de Log Analytics no tipo de preço "por nó (OMS)" e seus recursos de Application Insights no tipo de preço "Enterprise". 
>

