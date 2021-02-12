---
title: Painel Resumo para análise da Central de Parceiros no Marketplace Comercial
description: Saiba como acessar gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace no painel Resumo na Central de Parceiros.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462989"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Painel de resumo na análise do marketplace comercial

Este artigo fornece informações sobre o painel Resumo na Central de Parceiros. Esse painel exibe gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para suas ofertas.

Para acessar o painel de resumo no Partner Center, em **Marketplace comercial** , selecione **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Resumo**.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [terminologia de análise do Marketplace comercial e perguntas comuns](./analytics-faq.md).

## <a name="summary-dashboard"></a>Painel de resumo

O painel de resumo apresenta uma visão geral do Azure Marketplace e o Microsoft AppSource oferece desempenho comercial. O painel fornece uma ampla visão geral do seguinte:

- Pedidos dos clientes
- Clientes
- Uso dos clientes das ofertas
- Visitas à página dos clientes no Azure Marketplace e no AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel Resumo

As seções a seguir descrevem como usar o painel de resumo e como ler os dados.

### <a name="month-range"></a>Intervalo de meses

Você pode encontrar uma seleção de intervalo de mês no canto superior direito de cada página. Personalize a saída dos gráficos de página de **Resumo** selecionando um intervalo de mês com base nos últimos 3, 6 ou 12 meses ou selecionando um intervalo de mês personalizado com uma duração máxima de 12 meses. O intervalo de mês padrão (período de computação) é de seis meses.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Ilustra as opções de intervalo mensal no painel de resumo.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de computação selecionado pelo usuário.

### <a name="orders-widget"></a>Widget de pedidos

O widget pedidos no painel Resumo * * exibe os pedidos atuais para todas as ofertas baseadas em Transact. O widget pedidos exibe uma contagem e uma tendência de todos os pedidos comprados (excluindo pedidos cancelados) para o período de computação selecionado. Os **pedidos** de valor percentual representam a quantidade de crescimento durante o período de computação selecionado.

[![Ilustra o widget Orders no painel de resumo.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Você também pode ir para o relatório pedidos selecionando o link **painel pedidos** no canto inferior esquerdo do widget.

### <a name="customers-widget"></a>Widget de clientes

O widget **clientes** do painel **Resumo** exibe o número total de clientes que adquiriram suas ofertas para o período de computação selecionado. O widget clientes exibe uma contagem e uma tendência do número total de clientes ativos (incluindo novos e existentes) (excluindo clientes com rotatividade) para o período de computação selecionado. O valor percentual em **clientes** representa a quantidade de crescimento durante o período de computação selecionado.

[![Ilustra o widget clientes no painel de resumo.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Você também pode acessar o relatório detalhado de clientes selecionando o link do **painel clientes** no canto inferior esquerdo do widget.

### <a name="usage-widget"></a>Widget de uso

O widget **uso** do painel **Resumo** representa o total de horas de uso normalizado e bruto para todas as ofertas de VM (máquina virtual) do Azure. O widget uso exibe uma contagem e tendência do total de horas de uso para o período de computação selecionado.

A tabela de resumo de uso exibe as horas de uso do cliente para todas as ofertas adquiridas.

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- Horas de uso brutos são definidas como a quantidade de tempo que as VMs estão em execução em termos de horas.

O valor percentual abaixo do total de horas de uso representa a quantidade de crescimento em horas de uso durante o período de computação selecionado.

[![Ilustra o widget de uso no painel de resumo.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Você também pode ir para o relatório de uso selecionando o link **painel de uso** no canto inferior esquerdo do widget.

### <a name="marketplace-insights"></a>Informações do Marketplace

O Marketplace insights mostra o número de usuários que visitaram suas páginas de ofertas no Azure Marketplace e AppSource. A **contagem de visitas à página** mostra um resumo do Web Analytics do Marketplace comercial que permite que os editores meçam o envolvimento do cliente para suas respectivas páginas de detalhes do produto listadas nas lojas online do Marketplace comercial: Microsoft AppSource e Azure Marketplace. Esse widget exibe uma contagem e uma tendência do total de visitas à página durante o período de computação selecionado.

[![Ilustra o widget contagem de visitas à página no painel Resumo.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Você também pode acessar o relatório do Marketplace insights selecionando o link **painel do Marketplace insights** no canto inferior esquerdo do widget.

### <a name="geographical-spread"></a>Dispersão geográfica

Para o período de computação selecionado, o calor exibe o número total de clientes, pedidos e horas de uso normalizadas em relação à dimensão de Geografia.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Ilustra o widget de distribuição de países no painel de resumo.":::

Observe o seguinte:

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O calor tem uma grade suplementar para exibir os detalhes de contagem de clientes, contagem de pedidos e horas de uso normalizadas para o local específico.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando o botão **página inicial** no mapa.

> [!TIP]
> Você pode usar o ícone de download no canto superior direito de qualquer widget para baixar os dados. Você pode fornecer comentários sobre cada um dos widgets selecionando o ícone "polegar para cima" ou "polegar para baixo".

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial, consulte [acessar relatórios analíticos para o Marketplace comercial no Partner Center](./partner-center-portal/analytics.md).
- Para obter informações sobre os pedidos em um formato gráfico para download, confira [Painel de pedidos na análise do marketplace comercial](orders-dashboard.md).
- Para as métricas de cobrança de uso e de medição de VM (máquina virtual), confira [Painel de Uso em análises de marketplace comercial](usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./partner-center-portal/downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e AppSource, consulte [classificações & análises painel de análise no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [terminologia e perguntas comuns sobre a análise do Marketplace comercial](./analytics-faq.md).