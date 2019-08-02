---
title: Entidades predefinidas para Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: O LUIS incluem um conjunto de entidades predefinidas para reconhecer tipos comuns de informações, como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: db0fb8962030b536b6ea73f4141da551434cd528
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560352"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entidades predefinidas para reconhecer os tipos de dados comuns

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações como datas, horas, números, medidas e moeda. 

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 

1. Na página **Entidades**, clique em **Adicionar entidades predefinidas**.

1. Na caixa de diálogo **Adicionar as entidades predefinidas**, selecione o datetimeV2 entidade predefinida. 

    ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecione **Concluído**.

## <a name="publish-the-app"></a>Publicar o aplicativo

A maneira mais fácil para exibir o valor de uma entidade predefinida é a consulta de ponto de extremidade publicado. 

1. Na barra de ferramentas superior, selecione **Publicar**. Publique em **Produção**. 

1. Quando a notificação de êxito verde aparecer, selecione o link **Referir-se à lista de pontos de extremidade** para ver os pontos de extremidade.

1. Selecionar um ponto de extremidade. Uma nova guia do navegador é aberta para esse ponto de extremidade. Mantenha a guia do navegador aberta e continue na seção **Testar**.

## <a name="test"></a>Teste
Depois que a entidade é adicionada, você precisa treinar o aplicativo. 

Teste a nova intenção no ponto de extremidade adicionando um valor para o parâmetro **q**. Use a tabela a seguir para declarações sugeridas para **q**:

|Enunciado do teste|Valor de entidade|
|--|:--|
|Reservar um voo para amanhã|19-10-2018|
|cancelar o compromisso em 3 de março|LUIS retornou o mais recente 3 de março no passado (03-03-2018) e 3 de março do futuro (03-03-2019) porque o enunciado não especificou um ano.|
|Agendar uma reunião às 10h|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marcação de entidades que contêm um token de entidade pré-criado
 Se você tiver texto, como `HH-1234`, que você deseja marcar como uma entidade personalizada _e_ e tiver um [número pré-criado](luis-reference-prebuilt-number.md) adicionado ao modelo, não será possível marcar a entidade personalizada no portal do LUIS. Você pode marcá-la com a API. 

 Para marcar esse tipo de token, em que parte dele já está marcada com uma entidade pré-criada, remova a entidade pré-criada do aplicativo LUIS. Você não precisa treinar o aplicativo. Em seguida, marque o token com sua própria entidade personalizada. Em seguida, adicione a entidade pré-criada de volta ao aplicativo LUIS.

 Para obter outro exemplo, considere a declaração como uma lista de preferências de classe: `I want first year spanish, second year calculus, and fourth year english lit.` Se o aplicativo LUIS tiver um ordinal pré-criado adicionado, `first`, `second` e `fourth` já estarão marcados com ordinais. Se você deseja capturar o ordinal e a classe, pode criar uma entidade de composição e encapsulá-la no ordinal pré-criado e na entidade personalizada para o nome de classe.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de entidade predefinida](./luis-reference-prebuilt-entities.md)
