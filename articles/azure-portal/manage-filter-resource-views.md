---
title: Exibir e filtrar informações de recursos do Azure
description: Filtre informações e use diferentes exibições para entender melhor seus recursos do Azure.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771627"
---
# <a name="view-and-filter-azure-resource-information"></a>Exibir e filtrar informações de recursos do Azure

O portal do Azure permite que você procure informações detalhadas sobre os recursos em suas assinaturas do Azure. Este artigo mostra como filtrar informações e usar diferentes exibições para entender melhor seus recursos.

O artigo se concentra na tela **Todos os recursos** mostrada na captura a seguir. As telas para tipos de recursos individuais, como máquinas virtuais, têm opções diferentes, como iniciar e parar uma VM.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Exibição do portal do Azure de todos os recursos":::

## <a name="filter-resources"></a>Filtrar recursos

Comece a explorar **Todos os recursos** usando filtros para você se concentrar em um subconjunto dos seus recursos. A captura de tela a seguir mostra a filtragem em grupos de recursos, selecionando dois dos seis grupos de recursos em uma assinatura.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Filtrar exibição com base em grupos de recursos":::

Você pode combinar filtros, incluindo aqueles com base em pesquisas de texto, conforme mostrado na captura de tela a seguir. Nesse caso, os resultados têm como escopo os recursos que contêm "SimpleWinVM" em um dos dois grupos de recursos já selecionados.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Filtrar exibição com base em uma entrada de texto":::

Para alterar quais colunas são incluídas em uma exibição, selecione **Gerenciar exibição** e **Editar colunas**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Editar colunas mostradas na exibição":::

## <a name="save-use-and-delete-views"></a>Salvar, usar e excluir exibições

Você pode salvar exibições que incluem os filtros e as colunas que você selecionou. Para salvar e usar uma exibição:

1. Selecione **Gerenciar exibição** e, em seguida, **Salvar exibição**.

1. Digite um nome para a exibição e selecione **OK**. A exibição salva agora aparece no menu **Gerenciar exibição**.

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Exibição salva":::

1. Para usar uma exibição, alterne entre **Padrão** e uma das suas exibições para ver como isso afeta a lista de recursos exibidos.

Para excluir uma exibição:

1. Selecione **Gerenciar exibição** e, em seguida, **Procurar todas as exibições**.

1. No painel **Exibições salvas**, selecione a exibição e o ícone **Excluir** ![ícone Excluir exibição](media/manage-filter-resource-views/icon-delete.png).

## <a name="export-information-from-a-view"></a>Exportar informações de uma exibição

Você pode exportar as informações de recurso de uma exibição. Para exportar informações no formato CSV:

1. Selecione **Exportar para CSV**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Captura de tela da exportação para o formato CSV":::

1. Salve o arquivo localmente e abra-o no Excel ou em outro aplicativo compatível com o formato CSV. 

Ao navegar pelo portal, você verá outras áreas em que pode exportar informações, como um grupo de recursos individual.

## <a name="summarize-resources-with-visuals"></a>Resumir recursos com visuais

As exibições que examinamos até agora foram _exibições de lista_, mas também há _exibições de resumo_ que incluem visuais. Você pode salvar e usar essas exibições assim como as exibições de lista. Os filtros persistem entre os dois tipos de exibições. Há exibições padrão, como a exibição **Localização** mostrada abaixo, além de exibições relevantes para serviços específicos, como a exibição **Status** para o Armazenamento do Azure.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Resumo de recursos em uma exibição de mapa":::

Para salvar e usar uma exibição de resumo:

1. No menu Exibir, selecione **Exibição de resumo**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Menu de exibição de resumo":::

1. A exibição de resumo permite resumir por diferentes atributos, incluindo **Local** e **Tipo**. Selecione uma opção **Resumir por** e um visual apropriado. A captura de tela a seguir mostra o **Resumo do tipo** com um visual **Gráfico de barras**.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Resumo do tipo mostrando um gráfico de barras":::

1. Selecione **Gerenciar exibição** e **Salvar** para salvar essa exibição, como você fez com a exibição de lista.

1. Na exibição de resumo, em **Resumo do tipo**, selecione uma barra no gráfico. A seleção da barra fornece uma lista filtrada até um tipo de recurso.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Todos os recursos filtrados por tipo":::

## <a name="run-queries-in-azure-resource-graph"></a>Executar consultas no Azure Resource Graph

O Azure Resource Graph fornece uma exploração de recursos eficiente e de bom desempenho com a capacidade de fazer consultas em escala em um conjunto de assinaturas. A tela **Todos os recursos** no portal do Azure inclui um link para abrir uma consulta do Resource Graph cujo escopo é a exibição filtrada atual.

Para executar uma consulta do Resource Graph:

1. Selecione **Abrir consulta**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Abrir consulta do Azure Resource Graph":::

1. No **Gerenciador do Azure Resource Graph**, selecione **Executar consulta** para ver os resultados.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Executar consulta do Azure Resource Graph":::

    Para obter informações, confira [Executar a primeira consulta do Resource Graph usando o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Próximas etapas

[Visão geral do portal do Azure](azure-portal-overview.md)

[Criar e compartilhar painéis no portal do Azure](azure-portal-dashboards.md)
