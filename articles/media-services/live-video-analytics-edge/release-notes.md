---
title: Análise de vídeo ao vivo em notas de versão IoT Edge – Azure
description: Este tópico fornece notas de versão de análise de vídeo ao vivo sobre versões IoT Edge, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954388"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Análise de vídeo ao vivo em notas de versão IoT Edge

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no leitor de feed de RSS.

Este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

<hr width=100%>

## <a name="january-12-2021"></a>12 de janeiro de 2021

Esta marca de versão destina-se à atualização de janeiro de 2021 do módulo:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 2 (Live-Video-Analytics: 2). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.
### <a name="bug-fixes"></a>Correções de bug 

* Os campos `ActivationSignalOffset` , `MinimumActivationTime` e `MaximumActivationTime` nos processadores de porta de sinal foram definidos incorretamente como propriedades obrigatórias. Agora, eles são propriedades **opcionais** .
* Corrigido um bug de uso que faz com que a análise de vídeo ao vivo no módulo IoT Edge falhe quando implantada em determinadas regiões.

<hr width=100%>

## <a name="december-14-2020"></a>14 de dezembro de 2020
Esta versão é a versão de atualização de visualização pública da análise de vídeo ao vivo no IoT Edge. A marca de versão é

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Atualizações de módulo
* Adicionado suporte para usar mais de um processador de extensão HTTP e processador de extensão gRPC por topologia de grafo.
* Adicionado suporte para [Gerenciamento de espaço em disco para nós do coletor](upgrading-lva-module.md#disk-space-management-with-sink-nodes).
* `MediaGraphGrpcExtension` o nó agora dá suporte à propriedade [extensionConfiguration](grpc-extension-protocol.md) para usar vários modelos de ia em um único servidor gRPC.
* Adição de suporte à coleta de métricas do módulo análise de vídeo ao vivo no [formato Prometheus](https://prometheus.io/docs/practices/naming/). Saiba mais sobre como [coletar métricas e exibições no Azure monitor.](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* Adicionada a capacidade de filtrar a seleção de saída. Você pode transmitir **somente áudio** ou **somente vídeo** ou **áudio e vídeo** com a ajuda de `outputSelectors` para qualquer nó do grafo. 
* O processador do filtro de taxa de quadros foi **preterido**.  
    * O gerenciamento de taxa de quadros agora está disponível dentro dos nós do processador de extensão do grafo.

### <a name="visual-studio-code-extension"></a>Extensão Visual Studio Code
* Lançada a [análise de vídeo ao vivo em IOT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) -uma extensão Visual Studio Code para ajudá-lo a gerenciar grafos de mídia LVA. Essa extensão funciona com o **módulo LVA 2,0** e oferece edição e gerenciamento de grafos de mídia com uma interface gráfica elegante e fácil de usar.
## <a name="september-22-2020"></a>22 de setembro de 2020

Esta marca de versão destina-se à atualização de setembro de 2020 do módulo:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 1 (Live-Video-Analytics: 1). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.

### <a name="module-updates"></a>Atualizações de módulo

* Um novo nó de extensão do grafo, o [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) está disponível para integração com o módulo [análise espacial](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(versão prévia) dos serviços cognitivas.
* Suporte adicionado para dispositivos Linux ARM64-use [as etapas manuais](deploy-iot-edge-device.md) para implantar nesses dispositivos.

### <a name="documentation-updates"></a>Atualizações na documentação

* [As instruções](deploy-azure-stack-edge-how-to.md) estão disponíveis para usar a análise de vídeo ao vivo em IOT Edge em dispositivos Azure Stack Edge.
* Novo tutorial sobre como desenvolver modelos de visão de computador específicos do cenário usando [visão personalizada serviço](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) e usá-lo para [analisar o vídeo ao vivo](custom-vision-tutorial.md) em tempo real.

<hr width=100%>

## <a name="august-19-2020"></a>19 de agosto de 2020

Esta marca de versão é para a atualização de agosto de 2020 do módulo:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 1 (Live-Video-Analytics: 1). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.

### <a name="module-updates"></a>Atualizações de módulo

* Agora você pode obter alto desempenho de transferência de conteúdo de dados entre a análise de vídeo ao vivo no IoT Edge e sua extensão personalizada usando a estrutura gRPC. Consulte [o guia de início rápido](analyze-live-video-use-your-grpc-model-quickstart.md) para começar.
* Implantação regional mais ampla da análise de vídeo ao vivo e apenas o serviço de nuvem foi atualizado.  
* A análise de vídeo ao vivo agora está disponível em mais 25 regiões em todo o mundo. Aqui está a [lista](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) de todas as regiões disponíveis.  
* A [configuração](https://aka.ms/lva-edge/setup-resources-for-samples) para início rápido foi atualizada também com novas regiões com suporte.
    * Não há nenhuma chamada para ação para qualquer pessoa que já tenha os recursos configurados

### <a name="bug-fixes"></a>Correções de bug 

* Remover o uso de uma extensão do Azure preterida no script de instalação

<hr width=100%>

## <a name="july-13-2020"></a>13 de julho de 2020

Esta marca de versão é para a atualização de julho de 2020 do módulo:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 1 (Live-Video-Analytics: 1). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.

### <a name="module-updates"></a>Atualizações de módulo

* Agora você pode criar topologias de grafo que têm um nó de coletor de ativos e um nó de coletor de arquivo downstream de um nó de processador de porta de sinal. Consulte [a topologia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) para obter um exemplo.

### <a name="bug-fixes"></a>Correções de bug

* Melhorias na validação das propriedades desejadas

<hr width=100%>

## <a name="june-1-2020"></a>1º de junho de 2020

Esta versão é a primeira versão de visualização pública da análise de vídeo ao vivo no IoT Edge. A marca de versão é

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funcionalidades com suporte

* Analise fluxos de vídeo ao vivo usando os módulos de ia de sua escolha e, opcionalmente, grave vídeo no dispositivo de borda ou na nuvem
* Use em sistemas operacionais Linux AMD64 [com suporte](../../iot-edge/support.md) pelo IOT Edge
* Implantar e configurar o módulo por meio do Hub IoT usando portal do Azure ou Visual Studio Code
* Gerenciar [topologias de grafo e instâncias de grafo](media-graph-concept.md#media-graph-topologies-and-instances) remotamente ou localmente por meio das seguintes chamadas de método diretas

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Próximas etapas

[Visão geral](overview.md)
