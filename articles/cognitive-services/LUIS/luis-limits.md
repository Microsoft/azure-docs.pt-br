---
title: Limites-LUIS
description: Este artigo contém os limites conhecidos do LUIS (Reconhecimento vocal) dos Serviços Cognitivos do Azure. LUIS tem várias áreas limites. O limite de modelo controla as intenções, as entidades e os recursos no LUIS. Limites de cota com base no tipo de chave. A combinação de teclado controla o site do LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976785"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limites para o modelo e as chaves do LUIS
LUIS tem várias áreas de limite. O primeiro é o [limite do modelo](#model-limits), que controla as intenções, as entidades e os recursos no Luis. A segunda área é [limites de cota](#key-limits) com base no tipo de chave. Uma terceira área de limites é a [combinação de teclado](#keyboard-controls) para controlar o site Luis. Uma quarta área é o [mapeamento de região do mundo](luis-reference-regions.md) entre o site de criação do LUIS e as APIs do [ponto de extremidade](luis-glossary.md#endpoint) do LUIS.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limites de modelo

Se seu aplicativo exceder os limites do modelo LUIS, considere o uso de um aplicativo de [expedição Luis](luis-concept-enterprise.md#dispatch-tool-and-model) ou o uso de um [contêiner Luis](luis-container-howto.md).

|Área|Limite|
|--|:--|
| [Nome do aplicativo][luis-get-started-create-app] | *Máximo de caracteres padrão |
| Aplicativos| 500 aplicativos por recurso de criação do Azure |
| [Teste em lote][batch-testing]| 10 conjuntos de dados, 1000 declarações por conjunto de dados|
| Lista explícita | 50 por aplicativo|
| Entidades externas | sem limites |
| [Intenções][intents]|500 por aplicativo: 499 tentativas personalizadas e a intenção _nenhuma_ necessária.<br>O aplicativo [baseado em expedição](https://aka.ms/dispatch-tool) tem fontes de expedição 500 correspondentes.|
| [Listar entidades](./luis-concept-entity-types.md) | Pai: 50, filho: 20 mil itens. O nome Canonical é o máximo de caracteres padrão* Valores de sinônimos não têm restrição de comprimento. |
| [entidades de aprendizado de máquina + funções](./luis-concept-entity-types.md):<br> Spot<br>único<br>função de entidade|Um limite de entidades pai 100 ou de 330 entidades, o que limitará o usuário primeiro. Uma função conta como uma entidade com a finalidade desse limite. Um exemplo é uma composição com uma entidade simples, que tem duas funções: 1 composição + 1 simples + 2 funções = 4 das entidades 330.<br>As subentidades podem ser aninhadas até 5 níveis, com um máximo de 10 filhos por nível.|
|Modelo como um recurso| Número máximo de modelos que podem ser usados como um recurso para um modelo específico para ter 10 modelos. O número máximo de listas de frases usadas como um recurso para um modelo específico ter 10 listas de frases.|
| [Visualização-entidades de lista dinâmica](./luis-migration-api-v3.md)|2 listas de ~ 1K por solicitação de ponto de extremidade de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicativo.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 entidades Pattern.any por padrão<br>Máximo de 2 textos opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicativo, 3 entidades pattern.any por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. 10 listas de frases globais devido ao modelo como um limite de recursos. A lista de frases não intercambiáveis tem o máximo de 5.000 frases. A lista de frases intercambiáveis tem no máximo 50.000 frases. Número máximo de frases totais por aplicativo de 500.000 frases.|
| [Entidades predefinidas](./howto-add-prebuilt-models.md) | nenhum limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres, no máximo, por padrão de entidade de expressão regular|
| [Funções](./luis-concept-entity-types.md)|300 funções por aplicativo. 10 funções por entidade|
| [Enunciado][utterances] | 500 caracteres<br><br>Se você tiver um texto maior do que esse limite de caracteres, será necessário segmentar o expressão antes de inserir para LUIS e você receberá respostas de intenção individuais por segmento. Há quebras óbvias com as quais você pode trabalhar, como marcas de Pontuação e pausas longas em fala.|
| [Exemplos de expressão][utterances] | 15.000 por aplicativo – não há limite para o número de declarações por tentativa<br><br>Se você precisar treinar o aplicativo com mais exemplos, use uma abordagem de modelo de [expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) . Você treina aplicativos LUIS individuais (conhecidos como aplicativos filho para o aplicativo de expedição pai) com uma ou mais intenções e, em seguida, treina um aplicativo de expedição que faz amostras de cada declarações do aplicativo LUIS filho para direcionar a solicitação de previsão para o aplicativo filho correto. |
| [Versões](./luis-concept-app-iteration.md)| 100 versões por aplicativo |
| [Nome da versão][luis-how-to-manage-versions] | 128 caracteres |

*O máximo de caracteres padrão é 50 caracteres.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Exclusividade do nome

Os nomes de objeto devem ser exclusivos quando comparados a outros objetos do mesmo nível.

|Objetos|Restrições|
|--|--|
|Intenção, entidade|Todos os nomes de intenção e entidade devem ser exclusivos em uma versão de um aplicativo.|
|Componentes de entidade ML|Todos os componentes de entidade de aprendizado de máquina (entidades filho) devem ser exclusivos, dentro dessa entidade para componentes no mesmo nível.|
|Recursos | Todos os recursos nomeados, como listas de frases, devem ser exclusivos em uma versão de um aplicativo.|
|Funções de entidade|Todas as funções em um componente entidade ou entidade devem ser exclusivas quando estiverem no mesmo nível de entidade (pai, filho, neto, etc.).|

## <a name="object-naming"></a>Nomenclatura de objeto

Não use os seguintes caracteres nos nomes a seguir.

|Objeto|Excluir caracteres|
|--|--|
|Nomes de intenção, entidade e função|`:`<br>`$` <br> `&`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Limites e uso de recursos

O entendimento da linguagem tem recursos separados, um tipo para a criação e um tipo para consultar o ponto de extremidade de previsão. Para saber mais sobre as diferenças entre tipos de chaves, confira [Chaves de ponto de extremidade de previsão de criação e consulta no LUIS](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limites de recursos de criação

Use o _tipo_, `LUIS.Authoring` , ao filtrar recursos no portal do Azure. LUIS limita 500 aplicativos por recurso de criação do Azure.

|Recurso de criação|Criação de TPS|
|--|--|
|Inicial|1 milhão/mês, 5/segundo|
|F0-camada gratuita |1 milhão/mês, 5/segundo|

* TPS = transações por segundo

[Saiba mais sobre preços.][pricing]

### <a name="query-prediction-resource-limits"></a>Limites de recursos de previsão de consulta

Use o _tipo_, `LUIS` , ao filtrar recursos no portal do Azure. O recurso de ponto de extremidade de previsão de consulta LUIS, usado no tempo de execução, só é válido para consultas de ponto de extremidade.

|Recurso de previsão de consulta|Consulta TPS|
|--|--|
|F0-camada gratuita |10 mil/mês, 5/segundo|
|S0 – camada padrão|50/segundo|

### <a name="sentiment-analysis"></a>Análise de sentimento

A [integração da análise de sentimentos](luis-how-to-publish-app.md#enable-sentiment-analysis), que fornece informações de sentimentos, é fornecida sem a necessidade de outro recurso do Azure.

### <a name="speech-integration"></a>Integração de fala

A [integração de fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fornece 1000 solicitações de ponto de extremidade por custo unitário.

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada por teclado | Descrição |
|--|--|
|Control+E|alterna entre tokens e entidades na lista de declarações|

## <a name="website-sign-in-time-period"></a>Período de tempo de entrada do site

O acesso de entrada é para **60 minutos**. Após esse período, você obterá esse erro. Você precisa entrar novamente.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
