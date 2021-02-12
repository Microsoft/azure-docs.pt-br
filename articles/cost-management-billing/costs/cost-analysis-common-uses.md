---
title: Usos comuns da análise de custo no Gerenciamento de Custos do Azure
description: Este artigo explica como você pode obter resultados para tarefas comuns de análise de custo no Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600033"
---
# <a name="common-cost-analysis-uses"></a>Usos comuns da análise de custo

Os usuários do Gerenciamento de Custos do Azure muitas vezes desejam respostas a perguntas que muitas outras pessoas também fazem. Este artigo explica como obter resultados para tarefas comuns de análise de custo no Gerenciamento de Custos.

## <a name="view-forecasted-costs"></a>Ver os custos previstos

Os custos previstos são mostrados nas áreas de análise de custo para exibições de áreas e de colunas empilhadas. A previsão é baseada no seu uso histórico de recursos. As alterações no uso de recursos afetam os custos previstos.

No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.

Na exibição padrão, o gráfico superior tem as seções Custo real/amortizado e custo previsto. A cor sólida do gráfico mostra o Custo real/amortizado. A cor sombreada mostra o custo de previsão.

[![Custo previsto](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Ver os custos previstos agrupados por serviço

A exibição padrão não mostra o grupo de custos previstos por um serviço e, portanto, você precisará adicionar uma seleção agrupar por.

No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.

Selecione **Agrupar por** > **Nome do serviço**.

A exibição mostra os custos agrupados para cada serviço. O custo previsto não é calculado para cada serviço. Ele é projetado para o **Total** de todos os seus serviços.

[![Custo previsto agrupado](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Ver os custos previstos para um serviço

Você pode ver os custos previstos restritos a um só serviço. Por exemplo, talvez você queira ver os custos previstos apenas para máquinas virtuais.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Adicionar filtro** e **Nome do serviço**.
1. Na lista **Escolher**, selecione um serviço. Por exemplo, escolha **Máquinas virtuais**.

Examine o custo real da seleção e o custo previsto.

Adicione mais personalizações à exibição.

1. Adicione um segundo filtro para **Medidor** e escolha um valor a ser filtrado para um tipo individual de medidor no nome do serviço selecionado.
1. Agrupe-o por **Recurso** para ver os recursos específicos que estão gerando custos. O custo previsto não é calculado para cada serviço. Ele é projetado para o **Total** de todos os seus recursos.

[![Custo previsto para um serviço](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Ver os seus custos do Azure e do AWS juntos  

Para ver os custos do Azure e do AWS juntos, use escopos do grupo de gerenciamento no Azure.

1. Crie um grupo de gerenciamento ou escolha um existente.
1. Atribua as assinaturas existentes do Azure necessárias ao grupo de gerenciamento.
1. Atribua o *mesmo* grupo de gerenciamento à conta vinculada do conector.
1. Acesse a análise de custo e escolha **Custos acumulados**.
1. Selecione **Agrupar por** - **Provedor**.

## <a name="view-cost-breakdown-by-azure-service"></a>Exibir detalhamento de custos por serviço do Azure

A exibição de custos por um serviço do Azure pode ajudá-lo a entender melhor as partes de sua infraestrutura que custam mais. Por exemplo, os custos de computação de VMs podem ser pequenos. Ainda assim, você pode acumular custos de rede significativos devido à quantidade de informações emitidas por meio das VMs. Entender os principais elementos geradores de custo de seus serviços do Azure é essencial para que você possa ajustar, conforme necessário, a utilização do serviço.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Custo por serviço** e, em seguida, agrupe por **Camada de serviço**.
1. Altere a exibição para **Tabela**.

[![Detalhamento de custos por serviço do Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Examinar encargos faturados na Análise de custo

Para ver os detalhes de sua fatura no portal do Azure, navegue até a Análise de custo do escopo associado à fatura que você está analisado. Selecione a exibição **Detalhes da fatura**. Os detalhes da fatura mostram os encargos como foi visto na fatura.

[![Exemplo mostrando a seleção de detalhes da fatura](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Vendo os detalhes da fatura, é possível identificar o serviço que tem custos inesperados e determinar quais recursos estão diretamente associados ao recurso na Análise de custo. Por exemplo, se desejar analisar encargos do serviço de Máquinas Virtuais, navegue até a exibição **Custo acumulado**. Em seguida, defina a granularidade como **Diária** e filtre os encargos **Nome do serviço: máquinas virtuais** e agrupe-os por **Recurso**.

[![Exemplo mostrando os custos acumulados para máquinas virtuais](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Exibir detalhamento de custos por recurso do Azure

Seus serviços são criados com recursos do Azure. Examinar os custos com base nos recursos pode ajudá-lo a identificar rapidamente os elementos que mais contribuem para seus custos. Se um serviço tiver recursos muito caros, considere fazer alterações para reduzir os custos.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Custo por recurso**.
1. Altere a exibição para **Tabela**.

[![Exibir detalhamento de custos por recurso do Azure](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Exibir detalhamento de custos pelas dimensões selecionadas

As dimensões permitem que você organize seus custos com base em vários valores de metadados mostrados em seus encargos. Por exemplo, você pode agrupar seus custos por localização.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione o filtro **Agrupar por**.  
    [![Selecionar um item Agrupar por](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Opcionalmente, você salva a exibição para uso posterior.
1. Clique em um gráfico de pizza abaixo do gráfico para exibir dados mais detalhados.  
    [![Exibir detalhamento de custos por dimensões selecionadas](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Exibir custos por dia ou por mês

A análise dos custos diários e mensais pode ajudar você a entender melhor se há uma hora da semana ou do ano em que os custos são maiores. Se você tiver mais tráfego de clientes em um período de Natal, isso levará a um aumento correspondente nos custos do Azure? A sexta-feira é um dia mais dispendioso do que a segunda?

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Defina a **Granularidade** como **Mensal** ou **Diária**.

[![Exibir os custos por dia](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Exibir os encargos de VM spot

As VMs spot podem fornecer grandes economias de custos para cargas de trabalho que podem lidar com interrupções. As cargas de trabalho são executadas na capacidade do Azure não utilizada. Como elas podem ser removidas a qualquer momento, as VMs spot recebem um desconto significativo. Use as etapas a seguir para exibir seus encargos de VM spot.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo, **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
2. Adicione um filtro para **Modelo de Preços: spot**.

![Exemplo mostrando filtro de VM Spot](./media/cost-analysis-common-uses/spot-vm-filter.png)

A dimensão Modelo de Preços também é usada para exibir encargos sob demanda e de reserva.

## <a name="view-your-reservation-charges"></a>Exibir seus encargos de reserva

As instâncias reservadas fornecem uma forma de economizar dinheiro com o Azure. Com as reservas, você gasta dinheiro com antecedência para um determinado número de recursos ao longo do tempo. A análise de custo mostra os encargos como eles aparecem na sua fatura. Os encargos são mostrados como custos reais ou amortizados no decorrer do período de reserva.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo, **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Adicione um filtro para **Modelo de Preços: Reserva**.
1. Em **Escopo** e ao lado do custo mostrado, clique no símbolo de seta para baixo, selecione a métrica **Custo real** ou **Custo amortizado**.

![Selecionar uma métrica de custo](./media/cost-analysis-common-uses/metric-cost.png)

Cada métrica afeta o modo como os dados são mostrados para seus encargos de reserva.

**Custo real** – mostra a compra como ela aparece em sua fatura. Por exemplo, se você tiver comprado uma reserva de um ano de US$ 1200 em janeiro, a análise de custo mostrará um custo de US$ 1200 no mês de janeiro para a reserva. Ela não mostra um custo de reserva para os outros meses do ano. Se você agrupar os custos reais por VM, então a VM que tiver recebido o benefício de reserva para um determinado período terá custo zero para o mês.

**Custo amortizado** – mostra uma divisão de compra de reserva como um custo amortizado durante o termo da reserva. Usando o mesmo exemplo acima, a análise de custo mostrará um custo de US$ 100 para cada mês ao longo do ano, se você tiver comprado uma reserva de um ano de US$ 1200 em janeiro. Se você agrupar os custos por VM neste exemplo, você verá o custo atribuído a cada VM que recebeu o benefício de reserva.

## <a name="view-your-reservation-utilization"></a>Exibir sua utilização da reserva

Após comprar uma reserva, é importante acompanhar a utilização dela para que você tenha o que pagou. Por exemplo, se você comprou dez VMs para um ano e usa apenas cinco delas, então basicamente metade da compra foi desperdiçada. Há duas maneiras diferentes de avaliar sua utilização:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Exibir os custos de RI não utilizados na análise de custo

Para identificar a quantidade de custos que está sendo desperdiçada por mês para sua compra de reserva, siga as etapas abaixo.

1. No portal do Azure, navegue até análise de custo para o escopo em que sua reserva é aplicada. Por exemplo, **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Adicione um filtro para **Modelo de Preços: Reserva**.
1. Selecione a exibição **Custo Amortizado**.
1. Defina a granularidade como **Mensal**.
1. Defina o período como o ano atual ou o termo da reserva.
1. Defina o tipo de gráfico como **Coluna (empilhado)** .
1. Agrupar encargos por **Tipo de Encargo**.
1. Examine os resultados para valores `unusedreservation`.

[![Exemplo mostrando o uso de reserva](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Exibir a utilização em Reservas

Para obter instruções detalhadas, confira [Otimizar o uso de reserva](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Exibir os custos de uma marca específica

Muitos usuários do Azure aplicam marcas a seus recursos, como um centro de custo ou um ambiente de desenvolvimento (produção e teste) para categorizar melhor os encargos. As marcas aparecem como uma dimensão na análise de custo. Você pode usar a dimensão para obter informações sobre suas categorizações de marcação personalizadas.

O suporte para marcas aplica-se ao uso relatado *depois* da aplicação da marca ao recurso. As marcas não são aplicadas retroativamente para rollups de custo.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Agrupar por** para sua marca.

[![Exibir os custos de uma tag específica](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Faça o download dos seus detalhes de uso

O arquivo de relatório de detalhes de uso, no formato CSV, fornece um detalhamento de todos os encargos acumulados para uma fatura. Você pode usar o relatório para compará-los àqueles em sua fatura e, assim, entendê-los melhor. Cada encargo cobrado em sua fatura corresponde aos encargos detalhados no relatório de uso.

1. Na portal do Azure, navegue até a guia **Uso e Encargos** para uma conta de cobrança ou assinatura para cobrança. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Cobrança** > **Uso + encargos**.
1. Selecione o item de linha do qual baixar e clique no símbolo de download.  
    [![Baixar uso e cobranças](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Selecione o arquivo de uso para baixar.  
    ![Escolher um arquivo de uso para baixar](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Exibir o detalhamento de custos do EA

Seu registro no EA acumula custos para toda a sua organização. A compreensão de como os custos são acumulados e faturados ao longo do tempo ajuda a envolver os stakeholders apropriados para garantir que os custos sejam gerenciados com responsabilidade.

Os custos são mostrados apenas para seu registro ativo. Se você tiver transferido um registro (inativo) para um novo (ativo), os custos do registro anterior não serão mostrados no Gerenciamento de Custos.


1. No portal do Azure, navegue até **Gerenciamento de Custos + Cobrança** > **Visão geral**.
1. Clique em **Detalhamento** para ver o mês atual e o seu Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário) burndown.  
    [![Visão geral dos custos de EA – resumo do detalhamento](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Clique na guia **Uso e Encargos** e veja o detalhamento do mês anterior no período de tempo escolhido.  
    [![Guia Uso e encargos](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Exibir o custo mensal do registro por período

Use uma exibição gráfica dos custos mensais do seu registro para entender as tendências de custo e os valores faturados para um período determinado.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione seu registro e defina o prazo do registro.
1. Defina a granularidade como mensal e, em seguida, defina a exibição como **Coluna (empilhada)** .

Você pode agrupar e filtrar seus dados para uma análise mais detalhada.

[![Custo de registro mensal por período](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Exibir custos acumulados de registro em EA

Exiba os encargos líquidos acumulados ao longo do tempo para entender as despesas gerais da sua organização em um período determinado.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione seu registro e exiba os custos acumulados atuais.

[![Custos acumulados de registro](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Próximas etapas
- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
- Leia a [Documentação do Gerenciamento de Custos](../index.yml).
