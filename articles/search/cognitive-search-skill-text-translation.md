---
title: Habilidade de pesquisa cognitiva de tradução de texto-Azure Search
description: Avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado em um pipeline de enriquecimento de Azure Search.
services: search
manager: briansmi
author: careyjmac
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cmacdo
ms.custom: seodec2018
ms.openlocfilehash: 0268a16ecc07d578e1e3dc2b15ad9208509c2a8c
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68351442"
---
#   <a name="text-translation-cognitive-skill-preview"></a>Habilidade cognitiva de tradução de texto (visualização)

A habilidade de **conversão de texto** avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado. Essa habilidade usa o [API de tradução de texto v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) disponível em serviços cognitivas.  Enquanto estiver disponível atualmente na versão mais recente da API de Azure Search geralmente disponível, é considerado em visualização e deve ser tratado como tal até ser anunciado como disponível para o público geral.

Esse recurso é útil se você espera que seus documentos não estejam em um idioma, caso em que você pode normalizar o texto para uma única linguagem antes de indexar a pesquisa, traduzindo-o.  Ele também é útil para casos de uso de localização, onde talvez você queira ter cópias do mesmo texto disponíveis em vários idiomas.

O [API de tradução de texto v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) é um serviço de cognitiva não regional, o que significa que seus dados não têm garantia de permanecer na mesma região que o Azure Search ou o recurso de serviços cognitivas anexados.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. TranslationSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para a habilidade de tradução de texto, considere usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| defaultToLanguageCode | Necessária O código de idioma para converter documentos em para documentos que não especificam o idioma explicitamente. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | Adicional O código de idioma para converter documentos do para documentos que não especificam o idioma explicitamente.  Se o defaultFromLanguageCode não for especificado, a detecção automática de idioma fornecida pelo API de Tradução de Texto será usada para determinar o idioma de. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | Adicional O código de idioma para converter documentos de quando nem a entrada fromLanguageCode nem o parâmetro defaultFromLanguageCode são fornecidos, e a detecção automática de idioma não é bem-sucedida.  Se o idioma suggestedFrom não for especificado, o inglês (EN) será usado como a linguagem suggestedFrom. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Descrição |
|--------------------|-------------|
| texto | O texto a ser traduzido.|
| toLanguageCode    | Uma cadeia de caracteres que indica o idioma no qual o texto deve ser convertido. Se essa entrada não for especificada, o defaultToLanguageCode será usado para traduzir o texto. <br/>Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Uma cadeia de caracteres que indica o idioma atual do texto. Se esse parâmetro não for especificado, o defaultFromLanguageCode (ou a detecção automática de idioma se o defaultFromLanguageCode não for fornecido) será usado para traduzir o texto. <br/>Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| translatedText | O resultado da cadeia de caracteres da conversão de texto do translatedFromLanguageCode para o translatedToLanguageCode.|
| translatedToLanguageCode  | Uma cadeia de caracteres que indica o código de idioma para o qual o texto foi traduzido. Útil se você estiver traduzindo para vários idiomas e quiser ser capaz de controlar qual texto é o idioma.|
| translatedFromLanguageCode    | Uma cadeia de caracteres que indica o código de idioma do qual o texto foi traduzido. Útil se você optou pela opção de detecção automática de idioma, pois essa saída fornecerá o resultado dessa detecção.|

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Erros e avisos
Se você fornecer um código de idioma sem suporte para o idioma de ou para, um erro será gerado e o texto não será traduzido.
Se o texto estiver vazio, um aviso será gerado.
Se o texto tiver mais de 50.000 caracteres, somente os primeiros 50.000 caracteres serão traduzidos e um aviso será emitido.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
