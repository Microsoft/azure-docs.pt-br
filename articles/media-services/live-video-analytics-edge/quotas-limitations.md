---
title: Análise de vídeo ao vivo em cotas de IoT Edge e limitações – Azure
description: Este artigo descreve a análise de vídeo ao vivo em IoT Edge cotas e limitações.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400517"
---
# <a name="quotas-and-limitations"></a>Cotas e limitações

Este artigo enumera as cotas e limitações da análise de vídeo ao vivo no módulo IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Período máximo de uso desconectado

O módulo de borda pode sustentar uma perda temporária de conectividade com a Internet. Se o módulo permanecer desconectado por mais de 36 horas, ele desativará todas as instâncias de grafo em execução. Todas as chamadas de método diretas adicionais serão bloqueadas.

Para retomar o módulo de borda para um estado operacional, você precisará restaurar a conectividade com a Internet para que o módulo seja capaz de se comunicar com êxito com a conta de serviço de mídia do Azure.

## <a name="maximum-number-of-graph-instances"></a>Número máximo de instâncias de grafo

No máximo 1000 instâncias de grafo por módulo (criadas via GraphInstanceSet) têm suporte.

## <a name="maximum-number-of-graph-topologies"></a>Número máximo de topologias de grafo

No máximo 50 topologias de grafo por módulo (criadas via GraphTopologySet) têm suporte.

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitações em topologias de grafo na visualização

Com a versão de visualização, há limitações em nós diferentes que podem ser conectados juntos em uma topologia de grafo de mídia.

* Fonte RTSP
   * Somente uma fonte RTSP é permitida por topologia de grafo.
* Processador de detecção de movimento
   * Deve ser imediatamente downstream da origem RTSP.
   * Não pode ser usado downstream de um processador de extensão HTTP ou gRPC.
* Processador da porta do sinal
   * Deve ser imediatamente downstream da origem RTSP.
* Coletor de ativos 
   * Deve ser imediatamente downstream da fonte RTSP ou do processador de porta de sinal.
* Coletor de arquivos
   * Deve ser imediatamente downstream do processador da porta do sinal.
   * Não pode ser o downstream imediatamente de um processador de extensão HTTP ou gRPC ou processador de detecção de movimento
* Coletor do Hub IoT
   * Não pode ser o downstream imediatamente de uma origem do Hub IoT.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitações nas operações do serviço de mídia na versão prévia

No momento da versão de visualização, a análise de vídeo ao vivo no IoT Edge não oferece suporte ao seguinte:

* A capacidade de migrar a conta de serviço de mídia de uma assinatura para outra sem interrupção.
* A capacidade de usar mais de uma conta de armazenamento com a conta de serviço de mídia.
* A capacidade de alterar as informações da entidade de serviço nas propriedades desejadas do módulo dinamicamente, sem uma reinicialização.

Você só pode usar câmeras IP que dão suporte ao protocolo RTSP. Veja as câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products). Procure dispositivos em conformidade com os perfis G, S ou T.

Além disso, você deve configurar essas câmeras para usar vídeo H. 264 e áudio AAC. Não há suporte para outros codecs no momento. 

## <a name="next-steps"></a>Próximas etapas

[Visão geral](overview.md)
