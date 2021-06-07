---
title: Perguntas frequentes - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Obtenha respostas a perguntas frequentes sobre a API do Computer Vision nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "68564598"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Perguntas frequentes da API da Pesquisa Visual Computacional

> [!TIP]
> Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API da Pesquisa Visual Computacional em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/)

---

**Pergunta**: *posso treinar uma API de Pesquisa Visual Computacional para usar marcas personalizadas? Por exemplo, eu gostaria de fornecer imagens de raças de gatos para "treinar" a IA e receber o valor da raça em uma solicitação da IA.*

**Resposta**: Essa função não está disponível no momento. No entanto, nossos engenheiros estão trabalhando para trazer essa funcionalidade para a Pesquisa Visual Computacional.

---

**Pergunta**: *A Pesquisa Visual Computacional pode ser usada localmente sem uma conexão de internet?*

**Resposta**: No momento não oferecemos uma solução local ou no local.

---

**Pergunta**: *A Pesquisa Visual Computacional pode ser usada para ler placas de automóvel?*

**Resposta**: A API da Pesquisa Visual Computacional oferece boa detecção de texto com OCR, mas ele não é otimizado para placas de automóveis no momento. Estamos constantemente tentando melhorar nossos serviços e adicionamos OCR para reconhecimento de placas automaticamente à nossa lista de solicitações de recursos.

---

**Pergunta**: *Quais tipos de gravação de superfícies têm suporte para o reconhecimento de manuscrito?*

**Resposta**: A tecnologia funciona com tipos diferentes de superfícies, incluindo quadros de comunicações, papel branco e notas amarelas.

---

**Pergunta**: *Quanto tempo demora a operação de reconhecimento de manuscrito?*

**Resposta**: A quantidade de tempo que demora depende do tamanho do texto. Para textos mais longos, ele poderá demorar vários segundos. Portanto, após a operação de reconhecer texto manuscrito, talvez seja necessário aguardar antes que você possa recuperar os resultados usando a operação Obter resultados de operação de texto manuscrito.

---

**Pergunta**: *Como faz a tecnologia reconhecimento de manuscrito identifica o texto que foi inserido usando um cursor no meio de uma linha?*

**Resposta**: Esse texto é retornado como uma linha separada pela operação de reconhecimento de manuscrito.

---

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito identifica palavras ou linhas riscadas?*

**Resposta**: Se as palavras estão riscadas com várias linhas de forma irreconhecível, a operação de reconhecimento de manuscrito não as identifica. No entanto, se as palavras estão riscadas com uma única linha, essa linha é tratada como ruído e as palavras ainda escolhidas pela operação de reconhecimento de manuscrito.

---

**Pergunta**: *Quais orientações de texto têm suporte para a tecnologia de reconhecimento de manuscrito?*

**Resposta**: O texto orientado em ângulos de até cerca de 30 graus para 40 graus pode ser identificado pela operação de reconhecimento de manuscrito.

---
