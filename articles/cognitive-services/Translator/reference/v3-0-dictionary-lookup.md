---
title: Método de pesquisa de dicionário de API do Translator texto
titleSuffix: Azure Cognitive Services
description: Use o método de pesquisa de dicionário de API do Translator texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 9a06c8e3b50c3f54971694f8d3924a3a5ba5f071
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595043"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>API de Tradução de Texto 3.0: Pesquisa no dicionário

Fornece as traduções alternativas para uma palavra e um pequeno número de frases idiomáticas. Cada tradução tem uma parte da fala e uma lista de traduções reversas. As traduções reversas possibilitam que um usuário entenda a tradução no contexto. A operação [Exemplo de Dicionário](./v3-0-dictionary-examples.md) permite mais detalhamento para ver os usos de exemplo de cada par de tradução.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de entrada. O idioma de origem deve ser um dos [idiomas compatíveis](./v3-0-languages.md) incluídos no escopo de `dictionary`.</td>
  </tr>
  <tr>
    <td>para</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos [idiomas compatíveis](./v3-0-languages.md) incluídos no escopo de `dictionary`.</td>
  </tr>
</table>

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho (s) de autenticação</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>Veja <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>O tamanho do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. É possível omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta nomeado `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Solicitar corpo

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa o termo a pesquisar.

```json
[
    {"Text":"fly"}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 100 caracteres incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: uma cadeia de caracteres fornecendo a forma normalizada do termo de origem. Por exemplo, se a solicitação for "JOHN", o formato normalizado será "john". O conteúdo desse campo se torna a entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).
    
  * `displaySource`: uma cadeia de caracteres fornecendo o termo de origem na forma mais adequada para exibição do usuário final. Por exemplo, se a entrada for "JOHN", o formato de exibição refletirá a ortografia normal do nome: "John". 

  * `translations`: uma lista de traduções para o termo de origem. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `normalizedTarget`: uma cadeia de caracteres fornecendo a forma normalizada desse termo no idioma de destino. Esse valor deve ser usado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).

    * `displayTarget`: uma cadeia de caracteres fornecendo o termo no idioma de destino e na forma mais adequada para exibição do usuário final. Em geral, isso diferirá do `normalizedTarget` apenas em termos de maiúsculas e minúsculas. Por exemplo, um substantivo próprio como "Juan" terá `normalizedTarget = "juan"` e `displayTarget = "Juan"`.

    * `posTag`: uma cadeia de caracteres associando esse termo a uma marca de parte da fala.

        | Nome da marca | Descrição  |
        |----------|--------------|
        | ADJ      | Adjetivos   |
        | ADV      | Advérbios      |
        | CONJ     | Conjunções |
        | DET      | Determinantes  |
        | MODAL    | Verbos        |
        | SUBST     | Substantivos        |
        | PREP     | Preposições |
        | PRON     | Pronomes     |
        | VERBO     | Verbos        |
        | OUTROS    | Outros        |

        Como uma observação de implementação, essas marcas foram determinadas por marcação de parte de fala no lado em inglês e, em seguida, pegando a marca mais frequente para cada par de origem/destino. Assim, se as pessoas costumam traduzir uma palavra em espanhol para uma marcação de parte da fala diferente em inglês, as marcações podem acabar ficando incorretas (com relação à palavra em espanhol).

    * `confidence`: um valor entre 0,0 e 1,0 que representa a "confiança" (ou, talvez mais precisamente, a "probabilidade nos dados de treinamento") daquele par de tradução. A soma das pontuações de confiança para uma palavra de origem pode ou não somar 1,0. 

    * `prefixWord`: uma cadeia de caracteres que fornece a palavra para exibir como um prefixo da tradução. Atualmente, isso é o determinante de gênero de substantivos em idiomas que têm determinantes de gênero. Por exemplo, o prefixo da palavra em espanhol "mosca" é "la", uma vez que "mosca" é um substantivo feminino em espanhol. Isso só é dependente da tradução, e não da origem. Se não houver nenhum prefixo, será uma cadeia de caracteres vazia.
    
    * `backTranslations`: uma lista de "traduções reversas" do destino. Por exemplo, palavras de origem para as quais o destino pode ser traduzido. É garantido que a lista contenha a palavra de origem que foi solicitada (por exemplo, se a palavra de origem que está sendo pesquisada for "fly", será garantido que "fly" estará na lista `backTranslations`). No entanto, não é garantido que estará na primeira posição, e geralmente não estará. Cada elemento da lista `backTranslations` é um objeto descrito pelas seguintes propriedades:

        * `normalizedText`: uma cadeia de caracteres fornecendo a forma normalizada do termo de origem é uma tradução reversa do destino. Esse valor deve ser usado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).        

        * `displayText`: uma cadeia de caracteres fornecendo o termo de origem que é uma tradução reversa do destino na forma mais adequada para a exibição do usuário final.

        * `numExamples`: um inteiro representando o número de exemplos que estão disponíveis para esse par de tradução. Exemplos reais devem ser recuperados com uma chamada separada para [exemplos de pesquisa](./v3-0-dictionary-examples.md). O número destina-se principalmente a facilitar a exibição em um UX. Por exemplo, uma interface do usuário pode adicionar um hiperlink para uma tradução reversa se o número de exemplos é maior que zero e mostrar a tradução reversa como texto sem formatação se não houver nenhum exemplo. Observe que o número real de exemplos retornados por uma chamada para [exemplos de pesquisa](./v3-0-dictionary-examples.md) pode ser menor que `numExamples`, pois filtragem adicional pode ser aplicada rapidamente para remover os exemplos "ruins".
        
        * `frequencyCount`: um inteiro representando a frequência desse par de tradução nos dados. O principal objetivo desse campo é fornecer uma interface do usuário com um meio de classificar as traduções reversas para que os termos mais frequentes apareçam primeiro.

    > [!NOTE]
    > Se o termo que está sendo pesquisado não existir no dicionário, a resposta será 200 (OK), mas a lista `translations` será uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como pesquisar traduções alternativas em espanhol do termo em inglês `fly`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

O corpo da resposta (abreviado para maior clareza) é:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Este exemplo mostra o que acontece quando o termo que está sendo pesquisado não existe para o par de dicionários válido.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Uma vez que o termo não foi encontrado no dicionário, o corpo da resposta inclui uma lista `translations` vazia.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
