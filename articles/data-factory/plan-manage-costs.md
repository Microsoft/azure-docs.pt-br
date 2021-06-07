---
title: Planejar e gerenciar custos para o Azure Data Factory
description: Este artigo descreve como você pode planejar e gerenciar custos para o Azure Data Factory
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372885"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planejar e gerenciar custos para o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Data Factory é um serviço de integração de dados elástico e sem servidor criado para escala de nuvem.  Isso significa que não há uma computação de tamanho fixo que você precise planejar para a carga de pico; em vez disso, você especifica a quantidade de recursos a serem alocados sob demanda por operação, o que permite que você projete os processos de ETL de maneira muito mais escalonável. Além disso, o ADF é cobrado em um plano baseado em consumo, o que significa que você paga apenas pelo que usa.

Este artigo descreve como você pode planejar e gerenciar custos para o Azure Data Factory.

*   Primeiro, no início do projeto de ETL, você realiza prova de conceito e usa uma combinação de consumo por pipeline e calculadora de preços para estimar os custos.
*   Depois de implantar seus pipelines para produção, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar tendências de gastos.
*   Além disso, você pode exibir o consumo por pipeline e as informações de consumo por atividade para entender quais pipelines e quais atividades são mais onerosas, além de identificar candidatos para redução de custos.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Estimar custos usando a calculadora de preços e consumo por execução de atividade e por pipeline

Você pode usar a [calculadora de preços do ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) para obter uma estimativa do custo da execução da carga de trabalho do ETL no Azure Data Factory.  Para usar a calculadora, você precisa inserir detalhes como o número de execuções de atividade, o número de horas de unidade de integração de dados, o tipo de computação usado para Fluxo de Dados, a contagem de núcleos, a contagem de instâncias, a duração da execução e assim por diante.

Uma das perguntas mais frequentes para a calculadora de preços é sobre quais valores devem ser usados como entradas.  Durante a fase de prova de conceito, você pode realizar execuções de avaliação usando conjuntos de dados de exemplo para entender o consumo de vários medidores do ADF.  Em seguida, com base no consumo do conjunto de dados de exemplo, você pode projetar o consumo para o conjunto de dados completo o agendamento de operacionalização.

> [!NOTE]
> Os preços usados nestes exemplos abaixo são hipotéticos e não têm o objetivo de sugerir o preço real.

Por exemplo, digamos que você precise mover 1 TB de dados diariamente do AWS S3 para o Azure Data Lake Gen2.  Você pode executar a POC de mover 100 GB de dados para medir a taxa de transferência de ingestão de dados e entender o consumo de cobrança correspondente.

Aqui estão os detalhes de execução de uma atividade de cópia de exemplo (seu nível de uso real variará com base na forma de seu conjunto de dados específico, bem como das velocidades de rede, limites de saída na conta S3, limites de entrada em ADLS Gen2 e outros fatores).

![Execução de cópia S3](media/plan-manage-costs/s3-copy-run-details.png)

Aproveitando o [monitoramento de consumo no nível de execução de pipeline](#monitor-consumption-at-pipeline-run-level), você pode ver as quantidades correspondentes de consumo de medidor de movimentação de dados:

![Consumo do pipeline de cópia S3](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Portanto, o número total de horas de DIU necessárias para mover 1 TB por dia para o mês inteiro é:

1,2667 (horas de DIU) * (1 TB/100 GB) * 30 (dias em um mês) = 380 horas de DIU

Agora você pode conectar 30 execuções de atividade e 380 horas de DIU na calculadora de preços do ADF para obter uma estimativa da sua fatura mensal:

![Calculadora de preços de cópia S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Usar orçamentos e alertas de custo

É possível criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso.  Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo.  Ao criar um orçamento, você pode fazê-lo no nível da assinatura ou em uma granularidade mais baixa adicionando outros filtros, como ID do recurso e nome do medidor.  Mas você não pode criar orçamentos para pipelines individuais em um alocador.

## <a name="monitor-costs-at-factory-level"></a>Monitorar custos no nível de alocador

Ao começar a usar Azure Data Factory, você pode ver os custos incorridos no painel de [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md) no portal do Azure.

1. Para exibir a [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md), abra a janela **Gerenciamento de Custos + Cobrança**, selecione **Gerenciamento de custos** no menu e, em seguida, selecione **Análise de custo aberto**.
2. A exibição padrão mostra os custos acumulados para o mês atual.  Você pode alternar para um intervalo de tempo diferente e uma granularidade diferente, como diária ou mensal.
3. Para restringir os custos para um único serviço, como o Azure Data Factory, selecione **Adicionar filtro** e, em seguida, selecione **Nome do serviço**.  Em seguida, escolha **Azure Data Factory v2** na lista.
4. Você pode adicionar outros filtros para analisar o custo de uma instância de alocador específica e a granularidade específica do medidor do ADF.

   ![Análise de custo](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorar o consumo no nível de execução de pipeline

Dependendo dos tipos de atividades que você tem em seu pipeline, do volume de dados que você está movendo e transformando e da complexidade da transformação, a execução de um pipeline acionará medidores de cobrança diferentes no Azure Data Factory.

Você pode exibir a quantidade de consumo de diferentes medidores para execuções de pipeline individuais na experiência do usuário do Azure Data Factory. Para abrir a experiência de monitoramento, selecione o bloco **Monitorar e Gerenciar** na folha de data factory do [portal do Azure](https://portal.azure.com/). Se você já estiver na experiência do usuário do ADF, clique no ícone **Monitorar** na barra lateral esquerda. O modo de exibição de monitoramento padrão é a lista de execuções de pipeline.

Clicar no botão **Consumo** ao lado do nome do pipeline exibirá uma janela pop-up mostrando a você o consumo da execução do pipeline agregado em todas as atividades nesse pipeline.

![Consumo de execução do pipeline](media/plan-manage-costs/pipeline-run-consumption.png)

![Detalhes de consumo do pipeline](media/plan-manage-costs/pipeline-consumption-details.png)

O modo de exibição consumo de execução do pipeline mostra o valor consumido para cada medidor do ADF para a execução do pipeline específica, mas não mostra o preço real cobrado, pois o valor cobrado depende do tipo de conta do Azure que você tem e do tipo de moeda usada.  Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitorar o consumo no nível de execução da atividade
Depois de entender o consumo agregado no nível de execução do pipeline, há cenários em que você precisa fazer uma busca detalhada e identificar qual é a atividade mais onerosa no pipeline.

Para ver o consumo no nível de execução da atividade, vá para a interface do usuário **Criar e Monitorar** do data factory. Na guia **Monitorar**, em que você vê uma lista de execuções de pipeline, clique no link **Nome do pipeline** para acessar a lista de execuções de atividade na execução do pipeline.  Clique no botão **Saída** ao lado do nome da atividade e procure a propriedade **billableDuration** na saída JSON:

Aqui está um exemplo de uma execução de atividade de cópia:

![Saída da cópia](media/plan-manage-costs/copy-output.png)

E aqui está um exemplo de uma execução de atividade de Fluxo de Dados de Mapeamento:

![Saída de fluxo de dados](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir para saber mais sobre como os preços funcionam no Azure Data Factory:

- [Página de preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Noções básicas do Azure Data Factory por meio de exemplos](./pricing-concepts.md)
- [Calculadora de preços do Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory)