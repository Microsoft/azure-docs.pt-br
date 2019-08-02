---
title: Enviar dados de tinta para a API de Reconhecimento de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como chamar a API do Analisador de Tinta Digital para diferentes aplicativos
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 23431a6171f9ce4d2550ee62ac84679ce36126de
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721763"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Enviar dados de tinta para a API de Reconhecimento de Tinta Digital 

Escrita à tinta digital se refere às tecnologias que permitem representações digitais de entrada, como manuscrito e desenhos. Normalmente, isso é feito usando um digitalizador que captura os movimentos de dispositivos de entrada, como uma caneta. Conforme os dispositivos continuam possibilitando experiências sofisticadas de escrita à tinta digital, a inteligência artificial e o aprendizado de máquina permitem o reconhecimento de formas e texto escritos em qualquer contexto. A API de Reconhecimento de Tinta Digital permite que você envie traços de tinta e obtenha informações detalhadas sobre eles. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A API de Reconhecimento de Tinta Digital vs. serviços de OCR

A API de Reconhecimento de Tinta Digital não usa o OCR (reconhecimento óptico de caracteres). Os serviços de OCR processam os dados de pixel de imagens para fornecer o reconhecimento de manuscrito e texto. Às vezes, isso é chamado de reconhecimento offline. Em vez disso, a API de Reconhecimento de Tinta Digital exige dados de traço de tinta digital capturados conforme o dispositivo de entrada é utilizado. Dessa maneira, o processamento de dados de tinta digital pode produzir resultados de reconhecimento mais precisos comparado aos serviços de OCR. 

## <a name="sending-ink-data"></a>Como enviar dados de tinta

A API de Reconhecimento de Tinta Digital exige as coordenadas X e Y que representam os traços de tinta criados por um dispositivo de entrada, desde o momento em que ele toca a superfície de detecção até quando ele é elevado. Os pontos de cada traço precisam ser uma cadeia de caracteres de valores separados por vírgula e ser formatados em JSON, como o exemplo abaixo. Além disso, cada traço de tinta precisa ter uma ID exclusiva em cada solicitação. Se a ID for repetida na mesma solicitação, a API retornará um erro. Para obter os resultados de reconhecimento mais precisos, tenha, pelo menos, oito dígitos após o ponto decimal. A origem (0,0) da tela é considerada o canto superior esquerdo da tela de escrita à tinta.

> [!NOTE]
> O exemplo a seguir não é um JSON válido. Encontre uma solicitação JSON completa do Reconhecimento de Tinta Digital no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Resposta do Reconhecimento de Tinta Digital

A API de Reconhecimento de Tinta Digital retorna uma resposta de análise sobre os objetos que ela reconheceu do conteúdo de tinta. A resposta contém unidades de reconhecimento que descrevem as relações entre os diferentes traços de tinta. Por exemplo, os traços que criam formas distintas e separadas estarão contidos em unidades diferentes. Cada unidade contém informações detalhadas sobre seus traços de tinta, incluindo o objeto reconhecido, suas coordenadas e outros atributos de desenho.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Formas reconhecidas pela API de Reconhecimento de Tinta Digital

A API de Reconhecimento de Tinta Digital pode identificar as formas mais comumente usadas em anotações. A imagem abaixo mostra alguns exemplos básicos. Para obter uma lista completa de formas e outros tipos de conteúdo de tinta reconhecidos pela API, confira o [artigo de referência da API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![A lista de formas reconhecidas pela API de Reconhecimento de Tinta Digital](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Padrões de chamada recomendados

Chame a API REST do Reconhecimento de Tinta Digital em diferentes padrões de acordo com seu aplicativo. 

### <a name="user-initiated-api-calls"></a>Chamadas à API iniciadas pelo usuário

Se você estiver criando um aplicativo que recebe a entrada do usuário (por exemplo, um aplicativo de anotação), o ideal será dar a ele o controle sobre quando e qual tinta é enviada para a API de Reconhecimento de Tinta Digital. Essa funcionalidade é especialmente útil quando texto e formas estão presentes na tela e os usuários desejam executar ações diferentes para cada um. Considere a adição de recursos de seleção (como uma ferramenta Laço ou outra ferramenta de seleção geométrica) que permita aos usuários escolher o que é enviado à API.  

### <a name="app-initiated-api-calls"></a>Chamadas à API iniciadas pelo aplicativo

Você também pode fazer com que o aplicativo chame a API de Reconhecimento de Tinta Digital após um tempo limite. Enviando os traços de tinta atuais à API regularmente, você pode armazenar os resultados de reconhecimento conforme eles são criados, melhorando o tempo de resposta da API. Por exemplo, você pode enviar uma linha de texto manuscrito à API depois de detectar que o usuário a concluiu. 

Ter os resultados de reconhecimento com antecedência fornece informações sobre as características dos traços de tinta e sua relação entre si. Por exemplo, quais traços são agrupados para formar a mesma palavra, linha, lista, parágrafo ou forma. Essas informações podem melhorar os recursos de seleção de tinta de seu aplicativo proporcionando a capacidade de selecionar grupos de traços ao mesmo tempo, por exemplo.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrar a API de Reconhecimento de Tinta Digital ao Windows Ink

O [Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornece ferramentas e tecnologias para proporcionar experiências de escrita à tinta digital em uma ampla variedade de dispositivos. Combine a plataforma Windows Ink com a API de Reconhecimento de Tinta Digital para criar aplicativos que exibem e interpretam traços de tinta digital.

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Reconhecimento de Tinta Digital?](../overview.md)
* [Referência da API REST do Reconhecimento de Tinta Digital](https://go.microsoft.com/fwlink/?linkid=2089907)

* Comece a enviar dados de traço de tinta digital usando:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)