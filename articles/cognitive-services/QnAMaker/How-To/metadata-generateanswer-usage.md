---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite adicionar metadados na forma de pares chave-valor aos conjuntos de perguntas/respostas. Essas informações podem ser usadas para filtrar resultados para consultas de usuários e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.openlocfilehash: b634467381dc97e4a733e862e86632a089bf5f67
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765653"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Obter uma resposta de dados de conhecimento com a API GenerateAnswer e metadados

Para obter a resposta prevista à pergunta de um usuário, use a API GenerateAnswer. Quando você publica uma base de Conhecimento, essas informações para usar essa API são mostradas na página de publicação. Você pode também configurar a API para filtrar as respostas com base em marcas de metadados e testar a base de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

O QnA Maker permite adicionar metadados na forma de pares chave e valor aos conjuntos de perguntas/respostas. Essas informações podem ser usadas para filtrar resultados para consultas de usuários e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Perguntas e respostas com uma entidade de QnA de armazenamento

Primeiro, é importante reconhecer como o QnA Maker armazena os dados de pergunta/resposta. A ilustração a seguir mostra uma entidade QnA:

![Entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. A ID pode ser usada para fazer atualizações em uma entidade QnA específica.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API GenerateAnswer

Você usa a API do GenerateAnswer no bot ou aplicativo para consultar a base de dados de conhecimento com uma pergunta de usuário para obter a melhor correspondência dos conjuntos de pergunta/resposta.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter o ponto de extremidade GenerateAnswer 

Após publicar a base de dados de conhecimento, a partir do [portal do QnA Maker](https://www.qnamaker.ai), ou usando a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), será possível obter os detalhes do ponto de extremidade do GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Entre em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Em **Minhas bases de dados de conhecimento**, clique em **Exibir Código** para a base de dados de conhecimento.
    ![minhas bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    ![detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/csharp.md).

O **URL de solicitação** tem o seguinte formato: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Propriedade de solicitação HTTP|NOME|Type|Finalidade|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de Conhecimento|string|o GUID da base de dados de conhecimento.|
|Parâmetro de rota de URL|Host do ponto de extremidade QnAMaker|string|o nome do host do ponto de extremidade implantado na sua assinatura do Azure. Isso está disponível na página de configurações depois de publicar a base de Conhecimento. |
|Cabeçalho|Tipo de conteúdo|string|o tipo de mídia do corpo enviado para a API. Valor padrão é: '|
|Cabeçalho|Autorização|string|sua chave de ponto de extremidade (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo do POST|Objeto JSON|JSON|A pergunta com configurações|


O corpo JSON tem várias configurações:

|Propriedade de corpo JSON|Obrigatório|Type|Finalidade|
|--|--|--|--|
|`question`|obrigatório|string|Uma pergunta do usuário a serem enviados para sua base de dados de Conhecimento.|
|`top`|opcional|inteiro|o número de resultados classificados para incluir na saída. O valor padrão é 1.|
|`userId`|opcional|string|ID exclusiva para identificar o usuário. Essa ID será registrada nos logs de chat.|
|`scoreThreshold`|opcional|inteiro|Somente respostas com pontuação de confiança acima desse limite serão retornadas. O valor padrão é 0.|
|`isTest`|opcional|boolean|Se definido como true, retorna os resultados dos `testkb` índice de pesquisa em vez de índice publicado.|
|`strictFilters`|opcional|string|se especificado, informa ao QnA Maker para retornar apenas as respostas com os metadados especificados. Use `none` para indicar a resposta não deve ter nenhum filtro de metadados. |

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades da resposta GenerateAnswer

Uma resposta bem-sucedida retorna um status de 200 e uma resposta JSON. 

|Propriedade de respostas (classificada pela pontuação)|Finalidade|
|--|--|
|para seu app&#39;s|uma pontuação de classificação entre 0 e 100.|
|ID|a ID exclusiva atribuída à resposta.|
|Perguntas|as perguntas fornecidas pelo usuário.|
|resposta|a resposta à pergunta.|
|fonte|o nome da fonte da qual a resposta foi extraída ou salva na base de dados de conhecimento.|
|metadata|os metadados associados à resposta.|
|metadata.name|nome dos metadados. (cadeia de caracteres, Comprimento máx.: 100, obrigatório)|
|metadata.value: valor dos metadados. (cadeia de caracteres, Comprimento máx.: 100, obrigatório)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Usando metadados permite que você filtre as respostas por marcas de metadados personalizados

Adicionar metadados permite que você filtre as respostas por essas marcas de metadados. Considere os dados de perguntas frequentes abaixo. Adicione metadados à base de dados de conhecimento, clicando no ícone de metadados.

![adicionar metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "Quando esse hotel fecha?" onde a intenção está implícita para o restaurante "Paradise."

Como os resultados são necessários somente para o restaurante "Paradise", é possível definir um filtro na chamada do GenerateAnswer nos metadados "Nome do restaurante", conforme a seguir.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de pergunta e resposta para manter o contexto de conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondente do conjunto correspondente de pergunta/resposta. Essas informações podem ser usadas em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores. 

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

A página de publicação também fornece informações para gerar uma resposta com [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./create-knowledge-base.md)
