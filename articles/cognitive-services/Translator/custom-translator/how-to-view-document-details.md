---
title: Detalhes do documento - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: A página de lista de documentos mostra os primeiros 10 documentos no espaço de trabalho. Para cada um dos documentos, ela exibe o nome, pareamento, tipo, idioma, carimbo de data/hora do upload e endereço de email do usuário que carregou o documento.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595585"
---
# <a name="view-document-details"></a>Exibir detalhes do documento

A página de lista de documentos mostra os primeiros 10 documentos no espaço de trabalho. Para cada um dos documentos, ela exibe o nome, pareamento, tipo, idioma, carimbo de data/hora do upload e endereço de email do usuário que carregou o documento.

Clique em um documento individual para exibir a página de detalhes do documento. A página de detalhes de documento exibe a lista de frases extraídas do documento.

- Por padrão, o idioma de "origem" é selecionado no campo suspenso, mas é possível alternar para exibir as frases no idioma de destino.
- Por padrão, são exibidas 20 frases por página. Você pode usar o controle de paginação para navegar entre as páginas.

![detalhes do documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Excluir um documento

Para excluir um documento, o usuário deverá ser um proprietário do espaço de trabalho. Além disso, se um documento estiver em uso por um modelo, ou seja, em qualquer parte do processo de treinamento ou em qualquer parte do processo de implantação, o documento não poderá ser excluído.

1. Acessar a página de documento
2.  Passe o mouse sobre qualquer registro de documento e clique no ícone da lixeira.

    ![Excluir documento](media/how-to/how-to-delete-document-1.png)

3.  Confirme a exclusão.

    ![Confirmar exclusão](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como treinar um modelo](how-to-train-model.md).
