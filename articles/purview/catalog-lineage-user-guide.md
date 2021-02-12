---
title: Guia do usuário de linhagem do catálogo de dados (versão prévia)
description: Este artigo fornece uma visão geral do recurso de linhagem do catálogo do Azure alcance.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: a319dbce2502f35272cf9b70da2022f581d64275
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551573"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Guia do usuário de linhagem do catálogo de dados do Azure alcance

Este artigo fornece uma visão geral dos recursos de linhagem de dados no catálogo de dados do Azure alcance.

## <a name="background"></a>Segundo plano

Um dos recursos de plataforma do Azure alcance é a capacidade de mostrar a linhagem entre conjuntos de dados criados por processos de data. Sistemas como Data Factory, compartilhamento de dados e Power BI capturam a linhagem dos dados conforme eles se movem. O relatório de linhagem personalizada também tem suporte por meio de ganchos do Atlas e da API REST.

## <a name="lineage-collection"></a>Coleção de linhagem 
 Os metadados coletados no Azure alcance de sistemas de dados corporativos são Unidos em para mostrar uma linhagem de dados de ponta a ponta. Os sistemas de dados que coletam a linhagem em alcance são amplamente categorizados em três tipos a seguir.

### <a name="data-processing-system"></a>Sistema de processamento de dados
As ferramentas de ETL e integração de dados podem enviar a linhagem por push para o Azure alcance no momento da execução. Ferramentas como Data Factory, compartilhamento de dados, Synapse, Azure Databricks e assim por diante, pertencem a essa categoria de sistemas de dados. Os sistemas de processamento de dados referem-se a fontes de armazenamento e de bancos diferentes, como origem, para criar conjuntos de dado de destino. A lista de sistemas de processamento de dados atualmente integrados ao alcance para linhagem está listada na tabela abaixo.


| Sistema de processamento de dados | Escopo com suporte |
| ---------------------- | ------------|
| Fábrica de dados do Azure | [Atividade de cópia](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [Atividade de fluxo de dados](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Atividade executar pacote SSIS](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Instantâneo de compartilhamento](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Sistemas de armazenamento de dados
Os bancos de dados & soluções de armazenamento como SQL Server, Teradata e SAP têm mecanismos de consulta para transformar dados usando linguagem de script. A linhagem de dados de procedimentos armazenados é coletada em alcance e cojunto com a linhagem de outros sistemas.

| Sistema de armazenamento de dados | Escopo com suporte |
| ---------------------- | ------------|
| Teradata | Procedimentos armazenados

### <a name="data-analytics--reporting-systems"></a>Análise de dados & sistemas de relatórios
Sistemas de dados como o Azure ML e Power BI a linhagem de relatório no Azure alcance. Esses sistemas usarão os conjuntos de os de sistemas de armazenamento e processam seu meta modelo para criar painel de BI, experimentos de ML e assim por diante.

| Sistema de relatórios & de análise de dados | Escopo com suporte |
| ---------------------- | ------------|
| Power BI | [Conjuntos de gráficos, fluxos de os relatórios & dashboards](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Introdução à linhagem

A linhagem em alcance inclui conjuntos de valores e processos. Os conjuntos de linhas também são chamados de nós, enquanto os processos também podem ser chamados de bordas:

* **DataSet (nó)**: um conjunto de dados (estruturado ou não estruturado) fornecido como uma entrada para um processo. Por exemplo, uma tabela SQL, BLOB do Azure e arquivos (como. csv e. xml) são todos considerados conjuntos de os. Na seção linhagem de alcance, os conjuntos de valores são representados por caixas retangulares.

* **Processo (borda)**: uma atividade ou transformação executada em um conjunto de um DataSet é chamada de processo. Por exemplo, atividade de cópia do ADF, instantâneo de compartilhamento de dados e assim por diante. Na seção de linhagem de alcance, os processos são representados por caixas arredondadas.

Para acessar as informações de linhagem de um ativo no alcance, siga as etapas:

1. Na portal do Azure, vá para a [página contas do Azure alcance](https://aka.ms/purviewportal).

1. Selecione sua conta do Azure alcance na lista e, em seguida, selecione **Iniciar conta do alcance** na página **visão geral** .

1. Na **Home** Page do Azure alcance, procure um nome de conjunto de dados ou o nome do processo, como a cópia do ADF ou a atividade de fluxo do Data. E pressione Enter.

1. Nos resultados da pesquisa, selecione o ativo e selecione sua guia de **linhagem** .

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Captura de tela mostrando como selecionar a guia linhagem." border="true":::

## <a name="asset-level-lineage"></a>Linhagem no nível do ativo

O Azure alcance dá suporte à linhagem de nível de ativo para os conjuntos de valores e processos. Para ver a linhagem do nível do ativo, vá para a guia **linhagem** do ativo atual no catálogo. Selecione o nó atual do ativo do conjunto de ativos. Por padrão, a lista de colunas que pertencem aos dados aparece no painel esquerdo.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Captura de tela mostrando como selecionar colunas de exibição na página de linhagem" border="true":::

## <a name="column-level-lineage"></a>Linhagem em nível de coluna

O Azure alcance dá suporte à linhagem em nível de coluna para os conjuntos de valores. Para ver a linhagem em nível de coluna, vá para a guia **linhagem** do ativo atual no catálogo e siga as etapas abaixo:

1. Quando você estiver na guia linhagem, no painel esquerdo, marque a caixa de seleção ao lado de cada coluna que você deseja exibir na linhagem de dados.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Captura de tela mostrando como selecionar colunas para exibir na página de linhagem." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

1. Passe o mouse sobre uma coluna selecionada no painel esquerdo ou no conjunto de espaço da tela de linhagem para ver o mapeamento de coluna. Todas as instâncias de coluna são realçadas.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Captura de tela mostrando como passar o mouse sobre um nome de coluna para realçar o fluxo de coluna em um caminho de linhagem de dados." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

1. Se o número de colunas for maior do que o que pode ser exibido no painel esquerdo, use a opção de filtro para selecionar uma coluna específica por nome. Como alternativa, você pode usar o mouse para percorrer a lista.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Captura de tela mostrando como filtrar colunas por nome de coluna na página de linhagem." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

1. Se a tela de linhagem contiver mais nós e bordas, use o filtro para selecionar ativo de dados ou nós de processo por nome. Como alternativa, você pode usar o mouse para percorrer a janela de linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Captura de tela mostrando nós de ativos de dados por nome na página de linhagem." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

1. Use a alternância no painel esquerdo para realçar a lista de conjuntos de valores na tela de linhagem. Se você desativar a alternância, qualquer ativo que contenha pelo menos uma das colunas selecionadas será exibido. Se você ativar a alternância, somente os conjuntos de valores que contiverem todas as colunas serão exibidos.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Captura de tela mostrando como usar a alternância para filtrar a lista de nós na página de linhagem." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

1. Selecione **alternar para ativo** em qualquer ativo para exibir seus metadados correspondentes na exibição de linhagem. Fazer isso é uma maneira eficaz de navegar para outro ativo no catálogo a partir da exibição de linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Captura de tela como selecionar alternar para ativo em um ativo de dados de linhagem." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

1. A tela de linhagem pode se tornar complexa para conjuntos de valores populares. Para evitar confusão, a exibição padrão mostrará apenas cinco níveis de linhagem para o ativo em foco. O restante da linhagem pode ser expandido clicando nas bolhas na tela de linhagem. Os consumidores de dados também podem ocultar os ativos na tela sem interesse. Para reduzir ainda mais a desordem, desative a opção Alternar **mais linhagem** na parte superior da tela de linhagem. Esta ação ocultará todas as bolhas na tela de linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Captura de tela mostrando como alternar mais linhagem." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

1. Use os botões inteligentes na tela de linhagem para obter uma visão ideal da linhagem. Layout automático, zoom para ajustar, ampliar/reduzir, tela inteira e mapa de navegação estão disponíveis para uma experiência de linhagem de imersão no catálogo.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Captura de tela mostrando como selecionar os botões inteligentes de linhagem." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Próximas etapas

* [Link para Azure Data Factory para linhagem](how-to-link-azure-data-factory.md)
* [Link para o compartilhamento de dados do Azure para linhagem](how-to-link-azure-data-share.md)