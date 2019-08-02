---
title: Migrar para V3 - API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como migrar da V2 para a V3 da API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 9ce9dc330ec1b0c32ce045437fadb5d8c191529e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595234"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Migração da API de Tradução de Texto V2 para V3

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3.
> 
> O Hub do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).  

A equipe do Microsoft Translator lançou a versão 3 (V3) da API de Tradução de Texto. Esta versão inclui novos recursos, métodos preteridos e um novo formato para enviar e receber dados do Serviço do Microsoft Translator. Este documento apresenta informações sobre como alterar os aplicativos para usar a V3. 

O fim deste documento contém links úteis para saber mais.

## <a name="summary-of-features"></a>Resumo dos recursos

* Sem rastreamento – na V3, Sem Rastreamento se aplica a todas as camadas de preços no portal do Azure. Esse recurso significa que nenhum texto enviado para a API V3 será salvo pela Microsoft.
* JSON – XML é substituído por JSON. Todos os dados enviados para o serviço e recebidos do serviço estão no formato JSON.
* Vários idiomas de destino em uma única solicitação – o método Translate aceita vários idiomas de destino para tradução em uma única solicitação. Por exemplo, uma única solicitação pode ser 'de' inglês e 'para' alemão, espanhol e japonês ou qualquer outro grupo de idiomas.
* Dicionário bilíngue – um método de dicionário bilíngue foi adicionado à API. Esse método inclui 'pesquisa' e 'exemplos'.
* Transliterate – um método transliterate foi adicionado à API. Esse método converterá palavras e frases em um script (ex.: árabe) em outro script (ex.: latim).
* Idiomas – um novo método de 'idiomas' fornece informações de idioma, no formato JSON, para uso com os métodos 'translate', 'dictionary' e 'transliterate'.
* Não familiarizado com a Tradução – novas funcionalidades foram adicionadas ao método 'translate' para dar suporte a alguns dos recursos que estavam na API V2 como métodos separados. Um exemplo é TranslateArray.
* Método Speak – a funcionalidade de conversão de texto em fala não é mais compatível com a API do Microsoft Translator. A funcionalidade de texto para fala está disponível no [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

A seguinte lista de métodos V2 e V3 identifica as APIs e os métodos da V3 que fornecerão a funcionalidade disponibilizada com a V2.

| Método da API V2   | Compatibilidade da API V3 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Idiomas](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Idiomas](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Serviço de fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | Não há mais suporte para o recurso       |
| `AddTranslationArray`    | Não há mais suporte para o recurso          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Não há mais suporte para o recurso         |
| `GetTranslationsArray`      | Não há mais suporte para o recurso         |

## <a name="move-to-json-format"></a>Mover para o formato JSON

A Tradução de Texto do Microsoft Translator V2 aceitava e retornava dados em formato XML. Na V3, todos os dados enviados e recebidos usando a API está em formato JSON. XML não será mais aceito nem retornado na V3.

Esta alteração afetará vários aspectos de um aplicativo escrito para a API de Tradução de Texto V2. Por exemplo: A API de Idiomas retorna informações de idioma para tradução de texto, transliteração e os dois métodos de dicionário. Você pode solicitar todas as informações de idioma para todos os métodos em uma chamada ou solicitá-los individualmente.

O método languages não exige autenticação; ao clicar no link a seguir, você pode ver todas as informações de idioma para V3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de autenticação

A chave de autenticação que você está usando para a V2 será aceita para V3. Você não precisará obter uma nova assinatura. Você poderá combinar V2 e V3 em seus aplicativos durante o período de migração de um ano, tornando mais fácil para você liberar novas versões enquanto ainda está migrando de V2-XML para V3-JSON.

## <a name="pricing-model"></a>Modelo de preços

O preço do Microsoft Translator V3 é definido da mesma maneira que o da V2; por caractere, incluindo espaços. Os novos recursos na V3 fazem algumas alterações em que caracteres contam para cobrança.

| Método da V3   | Caracteres contados para cobrança |
|:----------- |:-------------|
| `Languages`     | Nenhum caractere enviado, nenhum contado, nenhum encargo.          |
| `Translate`     | A contagem é baseada em quantos caracteres são enviado para tradução e para quantos idiomas os caracteres são traduzidos. Cinquenta caracteres enviados e cinco idiomas solicitados serão 50 x 5.           |
| `Transliterate`     | O número de caracteres enviados para transliteração é contado.         |
| `Dictionary lookup & example`     | O número de caracteres enviados para pesquisa no Dicionário e exemplos é contato.         |
| `BreakSentence`     | Sem Encargos.       |
| `Detect`     | Sem Encargos.      |

## <a name="v3-end-points"></a>Pontos de extremidade da V3

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Métodos de traduções de texto da API V3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibilidade e personalização

> [!NOTE]
> 
> O Hub do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).   

Por padrão, o Microsoft Translator V3 usa tradução automática neural. Como tal, não pode ser utilizado com o Hub do Microsoft Translator. O Hub do Translator dá suporte somente para tradução automática de estatística herdada. A personalização para tradução neural agora está disponível usando o Tradutor Personalizado. [Saiba mais sobre como personalizar a tradução automática neural](custom-translator/overview.md)

A tradução neural com a API de texto V3 não dá suporte ao uso de categorias padrão (SMT, fala, texto, generalnn).

| |Ponto de extremidade|    Conformidade de processador do RGPD|  Usar o Hub do Tradutor| Usar o Tradutor Personalizado (Versão Prévia)|
|:-----|:-----|:-----|:-----|:-----|
|API de Tradução de Texto Versão 2| api.microsofttranslator.com|    Não  |Sim    |Não|
|API de Tradução de Texto Versão 3| api.cognitive.microsofttranslator.com|  Sim|    Não| Sim|

**API de Tradução de Texto Versão 3**
* Está geralmente disponível e tem suporte completo.
* É compatível com RGPD como um processador e cumpre todos os requisitos de ISO 20001 e 20018, bem como os requisitos de certificação SOC 3. 
* Permite invocar os sistemas de tradução de rede neural que você personalizou com o Tradutor Personalizado (Versão Prévia), o novo recurso de personalização do Tradutor NMT. 
* Não fornece acesso a sistemas de tradução personalizada criados usando o Hub do Microsoft Translator.

Você está usando a Versão 3 da API de Tradução de Texto caso esteja usando o ponto de extremidade api.cognitive.microsofttranslator.com.

**API de Tradução de Texto Versão 2**
* Não atende a todos os requisitos de certificação de SOC 3 e ISO 20001,20018. 
* Não permite invocar os sistemas de tradução de rede neural que você personalizou com o recurso de personalização do Tradutor.
* Fornece acesso a sistemas de tradução personalizada criados usando o Hub do Microsoft Translator.
* Você está usando a Versão 2 da API de Tradução de Texto caso esteja usando o ponto de extremidade api.microsofttranslator.com.

Nenhuma versão da API do Tradutor cria um registro de suas traduções. Suas traduções nunca são compartilhadas com ninguém. Mais informações podem ser encontradas na página da Web [Tradutor sem rastreamento](https://www.aka.ms/NoTrace).

## <a name="links"></a>Links

* [Política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informações legais do Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Termos de serviços online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibir a documentação da V3.0](reference/v3-0-reference.md)
