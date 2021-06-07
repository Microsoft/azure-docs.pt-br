---
title: Detalhes do documento - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: A página de lista de documentos mostra os primeiros 10 documentos no workspace. Para cada um dos documentos, ela exibe o nome, pareamento, tipo, idioma, carimbo de data/hora do upload e endereço de email do usuário que carregou o documento.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 56093204516e156d670097c22b4edab42db54bde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895604"
---
# <a name="view-document-details"></a>Exibir detalhes do documento

A página de lista de documentos mostra os primeiros 10 documentos no workspace. Para cada um dos documentos, ela exibe o nome, pareamento, tipo, idioma, carimbo de data/hora do upload e endereço de email do usuário que carregou o documento.

Clique em um documento individual para exibir a página de detalhes do documento. A página de detalhes de documento exibe a lista de frases extraídas do documento.

- Por padrão, a exibição de idiomas de origem e destino "lado a lado" é selecionada no campo suspenso, mas você pode alternar para exibir frases no idioma de origem ou de destino.
- Por padrão, são exibidas 20 frases por página. Você pode usar o controle de paginação para navegar entre as páginas.

![detalhes do documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Excluir um documento

Para excluir um documento, o usuário deverá ser um proprietário do workspace. Além disso, se um documento estiver em uso por um modelo, ou seja, em qualquer parte do processo de treinamento ou em qualquer parte do processo de implantação, o documento não poderá ser excluído.

1. Acessar a página de documento
2. Passe o mouse sobre qualquer registro de documento e clique no ícone da lixeira.

    ![Excluir documento](media/how-to/how-to-delete-document-1.png)

3. Confirme a exclusão.

    ![Confirmar exclusão](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como treinar um modelo](how-to-train-model.md).
