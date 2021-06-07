---
title: Fluxos de dados
description: Uma visão geral dos fluxos de dados no Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592589"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Fluxos de dados no Azure Synapse Analytics

## <a name="what-are-data-flows"></a>O que são fluxos de dados?

Os fluxos de dados são transformações de dados visualmente projetadas no Azure Synapse Analytics. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades nos pipelines do Azure Synapse Analytics que usam clusters de Apache Spark escalados horizontalmente. As atividades de fluxo de dados podem ser operadas usando recursos existentes de agendamento, controle, fluxo e monitoramento do Azure Synapse Analytics.

Os fluxos de dados fornecem uma experiência totalmente visual sem necessidade de codificação. Seus fluxos de dados são executados em clusters de execução gerenciados por Synapse para processamento de dados expandido. O Azure Synapse Analytics lida com toda a tradução de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Os fluxos de dados são criados no painel desenvolver no Synapse Studio. Para criar um fluxo de dados, selecione o sinal de adição ao lado de **desenvolver** e, em seguida, selecione **fluxo de dados**. 

![Novo fluxo de dados](media/data-flow/new-data-flow.png)

Essa ação leva você para a tela de fluxo de dados, na qual você pode criar a lógica de transformação. Selecione **Adicionar origem** para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Source Transformation](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Criação de fluxos de dados

O fluxo de dados tem uma tela de criação exclusiva projetada para facilitar a criação da lógica de transformação. A tela de fluxo de dados é separada em três partes: a barra superior, o grafo e o painel de configuração. 

![A captura de tela mostra as telas de fluxo de dados com barra superior, grafo e painel de configuração rotulados.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Grafo

O grafo exibe o fluxo de transformação. Ele mostra a linhagem dos dados de origem conforme eles fluem em um ou mais coletores. Para adicionar uma nova origem, selecione **Adicionar origem**. Para adicionar uma nova transformação, selecione o sinal de adição no canto inferior direito de uma transformação existente. Saiba mais sobre como [gerenciar o grafo de fluxo de dados](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Captura de tela mostra a parte gráfica do Canvas com uma caixa de texto de pesquisa.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas para a transformação selecionada no momento. Se nenhuma transformação for selecionada, ela mostrará o fluxo de dados. Na configuração geral do fluxo de dados, você pode adicionar parâmetros por meio da guia **parâmetros** . Para obter mais informações, consulte [parâmetros de fluxo de dados](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Cada transformação contém pelo menos quatro guias de configuração.

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia em cada painel de configuração de transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação da transformação.

![Guia Configurações de origem](media/data-flow/source-1.png)

#### <a name="optimize"></a>Otimizar

A guia **otimizar** contém configurações para configurar esquemas de particionamento. Para saber mais sobre como otimizar seus fluxos de dados, consulte o [Guia de desempenho do fluxo de dados de mapeamento](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Captura de tela mostra a guia otimizar](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecionar

A guia **inspecionar** fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem das colunas e as referências de coluna. **Inspecionar** é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver os metadados no painel **inspecionar** .

![Guia inspecionar](media/data-flow/inspect.png)

Ao alterar a forma de seus dados por meio de transformações, você verá o fluxo de alterações de metadados no painel **inspecionar** . Se não houver um esquema definido em sua transformação de origem, os metadados não estarão visíveis no painel **inspecionar** . A falta de metadados é comum em cenários de descompasso de esquema.

#### <a name="data-preview"></a>Visualização dos dados

Se o modo de depuração estiver ativado, a guia **visualização de dados** fornecerá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [visualização de dados em modo de depuração](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como as configurações de validação e de depuração. Você também pode exibir o código JSON subjacente e o script de fluxo de dados da sua lógica de transformação.

## <a name="available-transformations"></a>Transformações disponíveis

Exiba a [visão geral de transformação fluxo de dados de mapeamento](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter uma lista de transformações disponíveis.

## <a name="data-flow-activity"></a>Atividade de fluxo de dados

Os fluxos de dados são operados nos pipelines do Azure Synapse Analytics usando a [atividade de fluxo de dados](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Tudo o que um usuário precisa fazer é especificar qual Integration Runtime usar e passar valores de parâmetro. Para obter mais informações, saiba mais sobre o [tempo de execução de integração do Azure](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Modo de depuração

O modo de depuração permite ver interativamente os resultados de cada etapa de transformação enquanto você cria e depura seus fluxos de dados. A sessão de depuração pode ser usada no ao criar sua lógica de fluxo de dados e executar a depuração de pipeline executada com atividades de fluxo de dados. Para saber mais, confira a [documentação do modo de depuração](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitorando fluxos de dados

O fluxo de dados integra-se aos recursos de monitoramento existentes do Azure Synapse Analytics. Para saber como entender a saída de monitoramento do fluxo de dados, consulte [monitoramento fluxos de dados de mapeamento](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

A equipe do Azure Synapse Analytics criou um [Guia de ajuste de desempenho](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para ajudá-lo a otimizar o tempo de execução de seus fluxos de dados depois de criar sua lógica de negócios.

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma [transformação de origem](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Saiba como criar fluxos de dados no [modo de depuração](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
