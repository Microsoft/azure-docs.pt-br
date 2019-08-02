---
title: Base de dados de conhecimento em idiomas além do inglês – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker dá suporte a conteúdo da base de dados de conhecimento em vários idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para um único idioma. A primeira base de dados de conhecimento criada para um determinado serviço de QnA Maker define o idioma desse serviço.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 1203748b43ea5911d4717f451bca2cbe82753386
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447414"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte a idioma do conteúdo da base de dados de conhecimento para QnA Maker
O QnA Maker dá suporte a conteúdo da base de dados de conhecimento em vários idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para um único idioma. A primeira base de dados de conhecimento criada para um determinado serviço de QnA Maker define o idioma desse serviço. Consulte [aqui](../Overview/languages-supported.md) para a lista de idiomas com suporte.

O idioma é automaticamente reconhecido a partir do conteúdo das fontes de dados que estão sendo extraídas. Após criar um novo Serviço de QnA Maker e uma nova Base de Dados de Conhecimento nesse serviço, você poderá verificar se o idioma foi definido corretamente.

1. Navegue para o [Portal do Azure](https://portal.azure.com/).

2. Selecione **grupos de recurso** e navegue até o grupo de recursos onde o serviço de QnA Maker está implantado e selecione o recurso **Azure Search**.

    ![Selecionar o recurso do Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Selecione o índice **testkb**. Esse índice do Azure Search é sempre o primeiro criado e contém o conteúdo salvo de todas as bases de dados de dados de conhecimento no serviço. 

    ![Selecionar Testar Base de Dados de Conhecimento](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Selecione a seção **Campos** mostrando os detalhes de testkb.

    ![Selecionar Campos](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Marque a caixa do **Analisador** para ver os detalhes do idioma.

    ![Selecionar Analisador](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Você deve identificar se o Analisador está definido para um idioma específico. Esse idioma foi detectado automaticamente durante a etapa de criação da base de dados de conhecimento. Este idioma não poderá ser alterado depois que o recurso for criado.

    ![Analisador selecionado](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um bot do QnA com o Serviço de Bot do Azure](../Tutorials/create-qna-bot.md)
