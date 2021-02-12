---
title: Renderização de modos
description: Descreve os diferentes modos de renderização do lado do servidor
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594494"
---
# <a name="rendering-modes"></a>Renderização de modos

A renderização remota oferece dois modos principais de operação, modo **TileBasedComposition** e modo **DepthBasedComposition** . Esses modos determinam como a carga de trabalho é distribuída entre várias GPUs no servidor. O modo deve ser especificado no momento da conexão e não pode ser alterado durante o tempo de execução.

Ambos os modos vêm com vantagens, mas também com limitações de recursos inerentes, portanto, escolher o modo mais adequado é o uso específico do caso.

## <a name="modes"></a>Modos

Os dois modos agora são discutidos mais detalhadamente.

### <a name="tilebasedcomposition-mode"></a>Modo ' TileBasedComposition '

No modo **TileBasedComposition** , cada GPU envolvida renderiza subretângulos específicos (blocos) na tela. A GPU principal compõe a imagem final dos blocos antes de ser enviada como um quadro de vídeo para o cliente. Da mesma forma, todas as GPUs precisam ter o mesmo conjunto de recursos para renderização, de modo que os ativos carregados precisam se ajustar à memória de uma única GPU.

A qualidade de renderização nesse modo é um pouco melhor do que no modo **DepthBasedComposition** , uma vez que o MSAA pode funcionar no conjunto completo de geometria para cada GPU. A captura de tela a seguir mostra que a suavização funciona corretamente para ambas as bordas da mesma forma:

![MSAA em TileBasedComposition](./media/service-render-mode-quality.png)

Além disso, nesse modo, cada parte pode ser alternada para um material transparente ou alternado para o modo de **Visualização** por meio do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modo ' DepthBasedComposition '

No modo **DepthBasedComposition** , cada GPU envolvida é renderizada na resolução de tela inteira, mas apenas um subconjunto de malhas. A composição final da imagem na GPU principal cuida de que as partes são mescladas corretamente de acordo com suas informações de profundidade. Naturalmente, a carga de memória é distribuída entre as GPUs, permitindo, portanto, que os modelos de renderização que não caibam na memória de uma única GPU.

Cada GPU única usa MSAA para suavizar o conteúdo local. No entanto, pode haver um alias inerente entre as bordas de GPUs distintas. Esse efeito é mitigado pelo pré-processamento da imagem final, mas a qualidade de MSAA ainda é pior do que no modo **TileBasedComposition** .

Os artefatos MSAA são ilustrados na imagem a seguir: ![ MSAA em DepthBasedComposition](./media/service-render-mode-balanced.png)

A anti-aliasing funciona corretamente entre o sculpture e o Curtain, porque ambas as partes são renderizadas na mesma GPU. Por outro lado, a borda entre Curtain e Wall mostra alguns aliases, pois essas duas partes são compostas de GPUs distintas.

A maior limitação desse modo é que as partes de geometria não podem ser alternadas dinamicamente para materiais transparentes, nem o modo de **Visualização** funciona para o [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). No entanto, outros recursos de substituição de estado (estrutura de tópicos, tonalidade de cores,...) funcionam. Além disso, os materiais que foram marcados como transparentes no momento da conversão funcionam corretamente nesse modo.

### <a name="performance"></a>Desempenho

As características de desempenho para ambos os modos variam de acordo com o caso de uso e são difíceis de usar ou fornecer recomendações gerais. Se você não estiver restrito pelas limitações mencionadas acima (memória ou transparência/ver-pelo), é recomendável testar ambos os modos e monitorar o desempenho usando várias posições de câmera.

## <a name="setting-the-render-mode"></a>Definindo o modo de renderização

O modo de renderização usado em um servidor de renderização remoto é especificado durante `RenderingSession.ConnectAsync` por meio do `RendererInitOptions` .

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingSession. ConnectAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [Struct do C# RendererInitOptions](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession:: ConnectToConnectAsyncRuntime ()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [Struct C++ RendererInitOptions](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Próximas etapas

* [Sessões](../concepts/sessions.md)
* [Componente de substituição do estado hierárquico](../overview/features/override-hierarchical-state.md)