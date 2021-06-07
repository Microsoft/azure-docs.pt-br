---
title: Personalizar um modelo de linguagem no Video Indexer – Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de linguagem no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047129"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalizar um modelo de linguagem no Video Indexer

O Video Indexer dá suporte ao reconhecimento de fala automático por meio da integração com o [Serviço de Fala Personalizado](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/) da Microsoft. É possível personalizar o modelo de linguagem carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Após você treinar seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas, presumindo a pronúncia padrão, e o modelo de linguagem aprenderá novas sequências de palavras prováveis. Modelos de linguagem personalizados têm suporte para inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo, português, hindi e coreano. 

Vamos pegar uma palavra altamente específica, como "Kubernetes" (no contexto do serviço de Kubernetes do Azure), como exemplo. Como a palavra é nova para o Video Indexer, ela é reconhecida como "comunidades". Você precisa treinar o modelo para reconhecê-la como "Kubernetes". Em outros casos, as palavras existem, mas o modelo de linguagem não está esperando que elas apareçam em um determinado contexto. Por exemplo, "serviço de contêiner" não é uma sequência de três palavras que um modelo de linguagem não especializado reconheceria como um conjunto específico de palavras.

Você tem a opção de carregar palavras sem contexto em uma lista em um arquivo de texto. Isso é considerado uma adaptação parcial. Como alternativa, você pode carregar arquivos de texto de documentação ou frases relacionadas ao seu conteúdo para melhor adaptação.

Você pode usar o site ou as APIs do Video Indexer para criar e editar modelos de linguagem personalizados, conforme descrito na seção [Próximos passos](#next-steps) deste tópico.

## <a name="best-practices-for-custom-language-models"></a>Melhores práticas para modelos de linguagem personalizados

O Video Indexer aprende com base nas probabilidades de combinações de palavras, portanto, para que ele aprenda melhor:

* Forneça exemplos reais suficientes de frases como elas seriam faladas.
* Coloque apenas uma frase por linha, não mais. Caso contrário, o sistema aprenderá as probabilidades entre as frases.
* Não tem problema colocar uma palavra como uma frase para impulsionar a palavra em relação a outras, mas o sistema aprende melhor com frases completas.
* Ao introduzir novas palavras ou acrônimos, se possível, dê exemplos de uso em uma frase completa para dar o máximo de contexto possível para o sistema.
* Tente colocar várias opções de adaptação e ver como elas funcionam para você.
* Evite a repetição da mesma frase várias vezes. Isso pode criar um desvio contra o restante da entrada.
* Evite incluir símbolos incomuns (~, # @ % &), pois eles serão descartados. As frases em que aparecem também serão descartadas.
* Evite colocar entradas muito grandes, como centenas de milhares de frases, pois fazer isso diluirá o efeito do impulso.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Linguagem usando APIs](customize-language-model-with-api.md)

[Personalizar o modelo de Linguagem usando o site](customize-language-model-with-website.md)
