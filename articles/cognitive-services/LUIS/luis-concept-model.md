---
title: Criar com modelos-LUIS
description: A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de mais de uma maneira.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316504"
---
# <a name="design-with-intent-and-entity-models"></a>Design com intenção e modelos de entidade

A compreensão da linguagem fornece dois tipos de modelos para você definir o esquema do aplicativo. O esquema do aplicativo determina quais informações você recebe da previsão de um novo usuário expressão.

O esquema do aplicativo é criado a partir de modelos criados usando o [ensinando de máquina](#authoring-uses-machine-teaching):
* [Tentativas](#intents-classify-utterances) de classificação do usuário declarações
* [Entidades](#entities-extract-data) extraem dados do expressão

## <a name="authoring-uses-machine-teaching"></a>A criação usa o ensino de máquina

A metodologia de ensino de máquina do LUIS permite que você ensine facilmente conceitos a um computador. Entender o _aprendizado de máquina_ não é necessário para usar o Luis. Em vez disso, você como professor, comunica um conceito ao LUIS fornecendo exemplos do conceito e explicando como um conceito deve ser modelado usando outros conceitos relacionados. Você, como professor, também pode melhorar o modelo de LUIS interativamente identificando e corrigindo erros de previsão.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Enunciados de classificação de intenções

Uma intenção classifica o exemplo declarações para ensinar LUIS sobre a intenção. Os declarações de exemplo em uma intenção são usados como exemplos positivos de expressão. Esses mesmos declarações são usados como exemplos negativos em todas as outras intenções.

Considere um aplicativo que precisa determinar a intenção de um usuário para solicitar um livro e um aplicativo que precise do endereço de envio para o cliente. Este aplicativo tem duas intenções: `OrderBook` e `ShippingLocation` .

O expressão a seguir é um **exemplo positivo** para a `OrderBook` intenção e um **exemplo negativo** para as `ShippingLocation` tentativas e `None` :

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Dados de extração de entidades

Uma entidade representa uma unidade de dados que você deseja extrair do expressão. Uma entidade de aprendizado de máquina é uma entidade de nível superior que contém subentidades, que também são entidades de aprendizado de máquina.

Um exemplo de uma entidade de aprendizado de máquina é uma ordem para um tíquete de plano. Conceitualmente, essa é uma única transação com muitas unidades menores de dados, como data, hora, quantidade de estações, tipo de assento, como primeira classe, local de origem, local de destino e escolha de refeição.

## <a name="intents-versus-entities"></a>Tentativas versus entidades

Uma intenção é o resultado desejado de _todo_ o expressão, enquanto as entidades são partes dos dados extraídos do expressão. Geralmente, as intenções são vinculadas a ações, que o aplicativo cliente deve executar. As entidades são informações necessárias para executar esta ação. De uma perspectiva de programação, uma intenção dispararia uma chamada de método e as entidades seriam usadas como parâmetros para essa chamada de método.

Este expressão _deve_ ter uma intenção e _pode_ ter entidades:

`Buy an airline ticket from Seattle to Cairo`

Este expressão tem uma única intenção:

* Comprando um tíquete de plano

Este expressão _pode_ ter várias entidades:

* Locais de Seattle (origem) e Cairo (destino)
* A quantidade de um único tíquete

## <a name="entity-model-decomposition"></a>Decomposição de modelo de entidade

O LUIS dá suporte à _decomposição de modelo_ com as APIs de criação, dividindo um conceito em partes menores. Isso permite que você crie seus modelos com confiança em como as várias partes são construídas e previstas.

A decomposição do modelo tem as seguintes partes:

* [tentativas](#intents-classify-utterances)
    * [Features](#features)
* [entidades de machine learning](reference-entity-machine-learned-entity.md)
    * subentidades (também entidades de aprendizado de máquina)
        * [Features](#features)
            * [lista de frases](luis-concept-feature.md)
            * [entidades de aprendizagem não Machine-Learning](luis-concept-feature.md) , como [expressões regulares](reference-entity-regular-expression.md), [listas](reference-entity-list.md)e [entidades predefinidas](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Recursos

Um [recurso](luis-concept-feature.md) é uma característica ou atributo diferenciado dos dados que seu sistema observa. Os recursos de aprendizado de máquina fornecem indicações LUISs importantes para onde procurar coisas que irão distinguir um conceito. Elas são dicas que o LUIS pode usar, mas não as regras difíceis. Essas dicas são usadas em conjunto com os rótulos para localizar os dados.

## <a name="patterns"></a>Padrões

[Padrões](luis-concept-patterns.md) são projetados para melhorar a precisão quando várias declarações são muito semelhantes. Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais.

## <a name="extending-the-app-at-runtime"></a>Estendendo o aplicativo em tempo de execução

O esquema do aplicativo (modelos e recursos) é treinado e publicado no ponto de extremidade de previsão. Você pode [passar novas informações](schema-change-prediction-runtime.md), junto com o expressão do usuário, para o ponto de extremidade de previsão para aumentar a previsão.

## <a name="next-steps"></a>Próximas etapas

* Entenda as [intenções](luis-concept-intent.md) e as [entidades](luis-concept-entity-types.md).
* Saiba mais sobre os [recursos](luis-concept-feature.md)