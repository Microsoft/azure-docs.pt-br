---
title: Sinônimos para expansão de consulta em um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Crie um mapa de sinônimos para expandir o escopo de uma consulta de pesquisa em um índice de Pesquisa Cognitiva do Azure. O escopo é ampliado para incluir termos equivalentes fornecidos por você em uma lista.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251617"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinônimos no Azure Pesquisa Cognitiva

Com os mapas de sinônimos, você pode associar termos equivalentes, expandindo o escopo de uma consulta sem que o usuário tenha que realmente fornecer o termo. Por exemplo, supondo que "Dog", "canino" e "filhote" sejam sinônimos, uma consulta em "canino" será correspondente em um documento que contém "Dog".

## <a name="create-synonyms"></a>Criar sinônimos

Um mapa de sinônimos é um ativo que pode ser criado uma vez e usado por vários índices. A [camada de serviço](search-limits-quotas-capacity.md#synonym-limits) determina a quantidade de mapas de sinônimos que você pode criar, variando de três mapas de sinônimos para as camadas gratuita e básica, até 20 para as camadas padrão. 

Você pode criar vários mapas de sinônimos para diferentes idiomas, como versões em inglês e francês, ou léxicos, se o seu conteúdo inclui terminologia técnica ou obscura. Embora você possa criar vários mapas de sinônimos em seu serviço de pesquisa, um campo pode usar apenas um deles.

Um mapa de sinônimos consiste em nome, formato e regras que funcionam como entradas de mapa de sinônimos. O único formato com suporte é `solr` e o `solr` formato determina a construção da regra.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Para criar um mapa de sinônimos, use [criar mapa de sinônimos (API REST)](/rest/api/searchservice/create-synonym-map) ou um SDK do Azure. Para desenvolvedores de C#, é recomendável começar com [Adicionar sinônimos na pesquisa cognitiva do Azure usando C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definir regras

As regras de mapeamento aderem à especificação de filtro de sinônimos de software livre do Apache Solr, descritas neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). O `solr` formato dá suporte a dois tipos de regras:

+ equivalência (em que os termos são substituições iguais na consulta)

+ mapeamentos explícitos (em que os termos são mapeados para um termo explícito antes da consulta)

Cada regra deve ser delimitada pelo caractere de nova linha ( `\n` ). Você pode definir até 5.000 regras por mapa de sinônimos em um serviço gratuito e 20.000 regras por mapa em outras camadas. Cada regra pode ter até 20 expansões (ou itens em uma regra). Para obter mais informações, consulte [os limites de sinônimo](search-limits-quotas-capacity.md#synonym-limits).

Analisadores de consulta serão minúsculos quaisquer termos de maiúsculas ou minúsculas, mas se você quiser preservar caracteres especiais na cadeia de caracteres, como uma vírgula ou um traço, adicione os caracteres de escape apropriados ao criar o mapa de sinônimos.

### <a name="equivalency-rules"></a>Regras de equivalência

As regras para termos equivalentes são delimitadas por vírgula na mesma regra. No primeiro exemplo, uma consulta em `USA` será expandida para `USA` ou `"United States"` ou `"United States of America"` . Observe que, se você quiser corresponder a uma frase, a consulta em si deverá ser uma consulta de frase entre aspas.

No caso de equivalência, uma consulta para `dog` expandirá a consulta para também incluir `puppy` e `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Mapeamento explícito

As regras para um mapeamento explícito são indicadas por uma seta `=>` . Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponde ao lado esquerdo de `=>` será substituída pelas alternativas no lado direito no momento da consulta.

No caso explícito, uma consulta para `Washington` , `Wash.` ou `WA` será reescrita como `WA` , e o mecanismo de consulta só procurará correspondências no termo `WA` . O mapeamento explícito só se aplica à direção especificada e não reescreve a consulta `WA` `Washington` nesse caso.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Caracteres especiais de escape

Os sinônimos são analisados durante o processamento da consulta. Se você precisar definir sinônimos que contenham vírgulas ou outros caracteres especiais, poderá escapar com uma barra invertida, como neste exemplo:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Como a barra invertida é, por si só, um caractere especial em outras linguagens, como JSON e C#, você provavelmente precisará fazer o escape duplo. Por exemplo, o JSON enviado para a API REST para o mapa de sinônimos acima ficaria assim:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Carregar e gerenciar mapas de sinônimos

Conforme mencionado anteriormente, você pode criar ou atualizar um mapa de sinônimos sem interromper as cargas de trabalho de consulta e indexação. Um mapa de sinônimos é um objeto autônomo (como índices ou fontes de dados) e, desde que nenhum campo esteja usando, as atualizações não farão com que a indexação ou as consultas falhem. No entanto, depois de adicionar um mapa de sinônimos a uma definição de campo, se você excluir um mapa de sinônimo, qualquer consulta que inclua os campos em questão falhará com um erro 404.

Criar, atualizar e excluir um mapa de sinônimos sempre é uma operação de documento inteiro, o que significa que você não pode atualizar ou excluir partes do mapa de sinônimos de forma incremental. Atualizar até mesmo uma única regra requer um recarregamento.

## <a name="assign-synonyms-to-fields"></a>Atribuir sinônimos a campos

Depois de carregar um mapa de sinônimos, você pode habilitar os sinônimos em campos do tipo `Edm.String` ou `Collection(Edm.String)` , em campos com `"searchable":true` . Como observado, uma definição de campo pode usar apenas um mapa de sinônimos.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Consulta em campos equivalentes ou mapeados

Adicionar sinônimos não impõe novos requisitos na construção da consulta. Você pode emitir consultas de termo e frase da mesma forma como fazia antes da adição de sinônimos. A única diferença é que, se um termo de consulta existir no mapa de sinônimos, o mecanismo de consulta expandirá ou regravará o termo ou frase, dependendo da regra.

## <a name="how-synonyms-are-used-during-query-execution"></a>Como os sinônimos são usados durante a execução da consulta

Sinônimos são uma técnica de expansão de consulta que complementa o conteúdo de um índice com termos equivalentes, mas apenas para campos que têm uma atribuição de sinônimo. Se uma consulta com escopo de campo *excluir* um campo habilitado para sinônimo, você não verá as correspondências do mapa de sinônimos.

Para campos habilitados para sinônimos, os sinônimos estão sujeitos à mesma análise de texto que o campo associado. Por exemplo, se um campo for analisado usando o analisador Lucene padrão, os termos de sinônimo também estarão sujeitos ao analisador Lucene padrão no momento da consulta. Se você quiser preservar a pontuação, como pontos ou traços, no termo sinônimo, aplique um analisador de preservação de conteúdo no campo.

Internamente, o recurso de sinônimos regrava a consulta original com sinônimos com o operador OR. Por esse motivo, os perfis de destaque e pontuação acessados tratam o termo original e os sinônimos como equivalentes.

Os sinônimos se aplicam apenas a consultas de texto de forma livre e não têm suporte para filtros, facetas, preenchimento automático ou sugestões. Preenchimento automático e sugestões são baseados apenas no termo original; as correspondências de sinônimo não aparecem na resposta.

Expansões de sinônimo não se aplicam a termos de pesquisa de curinga; prefixo, lógica difusa e termos de regex não são expandidos.

Se for necessário fazer uma única consulta que aplique expansão de sinônimos e curingas, regex ou pesquisas difusas, você poderá combinar as consultas usando a sintaxe OR. Por exemplo, para combinar sinônimos com curingas para uma sintaxe de consulta simples, o termo seria `<query> | <query>*`.

Se houver um índice em um ambiente de desenvolvimento (não produção), experimente um dicionário pequeno para ver como a adição de sinônimos altera a experiência de pesquisa, incluindo o impacto nos perfis de pontuação, o realce de acesso e as sugestões.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um mapa de sinônimos (API REST)](/rest/api/searchservice/create-synonym-map)