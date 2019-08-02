---
title: O que é o Reconhecimento de Tinta Digital? – API de Reconhecimento de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Integre o Reconhecimento de Tinta Digital a seus aplicativos, sites, ferramentas e outras soluções para permitir que os dados de traço de tinta sejam identificados e usados como entrada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c90d656539a2ed64b416dc9b8e7e11e205b98ee6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478360"
---
# <a name="what-is-the-ink-recognizer-api"></a>O que é a API de Reconhecimento de Tinta Digital?


O Serviço Cognitivo do Reconhecimento de Tinta Digital fornece uma API REST baseada em nuvem para analisar e reconhecer o conteúdo de tinta digital. Ao contrário dos serviços que usam o OCR (reconhecimento óptico de caracteres), a API exige dados de traço de tinta digital como entrada. Os traços de tinta digital são conjuntos ordenados por tempo de pontos 2D (coordenadas X e Y) que representam o movimento das ferramentas de entrada, como canetas digitais ou dedos. Em seguida, ela reconhece as formas e o conteúdo manuscrito da entrada e retorna uma resposta JSON contendo todas as entidades reconhecidas.

![Um fluxograma que descreve o envio de uma entrada de traço de tinta à API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Recursos

Com a API de Reconhecimento de Tinta Digital, você pode reconhecer com facilidade o conteúdo manuscrito em seus aplicativos. 

|Recurso  |DESCRIÇÃO  |
|---------|---------|
| Reconhecimento de manuscrito | Reconheça o conteúdo manuscrito nos 63 [idiomas e localidades](language-support.md) principais. | 
| Reconhecimento de layout | Obtenha informações estruturais sobre o conteúdo de tinta digital. Divida o conteúdo em regiões de escrita, parágrafos, linhas, palavras e listas com marcadores. Em seguida, seus aplicativos podem usar as informações de layout para criar recursos adicionais, como formatação automática de lista e alinhamento de formas. |
| Reconhecimento de formas | Reconheça as [formas geométricas](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) mais comumente usadas em anotações. |
| Reconhecimento de formas e texto combinados | Reconheça quais traços de tinta pertencem a formas ou conteúdo manuscrito e classifique-os separadamente.|

## <a name="workflow"></a>Fluxo de trabalho

A API de Reconhecimento de Tinta Digital é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Após a inscrição:

1. Selecione seus dados de traço de tinta e [formate-os](concepts/send-ink-data.md#sending-ink-data) em JSON válido.
1. Envie uma solicitação à API de Reconhecimento de Tinta Digital com seus dados.
1. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Experimente um Início Rápido nas linguagens a seguir para começar a fazer chamadas à API de Reconhecimento de Tinta Digital.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
