---
title: Fluxos de dados de mapeamento
description: Uma visão geral do mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: 1529d2d546227880fee71d1823482e040d2e57c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564313"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapeando fluxos de dados em Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>O que são os fluxos de dados de mapeamento?

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines que usam clusters de Apache Spark expandidos. As atividades de fluxo de dados podem ser operadas usando recursos existentes de agendamento, controle, fluxo e monitoramento de Azure Data Factory.

O mapeamento de fluxos de dados fornece uma experiência totalmente visual sem a necessidade de codificação. Seus fluxos de dados são executados em clusters de execução gerenciados por ADF para processamento de dados escalados horizontalmente. Azure Data Factory manipula toda a conversão de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Os fluxos de dados são criados no painel recursos de fábrica como pipelines e conjuntos de dados. Para criar um fluxo de dados, selecione o sinal de adição ao lado de **recursos de fábrica** e, em seguida, selecione **fluxo de dados**. 

![Novo fluxo de dados](media/data-flow/new-data-flow.png)

Essa ação leva você para a tela de fluxo de dados, na qual você pode criar a lógica de transformação. Selecione **Adicionar origem** para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Source Transformation](data-flow-source.md).

## <a name="authoring-data-flows"></a>Criação de fluxos de dados

O mapeamento de fluxo de dados tem uma tela de criação exclusiva projetada para facilitar a criação da lógica de transformação. A tela de fluxo de dados é separada em três partes: a barra superior, o grafo e o painel de configuração. 

![A captura de tela mostra as telas de fluxo de dados com barra superior, grafo e painel de configuração rotulados.](media/data-flow/canvas-1.png "Tela")

### <a name="graph"></a>Grafo

O grafo exibe o fluxo de transformação. Ele mostra a linhagem dos dados de origem conforme eles fluem em um ou mais coletores. Para adicionar uma nova origem, selecione **Adicionar origem**. Para adicionar uma nova transformação, selecione o sinal de adição no canto inferior direito de uma transformação existente. Saiba mais sobre como [gerenciar o grafo de fluxo de dados](concepts-data-flow-manage-graph.md).

![Captura de tela mostra a parte gráfica do Canvas com uma caixa de texto de pesquisa.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas para a transformação selecionada no momento. Se nenhuma transformação for selecionada, ela mostrará o fluxo de dados. Na configuração geral do fluxo de dados, você pode adicionar parâmetros por meio da guia **parâmetros** . Para obter mais informações, consulte [mapeando parâmetros de fluxo de dados](parameters-data-flow.md).

Cada transformação contém pelo menos quatro guias de configuração.

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia em cada painel de configuração de transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação da transformação.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

#### <a name="optimize"></a>Otimizar

A guia **otimizar** contém configurações para configurar esquemas de particionamento. Para saber mais sobre como otimizar seus fluxos de dados, consulte o [Guia de desempenho do fluxo de dados de mapeamento](concepts-data-flow-performance.md).

![Captura de tela mostra a guia otimizar, que inclui a opção de partição, o tipo de partição e o número de partições.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecionar

A guia **inspecionar** fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem das colunas e as referências de coluna. **Inspecionar** é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver os metadados no painel **inspecionar** .

![Inspecionar](media/data-flow/inspect1.png "Inspecionar")

Ao alterar a forma de seus dados por meio de transformações, você verá o fluxo de alterações de metadados no painel **inspecionar** . Se não houver um esquema definido em sua transformação de origem, os metadados não estarão visíveis no painel **inspecionar** . A falta de metadados é comum em cenários de descompasso de esquema.

#### <a name="data-preview"></a>Visualização dos dados

Se o modo de depuração estiver ativado, a guia **visualização de dados** fornecerá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [visualização de dados em modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como salvar e validar. Você também pode exibir o código JSON subjacente e o script de fluxo de dados da sua lógica de transformação. Para obter mais informações, saiba mais sobre o [script de fluxo de dados](data-flow-script.md).

## <a name="available-transformations"></a>Transformações disponíveis

Exiba a [visão geral de transformação fluxo de dados de mapeamento](data-flow-transformation-overview.md) para obter uma lista de transformações disponíveis.

## <a name="data-flow-activity"></a>Atividade de fluxo de dados

O mapeamento de fluxos de dados é operacional em pipelines do ADF usando a [atividade de fluxo de dados](control-flow-execute-data-flow-activity.md). Tudo o que um usuário precisa fazer é especificar qual Integration Runtime usar e passar valores de parâmetro. Para obter mais informações, saiba mais sobre o [tempo de execução de integração do Azure](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Modo de depuração

O modo de depuração permite ver interativamente os resultados de cada etapa de transformação enquanto você cria e depura seus fluxos de dados. A sessão de depuração pode ser usada no ao criar sua lógica de fluxo de dados e executar a depuração de pipeline executada com atividades de fluxo de dados. Para saber mais, confira a [documentação do modo de depuração](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitorando fluxos de dados

O mapeamento de fluxo de dados integra-se com os recursos existentes de monitoramento de Azure Data Factory. Para saber como entender a saída de monitoramento do fluxo de dados, consulte [monitoramento fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

A equipe de Azure Data Factory criou um [Guia de ajuste de desempenho](concepts-data-flow-performance.md) para ajudá-lo a otimizar o tempo de execução de seus fluxos de dados depois de criar sua lógica de negócios.

## <a name="available-regions"></a>Regiões disponíveis

= = = = = = = Os fluxos de dados de mapeamento estão disponíveis nas seguintes regiões no ADF:

| Região do Azure | Fluxos de dados no ADF |
| ------------ | ----------------- |
|  Austrália Central | |
| Austrália Central 2 | |
| Leste da Austrália | ✓ |
| Australia Southeast   | ✓ |
| Brazil South  | ✓ |
| Canadá Central | ✓ |
| Índia Central | ✓ |
| Centro dos EUA    | ✓ |
| Leste da China |      |
| Leste da China 2  |   |
| China não regional | |
| Norte da China |     |
| Norte da China 2 | |
| Leste da Ásia | ✓ |
| Leste dos EUA   | ✓ |
| Leste dos EUA 2 | ✓ |
| França Central | ✓ |
| Sul da França  | |
| Centro da Alemanha (soberanas) | |
| Alemanha Não Regional (soberanas) | |
| Norte da Alemanha (público) | |
| Nordeste da Alemanha (soberanas) | |
| Centro-oeste da Alemanha (público) |  |
| Japan East | ✓ |
| Oeste do Japão |  |
| Coreia Central | ✓ |
| Sul da Coreia | |
| Centro-Norte dos EUA  | ✓ |
| Norte da Europa  | ✓ |
| Leste da Noruega | |
| Oeste da Noruega | |
| Norte da África do Sul    | ✓ |
| Oeste da África do Sul |  |
| Centro-Sul dos Estados Unidos  | |
| Sul da Índia | |
| Sudeste Asiático    | ✓ |
| Norte da Suíça |   |
| Oeste da Suíça | |
| EAU Central | |
| Norte dos EAU |  |
| Sul do Reino Unido  | ✓ |
| Oeste do Reino Unido |     |
| DoD Central dos EUA | |
| DoD do Leste dos EUA | |
| Governo dos EUA do Arizona |      |
| US Gov - Não Regional | |
| Governo dos EUA do Texas | |
| Gov. dos EUA – Virgínia |     |
| Centro-Oeste dos EUA |     |
| Europa Ocidental   | ✓ |
| Oeste da Índia | |
| Oeste dos EUA   | ✓ |
| Oeste dos EUA 2 | ✓ |

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma [transformação de origem](data-flow-source.md).
* Saiba como criar fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md).
