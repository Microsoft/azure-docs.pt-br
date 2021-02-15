---
title: Gravação de vídeo-Azure
description: No contexto de um sistema de gerenciamento de vídeo para câmeras CCTVs, a gravação de vídeo se refere ao processo de capturar vídeo das câmeras e gravá-lo para exibição por meio de aplicativos móveis e de navegador. A gravação de vídeo pode ser categorizada como gravação de vídeo contínua e gravação de vídeo baseada em evento.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260189"
---
# <a name="video-recording"></a>Gravação de vídeo

No contexto de um sistema de gerenciamento de vídeo para câmeras CCTVs, a gravação de vídeo se refere ao processo de capturar vídeo das câmeras e gravá-lo para exibição por meio de aplicativos móveis e de navegador. A gravação de vídeo pode ser categorizada como gravação de vídeo contínua e gravação de vídeo baseada em evento. 

## <a name="continuous-video-recording"></a>Gravação de vídeo contínua  

A CVR (gravação contínua de vídeo) refere-se ao processo de gravar continuamente todo o vídeo que está sendo capturado de uma fonte de vídeo. O CVR garante que todo o vídeo esteja disponível (ditado pela [política de gravação](#recording-policy)) para analisar e/ou auditar em um momento posterior.

## <a name="event-based-video-recording"></a>Gravação de vídeo baseada em eventos  

A EVR (Gravação de vídeo baseada em eventos) se refere ao processo de gravação de vídeo acionado por um evento. O evento em questão, poderia se originar devido ao processamento do próprio sinal de vídeo ou pode ser de uma fonte independente (por exemplo, de um sensor de porta). A EVR (gravação de vídeo baseada em eventos) pode resultar em economia de armazenamento, mas requer componentes adicionais que geram os eventos e disparam a gravação de vídeo (de acordo com uma política predefinida). Em outras palavras, o EVR requer tomar decisões adicionais sobre os eventos que devem disparar a gravação de vídeo e a política associada à gravação de vídeo (também conhecida como política de gravação). Um exemplo de uma política pode ser algo como: gravar o vídeo por 2 minutos a partir de 30 segundos antes da hora do evento. Os eventos em questão podem se originar devido ao processamento de vídeo na própria câmera. Por exemplo, várias câmeras dão suporte à geração de um evento de sinal de detecção de movimento quando o movimento é detectado em uma zona pré-configurada de interesse no visor da câmera. Os eventos também podem ser gerados pelo processamento do vídeo em outro dispositivo que captura o vídeo da câmera e o analisa usando técnicas de processamento de imagem simples ou modelos de aprendizado de máquina sofisticados. 

## <a name="choosing-recording-modes"></a>Escolhendo modos de gravação  

A escolha de se usar CVR ou EVR depende das metas de negócios. A análise de vídeo ao vivo em IoT Edge fornece recursos de plataforma para CVR e EVR. Você pode aprender mais sobre isso nos artigos do cenário [CVR](continuous-video-recording-concept.md) e [EVR](event-based-video-recording-concept.md) .

## <a name="recording-policy"></a>Política de gravação  

A política de gravação refere-se às políticas que determinam a hora de início/parada da gravação (no caso de EVR), a duração da gravação e a exclusão da gravação. As políticas de gravação permitem equilibrar o custo de armazenamento com os requisitos de negócios. As políticas de gravação diferem entre CVR e EVR. Para CVR, a política de gravação define quantos dias de vídeo devem ser armazenados (por exemplo, os últimos 7 dias). Para EVR, a política de gravação define quando a gravação deve começar e terminar, juntamente com a duração do vídeo. Você pode aprender mais sobre isso nos artigos do cenário [CVR](continuous-video-recording-concept.md) e [EVR](event-based-video-recording-concept.md) .

## <a name="next-steps"></a>Próximas etapas

* [Detectar movimento e gravar clipes de vídeo nos Serviços de Mídia do Azure](detect-motion-record-video-clips-media-services-quickstart.md)
* [Detectar o movimento, gravar clipes de vídeo em dispositivos de borda](detect-motion-record-video-clips-edge-devices-quickstart.md)

