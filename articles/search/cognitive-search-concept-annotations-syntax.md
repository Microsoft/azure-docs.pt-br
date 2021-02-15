---
title: Referenciar entradas e saídas em habilidades
titleSuffix: Azure Cognitive Search
description: Explica a sintaxe da anotação e como fazer referência a uma anotação nas entradas e saídas de um conconhecimento em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935527"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Como fazer referência a anotações em um Azure Pesquisa Cognitiva skillset

Neste artigo, você aprende a referenciar anotações em definições de habilidades usando exemplos para ilustrar diferentes cenários. Conforme o conteúdo de um documento flui por um conjunto de habilidades, ele é enriquecido com anotações. Anotações podem ser usadas como entradas para mais enriquecimento downstream ou podem ser mapeadas para um campo de saída em um índice. 
 
Os exemplos neste artigo são baseados campo *content* gerado automaticamente pelos [indexadores de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) como parte da fase de quebra de documento. Ao fazer referência a documentos de um contêiner de Blob, use um formato como `"/document/content"`, em que o campo *content* faz parte de *document*. 

## <a name="background-concepts"></a>Conceitos em segundo plano

Antes de examinarmos a sintaxe, vamos rever alguns conceitos importantes para entender melhor os exemplos fornecidos mais adiante neste artigo.

| Termo | Descrição |
|------|-------------|
| Documento enriquecido | Um documento enriquecido é uma estrutura interna criada e usada pelo pipeline para reter todas as anotações relacionadas a um documento. Pense em um documento enriquecido como uma árvore de anotações. Em geral, uma anotação criada de uma anotação anterior se torna seu filho.<p/>Documentos enriquecidos existem somente pela duração da execução do conjunto de habilidades. Após o conteúdo ser mapeado para o índice de pesquisa, o documento enriquecido deixará de ser necessário. Embora você não interaja diretamente com documentos enriquecidos, é útil ter um modelo mental dos documentos ao criar um conjunto de habilidades. |
| Contexto de enriquecimento | O contexto em que o enriquecimento ocorre, em termos de qual elemento é enriquecido. Por padrão, o contexto de enriquecimento está no nível do `"/document"` e seu escopo são documentos individuais. Quando uma habilidade é executada, suas saídas se tornam [propriedades do contexto definido](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exemplo 1: referência de anotação simples

No armazenamento de BLOBs do Azure, suponha que você tenha uma variedade de arquivos contendo referências a nomes de pessoas que você deseja extrair usando o reconhecimento de entidade. Na definição de habilidade abaixo, `"/document/content"` é a representação textual de todo o documento e "people" é uma extração de nomes completos para entidades identificadas como pessoas.

Como o contexto padrão é `"/document"`, a lista de pessoas agora pode ser referenciada como `"/document/people"`. Neste caso específico, `"/document/people"` é uma anotação que agora poderia ser mapeada para um campo em um índice ou usada em outra habilidade do mesmo conjunto de habilidades.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Exemplo 2: referenciar uma matriz em um documento

Este exemplo dá continuidade ao anterior, mostrando como invocar uma etapa de enriquecimento várias vezes no mesmo documento. Suponha que o exemplo anterior tenha gerado uma matriz de cadeias de caracteres com 10 nomes de pessoas provenientes de um único documento. Faz sentido que a próxima etapa seja um segundo enriquecimento que extrai o sobrenome de um nome completo. Como há 10 nomes, esta etapa precisa ser chamada 10 vezes no documento, uma vez para cada pessoa. 

Para invocar o número correto de iterações, defina o contexto como `"/document/people/*"`, em que o asterisco (`"*"`) representa todos os nós no documento enriquecido como descendentes de `"/document/people"`. Embora essa habilidade seja definida apenas uma vez na matriz de habilidades, ela é chamada para cada membro no documento até que todos os membros sejam processados.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Quando as anotações forem matrizes ou coleções de cadeias de caracteres, talvez você queira que o alvo sejam membros específicos em vez da matriz inteira. O exemplo anterior gera uma anotação chamada `"last"` em cada nó representado pelo contexto. Se quiser fazer referência a essa família de anotações, você poderá usar a sintaxe `"/document/people/*/last"`. Se quiser fazer referência a uma anotação específica, você poderá usar um índice explícito: `"/document/people/1/last`" para referenciar o sobrenome da primeira pessoa identificada no documento. Observe que nessa sintaxe as matrizes são "indexadas em 0".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exemplo 3: referenciar membros de uma matriz

Às vezes, você precisa agrupar todas as anotações de um tipo específico para passá-las para uma habilidade específica. Considere uma habilidade personalizada hipotética que identifica o sobrenome mais comum de todos os sobrenomes extraídos no exemplo 2. Para fornecer apenas os sobrenomes à habilidade personalizada, especifique o contexto como `"/document"` e a entrada como `"/document/people/*/lastname"`.

Observe que a cardinalidade de `"/document/people/*/lastname"` é maior do que a do documento. Pode haver 10 nós de sobrenome, enquanto há apenas um nó de documento para este documento. Neste caso, o sistema criará automaticamente uma matriz de `"/document/people/*/lastname"` contendo todos os elementos no documento.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Confira também
+ [Como integrar uma habilidade personalizada a um pipeline de enriquecimento](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](/rest/api/searchservice/create-skillset)
+ [How to map enriched fields to an index](cognitive-search-output-field-mapping.md) (Como mapear campos enriquecidos para um índice)