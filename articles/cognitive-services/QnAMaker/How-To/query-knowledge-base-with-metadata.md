---
title: Filtrar contextualmente usando metadados
titleSuffix: Azure Cognitive Services
description: QnA Maker filtra os pares QnA por metadados.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022060"
---
# <a name="filter-responses-with-metadata"></a>Filtrar respostas com metadados

QnA Maker permite adicionar metadados, na forma de pares de chave e valor, aos seus pares de perguntas e respostas. Você pode usar essas informações para filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como QnA Maker armazena os dados de pergunta e resposta. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar a ID para fazer atualizações em uma entidade QnA específica.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para filtrar respostas por marcas de metadados personalizadas

A adição de metadados permite filtrar as respostas por essas marcas de metadados. Adicione a coluna de metadados do menu de **Opções de exibição** . Adicione metadados à sua base de dados de conhecimento selecionando o ícone de metadados **+** para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Captura de tela da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "quando este hotel é fechado?", em que a intenção é implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", você pode definir um filtro na chamada GenerateAnswer nos metadados "nome do restaurante". O exemplo a seguir mostra isso:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>AND lógico por padrão

Para combinar vários filtros de metadados na consulta, adicione os filtros de metadados adicionais à matriz da `strictFilters` propriedade. Por padrão, os valores são logicamente combinados (e). Uma combinação lógica requer que todos os filtros correspondam aos pares de QnA para que o par seja retornado na resposta.

Isso é equivalente a usar a `strictFiltersCompoundOperationType` propriedade com o valor de `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>OR lógico usando a propriedade strictFiltersCompoundOperationType

Ao combinar vários filtros de metadados, se você estiver preocupado apenas com um ou alguns dos filtros correspondentes, use a `strictFiltersCompoundOperationType` propriedade com o valor de `OR` .

Isso permite que sua base de dados de conhecimento retorne respostas quando qualquer filtro corresponder, mas não retornará respostas que não têm metadados.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Exemplos de metadados em guias de início rápido

Saiba mais sobre metadados no início rápido do portal QnA Maker para obter os metadados:
* [Criação – adicionar metadados ao par de pergunta e resposta](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta – filtrar respostas por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de perguntas e respostas para manter o contexto da conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondentes do par de perguntas e respostas correspondentes. Você pode usar essas informações em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analise sua base de conhecimentos](../How-to/get-analytics-knowledge-base.md)
