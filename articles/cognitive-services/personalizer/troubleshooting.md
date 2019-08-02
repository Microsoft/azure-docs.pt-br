---
title: Solução de problemas-personalizador
titleSuffix: Azure Cognitive Services
description: Perguntas sobre solução de problemas sobre o personalizador podem ser encontradas neste artigo.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663677"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.

## <a name="learning-loop"></a>Loop de aprendizagem

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizagem parece não aprender. Como corrigir isso?

O loop de aprendizagem precisa de alguns mil pontos de recompensa para que as chamadas de classificação sejam priorizadas com eficiência. 

Se você não tiver certeza sobre como seu loop de aprendizado está sendo usado no momento, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida. 

## <a name="next-steps"></a>Próximas etapas

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)