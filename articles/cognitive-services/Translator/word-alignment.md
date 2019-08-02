---
title: Alinhamento de palavras – API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Receber informações de alinhamento de palavras da API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 3db5e9651e307211e9dccee20bb8d69586bb9ef1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448157"
---
# <a name="how-to-receive-word-alignment-information"></a>Como receber informações de alinhamento de palavras

## <a name="receiving-word-alignment-information"></a>Receber informações de alinhamento de palavras
Para receber informações de alinhamento, use o método Traduzir e inclua o parâmetro opcional includeAlignment.

## <a name="alignment-information-format"></a>Formato de informações de alinhamento
O alinhamento é retornado como um valor de cadeia de caracteres do seguinte formato para cada palavra da fonte. As informações para cada palavra são separadas por um espaço, incluindo para idiomas não separados por espaço (scripts), como o chinês:

[[SourceTextStartIndex]: [SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exemplo de cadeia de caracteres de alinhamento: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Em outras palavras, os dois-pontos separam o índice inicial e final, o traço separa os idiomas e o espaço separa as palavras. Uma palavra pode alinhar com zero, uma, ou várias palavras em outro idioma e, as palavras alinhadas podem ser não contíguas. Quando nenhuma informação de alinhamento estiver disponível, o elemento Alinhamento ficará vazio. Nesse caso, método não retornará nenhum erro.

## <a name="restrictions"></a>Restrições
O alinhamento somente será retornado para um subconjunto dos pares de idiomas neste ponto:
* do inglês para qualquer outro idioma;
* de qualquer outro idioma para o inglês, exceto chinês simplificado, chinês tradicional e letão para inglês
* de japonês para coreano ou de coreano para japonês e, se a sentença for uma tradução automática, você não receberá informações de alinhamento. Exemplo de uma tradução predefinida é "Isso é um teste", "eu te amo" e outras frases de alta frequência.

## <a name="example"></a>Exemplo

JSON de exemplo

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
