---
title: Habilidade cognitiva de Extração de Frases-Chave
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registro, retorna uma lista de frases-chave em um pipeline de enriquecimento de IA no Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547825"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Habilidade cognitiva de Extração de Frases-Chave

A habilidade de **Extração de Frases-Chave** avalia o texto não estruturado e para cada registro, retorna uma lista de frases-chave. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](../cognitive-services/text-analytics/overview.md) nos Serviços Cognitivos.

Esse recurso é útil se você precisar identificar rapidamente os principais pontos de estratégias no registro. Por exemplo, texto de entrada especificado “A comida estava deliciosa e a equipe foi maravilhosa”, o serviço retorna “comida” e “equipe maravilhosa”.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-la para o extrator de frases-chave, considere o uso de [habilidade de Texto Dividido](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas | Descrição |
|---------------------|-------------|
| `defaultLanguageCode` | (opcional) O código de idioma a ser aplicado a documentos que não especifica explicitamente o idioma.  Se o código de idioma padrão não for especificado, em inglês (en) será usado como o código de idioma padrão. <br/> Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | (opcional) O número máximo de frases-chave para produzir. |
| `modelVersion`   | Adicional A versão do modelo a ser usada ao chamar o serviço de Análise de Texto. O padrão será o mais recente disponível quando não for especificado. Recomendamos que você não especifique esse valor, a menos que seja absolutamente necessário. Consulte [controle de versão de modelo no API de análise de texto](../cognitive-services/text-analytics/concepts/model-versioning.md) para obter mais detalhes. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Entrada  | Descrição |
|--------------------|-------------|
| `text` | O texto a ser analisado.|
| `languageCode`    |  Uma cadeia de caracteres que indica o idioma dos registros. Se esse parâmetro não for especificado, o código de idioma padrão será usado para analisar os registros. <br/>Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Saídas de habilidades

| Saída     | Descrição |
|--------------------|-------------|
| `keyPhrases` | Uma lista de frases-chave extraídas do texto de entrada. As frases-chave são retornadas em ordem de importância. |


##  <a name="sample-definition"></a>Definição de exemplo

Considere um registro SQL que tenha os seguintes campos:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Em seguida, sua definição de habilidade pode ser assim:

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Saída de exemplo

Para o exemplo acima, a saída de suas habilidades será gravada em um novo nó na árvore aprimorada chamada "Document/myKeyPhrases", já que isso é o `targetName` que especificamos. Se você não especificar um `targetName` , então ele seria "documento/prefrases".

#### <a name="documentmykeyphrases"></a>documento/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Você pode usar "Document/myKeyPhrases" como entrada em outras habilidades ou como uma fonte de um [mapeamento de campo de saída](cognitive-search-output-field-mapping.md).

## <a name="warnings"></a>Warnings
Se você fornecer um código de idioma sem suporte, um aviso será gerado e as frases-chave não serão extraídas.
Se o texto estiver vazio, um aviso será gerado.
Se o texto for maior que 50.000 caracteres, somente os primeiros 50.000 caracteres serão analisados e um aviso será emitido.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como definir mapeamentos de campos de saída](cognitive-search-output-field-mapping.md)