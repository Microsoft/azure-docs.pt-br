---
title: Diretrizes de design de divulgação
titleSuffix: Azure Cognitive Services
description: Introdução às diretrizes de design de divulgação e à avaliação do nível de divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 472d55f79033d60c4f40e60b55e0f7fc2ea4517e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716645"
---
# <a name="disclosure-design-guidelines"></a>Diretrizes de design de divulgação
Saiba como criar e manter a confiança com os clientes ao ser transparente sobre a natureza sintética da sua experiência de voz.

## <a name="what-is-disclosure"></a>O que é divulgação?

A divulgação é um meio de permitir que as pessoas saibam que&#39;reinteragir ou ouvir uma voz gerada de forma sintética.

## <a name="why-is-disclosure-necessary"></a>Por que a divulgação é necessária?

A necessidade de divulgar as origens sintéticas de uma voz gerada por computador é relativamente nova. No passado, as vozes geradas por computador eram obviamente que, ninguém jamais as inequívocoia para uma pessoa real. Todos os dias, no entanto, o realm das vozes sintéticas melhora e tornam-se cada vez mais indistinguíveis das vozes humanas.

## <a name="goals"></a>Metas
Esses são os princípios que devem ser considerados ao criar experiências de voz sintética:

**Reforçar confiança**
<br>Projete com a intenção de reprovar o teste do Turing sem degradar a experiência. Permita que os usuários no fato de eles estejam interagindo com uma voz sintética, permitindo que eles se envolvam diretamente com a experiência.

**Adaptar ao contexto de uso**
<br>Entenda quando, onde e como os usuários irão interagir com a voz sintética para fornecer o tipo certo de divulgação no momento certo.

**Definir expectativas claras**
<br>Permita que os usuários descubram e entendam facilmente os recursos do agente. Ofereça oportunidades para saber mais sobre a tecnologia de voz sintética mediante solicitação.

**Adotar falha**
<br>Use momentos de falha para reforçar os recursos do agente.

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia ajuda a determinar quais padrões de divulgação são mais adequados para sua experiência de voz sintética. Em seguida, oferecemos exemplos de como e quando usá-los. Cada um desses padrões foi projetado para maximizar a transparência com os usuários sobre a fala sintética enquanto se mantém verdadeiro no design centrado no homem.

Considerando o vasto corpo de diretrizes de design sobre experiências de voz, nos concentramos especificamente em:

1. [**Avaliação de divulgação**](#disclosure-assessment): um processo para determinar o tipo de divulgação recomendado para sua experiência de voz sintética

2. [**Como divulgar**](concepts-disclosure-patterns.md): exemplos de padrões de divulgação que podem ser aplicados à sua experiência de voz sintética

3. [**Quando divulgar**](concepts-disclosure-patterns.md#when-to-disclose): um tempo ideal para divulgar durante o percurso do usuário

## <a name="disclosure-assessment"></a>Avaliação de divulgação
Considere seus usuários&#39; expectativas sobre uma interação e o contexto no qual eles terão a voz. Se o contexto tornar claro que uma voz sintética está &quot; falando, a &quot; divulgação poderá ser mínima, momentânea ou ainda desnecessária. Os principais tipos de contexto que afetam a divulgação incluem tipo persona, tipo de cenário e nível de exposição. Também ajuda a considerar quem pode estar ouvindo.

### <a name="understand-context"></a>Entender o contexto

Use esta planilha para determinar o contexto da sua experiência de voz sintética. Você o aplicará na próxima etapa, na qual determinará seu nível de divulgação.

|                                    | Contexto de uso                                                                                                                                                                                                                                                                                                                                                       | Riscos potenciais & desafios                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. tipo persona**               | **Se qualquer uma das seguintes opções for aplicável, seu persona se ajustará à categoria "persona-like humana":**<br><br><ul><li> A persona incorpora um humano real, seja uma representação fictícia ou não. (por exemplo, fotografia ou um processamento gerado por computador de uma pessoa real)<br><br><li> A voz sintética é baseada na voz de uma pessoa real amplamente reconhecível (por exemplo, celebridade, figura política) | Quanto mais representações humanas você fornecer à sua persona, maior será a probabilidade de um usuário associá-la a uma pessoa real ou fazer com que ele acredite que o conteúdo é falado por uma pessoa real, e não pelo computador. </ul>                                                                                                                                                                      |
| **2. tipo de cenário**            | **Se qualquer uma das seguintes opções for aplicável, sua experiência de voz se ajustará à categoria "confidencial":**<br><br><ul><li> Obtém ou exibe informações pessoais do usuário <br><br> <li> Transmite notícias/informações confidenciais de tempo (por exemplo, alerta de emergência)<br><br><li> Tem o objetivo de ajudar pessoas reais a se comunicarem entre si (por exemplo, ler emails/textos pessoais)<br><br> <li> Fornece assistência médica/de saúde </ul>            | O uso de voz sintética pode não parecer apropriado ou confiável para as pessoas que o utilizam quando os tópicos estão relacionados a assuntos confidenciais, pessoais ou urgentes. Eles também podem esperar o mesmo nível de empatia e reconhecimento contextual como um humano real. |
| **3. nível de exposição** |**Sua experiência de voz provavelmente se encaixará na categoria ' alta ' se:** <br><br><ul><li>O usuário ouvirá ou interagir com a voz sintética frequentemente ou por um longo período de tempo </ul>                                                                                                                                                                             | A importância de ser transparente e de criar confiança com os usuários é ainda mais alta ao estabelecer relações de longo prazo.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinar o nível de divulgação

Use o diagrama a seguir para determinar se sua experiência de voz sintética requer uma divulgação alta ou baixa com base no seu contexto de uso.

  ![Diagrama de avaliação de divulgação](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Veja também

* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
* [Divulgação para talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Diretrizes para a implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)