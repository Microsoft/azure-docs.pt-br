---
title: Listas de frases-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como fornecer os serviços de fala com uma lista de frases usando `PhraseListGrammar` o objeto para melhorar os resultados de reconhecimento de fala em texto.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562777"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frases para conversão de fala em texto

Ao fornecer os serviços de fala com uma lista de frases, você pode melhorar a precisão do reconhecimento de fala. As listas de frases são usadas para identificar frases conhecidas em dados de áudio, como o nome de uma pessoa ou um local específico.

Por exemplo, se você tiver um comando "mover para" e um possível destino de "para trás" que pode ser falado, você poderá adicionar uma entrada de "mover para o mais à frente". A adição de uma frase aumentará a probabilidade de quando o áudio for reconhecido de "mover para o mais para cima" será reconhecido em vez de "ir para".

Palavras únicas ou frases completas podem ser adicionadas a uma lista de frases. Durante o reconhecimento, uma entrada em uma lista de frases será usada se uma correspondência exata for incluída no áudio. Criando no exemplo anterior, se a lista de frases incluir "mover para a frente" e a frase capturada for "mover para mais lentamente", o resultado do reconhecimento será "mover para o mais lentamente".

>[!Note]
> Atualmente, as listas de frases dão suporte apenas ao inglês para conversão de fala em texto.

## <a name="how-to-use-phrase-lists"></a>Como usar listas de frases

Os exemplos a seguir ilustram como criar uma lista de frases `PhraseListGrammar` usando o objeto.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> O número máximo de listas de frases que o serviço de fala usará para corresponder à fala é de 1024 frases.

Você também pode limpar as frases associadas `PhraseListGrammar` ao chamando Clear ().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> As alterações em `PhraseListGrammar` um objeto são afetadas no próximo reconhecimento ou após uma reconexão com os serviços de fala.

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do SDK de fala](speech-sdk.md)
