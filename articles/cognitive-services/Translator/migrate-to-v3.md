---
title: Migrar para v3-Translator
titleSuffix: Azure Cognitive Services
description: Este artigo fornece as etapas para ajudá-lo a migrar de v2 para V3 do tradutor de serviços cognitivas do Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 13c4d39284fad293c945f8b7e31076dccee84fda
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896826"
---
# <a name="translator-v2-to-v3-migration"></a>Conversor v2 para migração v3

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3. O v2 será desativado em 24 de maio de 2021. 

A equipe do Microsoft Translator lançou a versão 3 (v3) do tradutor. Esta versão inclui novos recursos, métodos preteridos e um novo formato para enviar e receber dados do Serviço do Microsoft Translator. Este documento apresenta informações sobre como alterar os aplicativos para usar a V3. 

O fim deste documento contém links úteis para saber mais.

## <a name="summary-of-features"></a>Resumo dos recursos

* Sem rastreamento – na V3, Sem Rastreamento se aplica a todas as camadas de preços no portal do Azure. Esse recurso significa que nenhum texto enviado para a API V3 será salvo pela Microsoft.
* JSON – XML é substituído por JSON. Todos os dados enviados para o serviço e recebidos do serviço estão no formato JSON.
* Vários idiomas de destino em uma única solicitação – o método translate aceita vários idiomas ' to ' para tradução em uma única solicitação. Por exemplo, uma única solicitação pode ser ' de ' em inglês e ' para ' alemão, espanhol e japonês ou qualquer outro grupo de idiomas.
* Dicionário bilíngue – um método de dicionário bilíngue foi adicionado à API. Esse método inclui ' Lookup ' e ' examples '.
* Transliterate – um método transliterate foi adicionado à API. Esse método converterá palavras e frases em um script (ex.: árabe) em outro script (ex.: latim).
* Idiomas – um novo método ' Languages ' fornece informações de idioma, no formato JSON, para uso com os métodos ' translate ', ' Dictionary ' e ' transliterate '.
* Novo para traduzir-novos recursos foram adicionados ao método ' translate ' para dar suporte a alguns dos recursos que estavam na API v2 como métodos separados. Um exemplo é TranslateArray.
* Método de fala – não há mais suporte para a funcionalidade de conversão de texto em fala no Microsoft Translator. A funcionalidade de texto para fala está disponível no [Microsoft Speech Service](../speech-service/text-to-speech.md).

A seguinte lista de métodos V2 e V3 identifica as APIs e os métodos da V3 que fornecerão a funcionalidade disponibilizada com a V2.

| Método da API V2   | Compatibilidade da API V3 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Linguagens](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Linguagens](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de fala da Microsoft](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Serviço de fala da Microsoft](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | Não há mais suporte para o recurso       |
| `AddTranslationArray`    | Não há mais suporte para o recurso          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Não há mais suporte para o recurso         |
| `GetTranslationsArray`      | Não há mais suporte para o recurso         |

## <a name="move-to-json-format"></a>Mover para o formato JSON

A tradução do Microsoft Translator v2 aceitou e retornou dados em formato XML. Na V3, todos os dados enviados e recebidos usando a API está em formato JSON. XML não será mais aceito nem retornado na V3.

Esta alteração afetará vários aspectos de um aplicativo escrito para a API de Tradução de Texto V2. Como exemplo: uma API de Idiomas retorna informações de idioma para tradução de texto, transliteração e os dois métodos de dicionário. Você pode solicitar todas as informações de idioma para todos os métodos em uma chamada ou solicitá-los individualmente.

O método languages não exige autenticação; ao clicar no link a seguir, você pode ver todas as informações de idioma para V3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, dicionário, transliteração](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

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

| Versão | Ponto de Extremidade | Conformidade de processador do RGPD | Usar o Hub do Tradutor | Usar o Tradutor Personalizado (Versão Prévia) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Tradutor versão 2|    api.microsofttranslator.com|    Não    |Sim    |Não|
|Tradutor versão 3|    api.cognitive.microsofttranslator.com|    Sim|    Não|    Sim|

**Tradutor versão 3**
* Está geralmente disponível e tem suporte completo.
* É compatível com RGPD como um processador e cumpre todos os requisitos de ISO 20001 e 20018, bem como os requisitos de certificação SOC 3. 
* Permite invocar os sistemas de tradução de rede neural que você personalizou com o Tradutor Personalizado (Versão Prévia), o novo recurso de personalização do Tradutor NMT. 
* Não fornece acesso a sistemas de tradução personalizada criados usando o Hub do Microsoft Translator.

Você está usando a versão 3 do tradutor se estiver usando o ponto de extremidade api.cognitive.microsofttranslator.com.

**Tradutor versão 2**
* Não atende a todos os requisitos de certificação de SOC 3 e ISO 20001,20018. 
* Não permite invocar os sistemas de tradução de rede neural que você personalizou com o recurso de personalização do Tradutor.
* Fornece acesso a sistemas de tradução personalizada criados usando o Hub do Microsoft Translator.
* Você está usando a versão 2 do tradutor se estiver usando o ponto de extremidade api.microsofttranslator.com.

Nenhuma versão do tradutor cria um registro de suas traduções. Suas traduções nunca são compartilhadas com ninguém. Mais informações podem ser encontradas na página da Web [Tradutor sem rastreamento](https://www.aka.ms/NoTrace).

## <a name="links"></a>Links

* [Política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informações legais do Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Termos de serviços online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibir a documentação da V3.0](reference/v3-0-reference.md)
