---
title: Recursos
titleSuffix: Language Understanding - Azure Cognitive Services
description: Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 7889f223b607912fd88c798b31ec028f97dfbbd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812931"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Recurso de lista de frases em seu aplicativo LUIS

No aprendizado de máquina, um *recurso* é um traço ou atributo específico de dados que seu sistema observa. 

Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar. Os recursos ajudam o LUIS a reconhecer intenções e entidades, mas não são intenções ou entidades em si. Em vez disso, os recursos podem fornecer exemplos de termos relacionados.  

## <a name="what-is-a-phrase-list-feature"></a>O que é um recurso de lista de frases?
Uma lista de frases é uma lista de palavras ou frases significativas para seu aplicativo, mais que outras palavras em enunciados. Uma lista de frase adiciona ao vocabulário do domínio de aplicativo como um sinal adicional ao LUIS sobre essas palavras. O que o LUIS aprende sobre um deles é aplicado automaticamente aos outros também. Esta lista não é uma [entidade de lista](luis-concept-entity-types.md#types-of-entities) fechada de correspondências de texto exato.

Listas de frases não ajudam com lematização, portanto, você precisará adicionar exemplos de enunciado que usem uma variedade de lematização para quaisquer palavras do vocabulário e frases significativas.

## <a name="phrase-lists-help-all-models"></a>Listas de frases ajudam todos os modelos

Listas de frase não estão vinculadas a uma intenção ou entidade específico, mas são adicionadas como um aumento significativo para todas as intenções e entidades. Sua finalidade é melhorar a detecção de intenções e a classificação de entidades.

## <a name="how-to-use-phrase-lists"></a>Como usar as listas de frase

Crie uma lista de frases quando seu aplicativo tiver palavras ou frases importantes para o aplicativo, como:

* termos do setor
* gírias
* abreviações
* linguagem específica da empresa
* o texto é de outro idioma, mas frequentemente usado em seu aplicativo
* palavras-chave e frases em seus enunciados de exemplo

Depois de inserir algumas palavras ou frases, use o recurso **Recomendado** para localizar valores relacionados. Examine os valores relacionados antes de adicionar a seus valores de lista de frase.

|Tipo de lista|Finalidade|
|--|--|
|Intercambiável|Sinônimos ou palavras que, quando trocadas por outra palavra da lista, têm a mesma intenção e extração de entidade.|
|Não intercambiável|Vocabulário do aplicativo, específico de seu aplicativo, mais do que outras palavras do idioma de modo geral.|

### <a name="interchangeable-lists"></a>Listas intercambiáveis

Uma lista de frases *intercambiáveis* é para valores que são sinônimos. Por exemplo, se você quiser encontrar todos os corpos de água e tiver enunciados de exemplo como: 

* Que cidades estão próximas dos Grandes Lagos? 
* Que estrada passa ao longo do Lago Havasu?
* Onde o Nilo começa e termina? 

Cada enunciado deve ser determinado para intenção e entidades, independentemente do corpo de água: 

* Quais cidades estão próximas de [bodyOfWater]?
* Quais estradas passam ao longo de [bodyOfWater]?
* Onde o [bodyOfWater] começa e terminar? 

Como as palavras ou frases para o corpo da água são sinônimos e podem ser usadas alternadamente em enunciados, use a configuração **Intercambiável** na lista de frases. 

### <a name="non-interchangeable-lists"></a>Listas não intercambiáveis

Uma lista de frases não intercambiáveis é um sinal que aumenta a detecção para LUIS. A lista de frases indica palavras ou frases mais significativas que outras palavras. Isso ajuda na determinação da intenção e na detecção da entidade. Por exemplo, digamos que você tenha um domínio de assunto, como viagem, que seja global (ou seja, entre culturas, mas ainda em um único idioma). Há palavras e frases que são importantes para o aplicativo, mas que não são sinônimos. 

Para outro exemplo, use uma lista de frases não intercambiáveis para palavras raras, proprietárias e estrangeiras. O LUIS pode ser incapaz de reconhecer palavras raras e proprietárias, assim como palavras estrangeiras (fora da cultura do aplicativo). A configuração não intercambiável indica que o conjunto de palavras raras forma uma classe que o LUIS deve aprender a reconhecer, mas elas não são sinônimos ou intercambiáveis entre si.

Não adicione cada palavra ou frase possível a uma lista de frases, adicione algumas palavras ou frases por vez e treine e publique novamente. 

À medida que a lista de frases cresce ao longo do tempo, você pode descobrir que alguns termos têm muitas formas (sinônimos). Divida esses termos em outra lista de frases intercambiáveis. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Listas de frases ajudam a identificar entidades intercambiáveis simples
Listas de frases intercambiáveis são uma boa maneira de ajustar o desempenho do aplicativo do LUIS. Se o seu aplicativo tiver problemas para prever declarações da intenção correta, ou reconhecer entidades, pense se a declaração contém palavras incomuns ou que podem ter significado ambíguo. Essas palavras são boas candidatas para inclusão em uma lista de frases.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Listas de frases ajudam a identificar intenções entendendo melhor o contexto
Uma lista de frases não é uma instrução para o LUIS executar uma correspondência estrita ou sempre rotular todos os termos na lista de frases exatamente da mesma forma. Ela é simplesmente uma dica. Por exemplo, você poderia ter uma lista de frases que indica que "Patti" e "Selma" são nomes, mas o LUIS ainda pode usar as informações contextuais para reconhecer que eles significam algo diferente em "Fazer uma reserva de jantar para 2 no Patti's Diner" e "Localizar trajeto de automóvel até Selma, Geórgia". 

Adicionar uma lista de frases é uma alternativa à adição de mais declarações de exemplo para uma intenção. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usar listas de frase versus entidades de lista
Embora uma lista de frases e as entidades de lista possam afetar as declarações em todas as intenções, cada uma faz isso de maneira diferente. Use uma lista de frases para afetar a pontuação de previsão da intenção. Use uma entidade de lista para afetar a extração de entidade de uma correspondência exata do texto. 

### <a name="use-a-phrase-list"></a>Usar uma lista de frases
Com uma lista de frases, o LUIS ainda pode levar o contexto em consideração e generalizar para identificar os itens que são semelhantes, mas que não são uma correspondência exata, como itens em uma lista. Se você precisar que o seu aplicativo LUIS generalize e identifique novos itens em uma categoria, use uma lista de frases. 

Quando você deseja ser capaz de reconhecer novas instâncias de uma entidade, como um agendador de reunião deve reconhecer os nomes dos novos contatos ou um aplicativo de estoque que deve reconhecer novos produtos, use outro tipo de entidade de computador aprendeu como uma entidade simples. Em seguida, crie uma lista de palavras e frases que ajuda o LUIS a encontrar outras palavras semelhantes para a entidade. Essa lista orienta o LUIS a reconhecer exemplos da entidade adicionando outro significado ao valor dessas palavras. 

Listas de frase são como um vocabulário específico ao domínio que ajudam a melhorar a qualidade da compreensão de intenções e entidades. Um uso comum de uma lista de frases é para substantivos, como nomes de cidades. Um nome de cidade pode ter várias palavras, incluindo hifens ou apóstrofos.
 
### <a name="dont-use-a-phrase-list"></a>Não usar uma lista de frases 
Uma entidade de lista define explicitamente cada valor que uma entidade pode assumir, e identifica somente os valores com correspondência exata. Uma entidade de lista pode ser apropriada para um aplicativo no qual todas as instâncias de uma entidade são conhecidas e não são alteradas com frequência. Exemplos são os itens do menu de um restaurante que muda com pouca frequência. Se você precisar de uma correspondência exata do texto de uma entidade, não use uma lista de frases. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

Veja [Adicionar recursos](luis-how-to-add-features.md) para saber como adicionar recursos ao seu aplicativo LUIS.
