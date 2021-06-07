---
title: 'Tutorial: Procurar ativos no Azure Purview e ver a linhagem deles'
description: Este tutorial descreve como procurar ativos no catálogo e ver a linhagem de dados.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696094"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Tutorial: Procurar ativos no Azure Purview (versão prévia) e ver a linhagem deles

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Este tutorial mostra como procurar ativos no Azure Purview e ver os detalhes importantes deles, como a linhagem.

Este tutorial é a *parte 3 de uma série de cinco tutoriais* em que você aprenderá os conceitos básicos de como gerenciar a governança de dados em um acervo de dados usando o Azure Purview. Este tutorial se baseia no trabalho que você concluiu na [Parte 2: Navegar até a home page do Azure Purview (versão prévia) e pesquisar um ativo](tutorial-asset-search.md).

Neste tutorial, você irá:

> [!div class="checklist"]
>
> * Procurar ativos no catálogo.
> * Ver a linhagem de ativos.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial completo: Navegar até a home page do Azure Purview (versão prévia) e pesquisar um ativo](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Procurar ativos no catálogo

No tutorial anterior, você viu como a pesquisa pode ajudar você a descobrir ativos no catálogo do Azure Purview. Outra maneira de descobrir ativos no catálogo é usando a experiência de navegação do catálogo.

Esta seção mostra como procurar o catálogo do Azure Purview.

1. Navegue até o recurso do Azure Purview no portal do Azure e selecione **Abrir o Purview Studio**. Você é direcionado automaticamente para a home page do Purview Studio.

1. Na página **Início**, selecione **Procurar ativos**.

   A página **Procurar ativos** é exibida, que mostra um resumo de todos os tipos de ativos em seu catálogo.

1. Para explorar os vários ativos do tipo Azure Data Lake Gen2 disponíveis em seu catálogo, selecione o bloco **Azure Data Lake Gen2**.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Captura de tela mostrando a página Procurar por tipo de ativo com Azure Data Lake Gen2 selecionado.":::

1. Se houver vários ativos, você poderá selecionar o cabeçalho de coluna **Nome** para classificar os ativos em ordem alfabética. Neste tutorial, há apenas um ativo do Azure Data Lake Storage Gen2.

1. Selecione o nome do ativo.

1. Selecione o conjunto de recursos **Contoso_GrossProfit_{N}.ssv**. Se esse ativo não existir em seu catálogo, escolha outro.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Lista de recursos do Azure Data Lake Storage Gen2":::

## <a name="view-the-lineage-of-assets"></a>Exibir a linhagem de ativos

Na página de detalhes do ativo, explore a fonte dos dados.

1. Selecione a guia **Linhagem** do conjunto de recursos **Contoso_GrossProfit_{N}.ssv**.

   O ativo selecionado é exibido. As informações de linhagem que você vê mostram que esse conjunto de recursos foi copiado da sua conta do Armazenamento de Blobs do Azure para o Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Captura de tela mostrando a exibição Linhagem para o ativo.":::

1. Selecione o ativo de blob nesta página e selecione o link **Alternar para o ativo**.

   Observe que a janela mudou para o conjunto de recursos de Blob do Azure, que era a origem do Azure Data Lake Storage Gen2 original.

1. Selecione a guia **Visão geral** para investigar o ativo e confirmar os detalhes dele.

Para obter informações sobre como criar uma atividade de fluxo de dados do Azure Data Factory entre um conjunto de recursos de Blob do Azure e do Azure Data Lake Storage Gen2 e observar a linhagem, confira [atividade de fluxo de dados do Azure Data Factory](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Procurar ativos no catálogo.
> * Ver a linhagem de ativos.

Avance para o próximo tutorial para saber mais sobre conjuntos de recursos, detalhes de ativos, esquemas e classificações.

> [!div class="nextstepaction"]
> [Conjuntos de recursos, detalhes do ativo, esquemas e classificações](tutorial-schemas-and-classifications.md)
