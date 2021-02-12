---
title: Detecção de caracteres animados com Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a detecção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854743"
---
# <a name="animated-character-detection-preview"></a>Detecção de caracteres animados (versão prévia)

Os serviços de mídia do Azure Video Indexer dão suporte à detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Essa funcionalidade está disponível por meio do portal e pela API.

Depois de carregar um vídeo animado com um modelo de animação específico, Video Indexer extrai quadros-chave, detecta caracteres animados nesses quadros, agrupa caracteres semelhantes e escolhe o melhor exemplo. Em seguida, ele envia os caracteres agrupados para Visão Personalizada que identificam caracteres com base nos modelos em que foram treinados. 

Antes de começar a treinar seu modelo, os caracteres são detectados namelessly. À medida que você adiciona nomes e treina o modelo, o Video Indexer reconhecerá os caracteres e os nomeará de acordo.

## <a name="flow-diagram"></a>Fluxograma

O diagrama a seguir demonstra o fluxo do processo de detecção de caracteres animados.

![Fluxograma](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo de sua conta de Video Indexer, diferentes conjuntos de recursos estarão disponíveis. Para obter informações sobre como conectar sua conta ao Azure, consulte [criar uma conta de video indexer conectada ao Azure](connect-to-azure.md).

* Conta de avaliação: Video Indexer usa uma conta de Visão Personalizada interna para criar o modelo e conectá-lo à sua conta do Video Indexer. 
* Conta paga: você conecta sua conta do Visão Personalizada à sua conta do Video Indexer (se você ainda não tiver uma, precisará criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Avaliação vs. pagas

|Funcionalidade|Avaliação|Pago|
|---|---|---|
|Conta de Visão Personalizada|Gerenciado por trás dos bastidores por Video Indexer. |Sua conta de Visão Personalizada está conectada ao Video Indexer.|
|Número de modelos de animação|Um|Até 100 modelos por conta (limitação de Visão Personalizada).|
|Como treinar o modelo|Video Indexer treina o modelo para obter novos exemplos de caracteres adicionais de caracteres existentes.|O proprietário da conta treina o modelo quando eles estão prontos para fazer alterações.|
|Opções avançadas no Visão Personalizada|Sem acesso ao portal de Visão Personalizada.|Você pode ajustar os modelos por conta própria no portal de Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Usar a detecção de caracteres animados com o portal e a API

Para obter detalhes, consulte [usar a detecção de caracteres animados com o portal e a API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Limitações

* Atualmente, não há suporte para a funcionalidade de "identificação de animação" na região East-Asia.
* Os caracteres que parecem ser pequenos ou distantes no vídeo podem não ser identificados corretamente se a qualidade do vídeo for ruim.
* A recomendação é usar um modelo por conjunto de caracteres animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)