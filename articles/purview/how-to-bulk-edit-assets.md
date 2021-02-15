---
title: Como marcar vários termos do glossário em uma lista de ativos selecionados
description: Aprenda ativos de edição em massa no Azure alcance.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: c42a6894c33993dc9aee5a9fdd10b1c3a3627320
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97372072"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>Como editar ativos em massa para marcar os termos do glossário

Este artigo descreve como marcar vários termos de glossário em uma lista de ativos selecionados em uma única ação.

### <a name="add-assets-to-view-selected-list-using-search"></a>Adicionar ativos para exibir a lista selecionada usando a pesquisa

1. Pesquise o ativo de dados que você deseja adicionar à lista para edição em massa.

2. Na página resultado da pesquisa, focalize o ativo que você deseja adicionar à lista de exibição de edição em massa **selecionada** para ver uma caixa de seleção.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Captura de tela da caixa de seleção.":::

3. Marque a caixa de seleção para adicioná-la à lista de exibição de edição em massa **selecionada** . Depois de adicionado, você verá o ícone itens selecionados na parte inferior da página.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Captura de tela da lista.":::

4. Repita as etapas acima para adicionar todos os ativos de dados à lista.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Adicionar ativos para exibir a lista selecionada na página de detalhes do ativo

1. Na página de detalhes do ativo, marque a caixa de seleção no canto superior direito para adicionar o ativo à lista de exibição de edição em massa **selecionada** .

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Captura de tela do ativo.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Edite ativos em massa na lista exibir selecionados para adicionar, substituir ou remover os termos do glossário.

1. Uma delas é feita com a identificação de todos os ativos de dados que precisam ser editados em massa, selecione **Exibir lista selecionada** na página de resultados da pesquisa ou na página de detalhes do ativo.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Captura de tela da exibição.":::

2. Examine a lista e selecione **remover** se desejar remover os termos.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Captura de tela da remoção.":::

3. Selecione edição em massa para adicionar, remover ou substituir os termos do glossário de todos os ativos selecionados.

4. Para adicionar os termos do glossário, selecione operação como **Adicionar**. Selecione todos os termos do glossário que você deseja adicionar ao novo valor. Selecione aplicar ao concluir.
    - Adicionar operação acrescentará um novo valor à lista de termos do glossário já marcados para os ativos de dados.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Captura de tela da adição.":::

5. Para substituir os termos do glossário, selecione a operação como **substituir por**. Selecione todos os termos do glossário que você deseja substituir no novo valor. Selecione aplicar ao concluir.
    - A operação de substituição substituirá todos os termos do glossário dos ativos de dados selecionados pelos termos selecionados em novo valor.
   
6. Para remover os termos do glossário, selecione a operação como **remover**. Selecione aplicar ao concluir.
    - A operação de remoção removerá todos os termos do glossário dos ativos de dados selecionados.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Captura de tela dos termos de remoção.":::

7. Depois de concluir, feche a folha de edição em massa selecionando **fechar** ou **remover tudo e fechar**. Fechar não removerá os ativos selecionados, enquanto remover tudo e fechar removerá todos os ativos selecionados.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Captura de tela do fechamento.":::

   > [!Important]
   > O número recomendado de ativos para edição em massa é 15. A seleção de mais de 15 pode causar problemas de desempenho.
   > A caixa **Exibir selecionado** ficará visível somente se houver pelo menos um ativo selecionado.


Siga o [tutorial: criar e importar os termos do glossário](how-to-create-import-export-glossary.md) para saber mais.
