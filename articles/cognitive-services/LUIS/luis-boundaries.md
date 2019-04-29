---
title: limites
titleSuffix: Language Understanding - Azure Cognitive Services
description: Este artigo contém os limites conhecidos do LUIS (Reconhecimento vocal) dos Serviços Cognitivos do Azure. O LUIS tem várias áreas de limite. O limite de modelo controla intenções, entidades e recursos no LUIS. Limites de cota com base no tipo de chave. A combinação de teclado controla o site do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/18/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 58f6d6cf8bf16f7c35bab35a69cfcdf8759f66ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814043"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para seu modelo e suas chaves do LUIS
O LUIS tem várias áreas de limite. A primeira é o [limite de modelo](#model-boundaries), que controla intenções, entidades e recursos no LUIS. A segunda área é [limites de cota](#key-limits) com base no tipo de chave. A terceira área de limites é a [combinação de teclado](#keyboard-controls) para controlar o site do LUIS. Uma quarta área é o [mapeamento de região do mundo](luis-reference-regions.md) entre o site de criação do LUIS e as APIs do [ponto de extremidade](luis-glossary.md#endpoint) do LUIS. 


## <a name="model-boundaries"></a>Limites de modelo

Se seu aplicativo exceder os limites e os perímetros do modelo LUIS, considere usar um aplicativo de [expedição LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou um [contêiner LUIS](luis-container-howto.md). 

|Área|Limite|
|--|:--|
| [Nome do aplicativo][luis-get-started-create-app] | *Máximo de caracteres padrão |
| [Teste de lote][batch-testing]| 10 conjuntos de dados, 1000 declarações por conjunto de dados|
| Lista explícita | 50 por aplicativo|
| [Intenções][intents]|500 por aplicativo: 499 intenções personalizadas e a intenção _Nenhuma_ obrigatória.<br>O aplicativo [baseado em expedição](https://aka.ms/dispatch-tool) tem 500 fontes de expedição correspondentes.|
| [Entidades de lista](./luis-concept-entity-types.md) | Pai: 50, filho: 20.000 itens. O nome Canonical é o máximo de caracteres padrão* Valores de sinônimos não têm restrição de comprimento. |
| [Entidades com aprendizado de máquina](./luis-concept-entity-types.md):<br> Composição,<br>  Hierárquico<br> Simples|Um limite de 100 entidades pai (não incluindo filhos hierárquicos) ou 330 entidades (incluindo filhos hierárquicos), o que limitar as ocorrências de usuário primeiro.<br><br>Um exemplo de hierarquia são 30 hierarquias, cada uma com 10 filhos.  Os filhos consumirão 300 no total e os elementos da hierarquia consumirão as 30 restantes. |
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicativo.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 entidades Pattern.any por padrão<br>Máximo de 2 textos opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicativo, 3 entidades pattern.any por padrão |
| [Lista de frases][phrase-list]|10 listas de frases, 5 mil itens por lista|
| [Entidades predefinidas](./luis-prebuilt-entities.md) | nenhum limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres, no máximo, por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicativo. 10 funções por entidade|
| [Declaração][utterances] | 500 caracteres|
| [Declarações][utterances] | 15.000 por aplicativo - não há nenhum limite no número de declarações por intenção|
| [Versões](luis-concept-version.md)| nenhum limite |
| [Nome da versão][luis-how-to-manage-versions] | 10 caracteres restritos a alfanuméricos e ponto (.) |

*O máximo de caracteres padrão é 50 caracteres. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Nomeação de objeto

Não use os seguintes caracteres em que os nomes a seguir.

|Object|Excluir caracteres|
|--|--|
|Intenção, entidade e nomes de função|`:`<br>`$`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Uso de chave

O Reconhecimento Vocal tem chaves separadas: um tipo para criação e outro para consulta do ponto de extremidade de previsão. Para saber mais sobre as diferenças entre tipos de chaves, confira [Chaves de ponto de extremidade de previsão de criação e consulta no LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Limites de chave

A chave de criação tem diferentes limites para criação e ponto de extremidade. A chave de ponto de extremidade de serviço de LUIS é válida apenas para consultas de ponto de extremidade.


|Chave|Criação|Ponto de extremidade|Finalidade|
|--|--|--|--|
|Criação/Início de Reconhecimento Vocal|1 milhão/mês, 5/segundo|1 mil/mês, 5/segundo|Criando seu aplicativo LUIS|
|[Assinatura][pricing] de Reconhecimento Vocal – F0 – Camada gratuita |inválido|10 mil/mês, 5/segundo|Consultando seu ponto de extremidade LUIS|
|[Assinatura][pricing] de Reconhecimento Vocal - S0 – camada Básica|inválido|50/segundo|Consultando seu ponto de extremidade LUIS|
|[Assinatura][pricing] do Serviço Cognitivo – S0 – camada Standard|inválido|50/segundo|Consultando seu ponto de extremidade LUIS|
|[Integração de análise de sentimento](luis-how-to-publish-app.md#enable-sentiment-analysis)|inválido|sem encargos|Adicionando informações de sentimento incluindo extração de dados de frase de chave |
|Integração de fala|inválido|US$ 5,50/1 mil solicitações de ponto de extremidade|Converter declaração falada em declaração de texto e retornar resultados do LUIS|

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada de teclado | DESCRIÇÃO | 
|--|--|
|Control+E|alterna entre tokens e entidades na lista de declarações|

## <a name="website-sign-in-time-period"></a>Período de tempo de entrada no site

O acesso de entrada é para **60 minutos**. Após esse período, você obterá esse erro. Você precisa entrar novamente.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
