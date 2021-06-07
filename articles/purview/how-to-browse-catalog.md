---
title: Como procurar o catálogo de dados
description: Este artigo fornece uma visão geral de como procurar o catálogo de dados do Azure alcance com base no tipo de ativo.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695052"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Navegar pelo Catálogo de Dados do Azure Purview

Este artigo descreve como descobrir dados em seu catálogo de dados do Azure alcance usando o namespace hierárquico da fonte de dados.

## <a name="browse-experience"></a>Procurar experiência

Um consumidor de dados pode descobrir dados usando o namespace hierárquico familiar para cada uma das fontes de dados usando uma exibição do Explorer. Depois que a fonte de dados é registrada e verificada, o mapa de dados extrai informações sobre a estrutura (namespace hierárquico) da fonte de dados. Essas informações são usadas para criar a experiência de navegação para a descoberta de dados.

Por exemplo, você pode encontrar facilmente um conjunto de dados chamado *DateDimension* em uma pasta chamada *Dimensions* no Azure data Lake Storage Gen 2. Você pode usar a experiência "procurar por tipo de ativo" para navegar até a conta de armazenamento do ADLS Gen 2 e, em seguida, navegar até o > contêiner > pasta (s) para acessar a pasta *dimensões* específica e, em seguida, ver a tabela *DateDimension* .

Uma experiência de navegação nativa com namespace hierárquico é fornecida para cada fonte de dados correspondente.

## <a name="browse-the-data-catalog-by-asset-type"></a>Procurar o catálogo de dados por tipo de ativo

1. Você pode procurar ativos de dados, selecionando **procurar por tipo de ativo** na página inicial.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="home page alcance" border="true":::

1. Na página **procurar tipos de ativo** , os blocos são categorizados por fontes de dados. Para explorar ainda mais os ativos em cada fonte de dados, selecione o bloco correspondente.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Página procurar tipos de ativo" border="true":::

1. Na próxima página, os ativos de nível superior sob o tipo de dados escolhido são listados. Escolha um dos ativos para explorar ainda mais seu conteúdo.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Página de navegação específica do tipo de ativo. O exemplo mostrado é a conta de armazenamento do Azure" border="true":::

1. A exibição do Explorer será aberta. Comece a navegar selecionando o ativo no painel esquerdo. Os ativos filho serão listados no painel direito da página.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Exibição do Explorer" border="true":::

1. Para exibir os detalhes de um ativo, selecione o botão nome ou reticências na extrema direita.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Selecione o botão de reticências para ver a página de detalhes do ativo" border="true":::

## <a name="view-related-data-assets"></a>Exibir ativos de dados relacionados

Quando estiver na página de detalhes do ativo, você também poderá exibir outros ativos de dados relacionados. Por exemplo, você pode navegar até a pasta pai de *DateDimension* para ver a pasta *Dimensions* ou até mesmo navegar até o contêiner para ver os ativos na hierarquia específica.

1. Na página de detalhes do ativo, selecione a guia **relacionado** para ver outros ativos relacionados.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Iniciar guia relacionado" border="true":::

1. A hierarquia completa do ativo atual será listada à esquerda.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Estrutura hierárquica" border="true":::

1. Selecione qualquer nível na hierarquia para listar os ativos imediatos sob esse nível.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Selecionar hierarquia diferente" border="true":::

## <a name="next-steps"></a>Próximas etapas

- [Como criar, importar e exportar os termos do glossário](how-to-create-import-export-glossary.md)
- [Como gerenciar modelos de termo para o Glossário de negócios](how-to-manage-term-templates.md)
