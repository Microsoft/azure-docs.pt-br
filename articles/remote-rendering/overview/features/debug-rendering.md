---
title: Depurar renderização
description: Visão geral dos efeitos de renderização de depuração no lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591728"
---
# <a name="debug-rendering"></a>Depurar renderização

A API de renderização de depuração oferece uma variedade de opções globais para alterar a renderização no lado do servidor com diferentes efeitos de depuração.

## <a name="available-debug-rendering-effects"></a>Efeitos de renderização de depuração disponíveis

|Configuração                          | Efeito                               |
|---------------------------------|:-------------------------------------|
|Contador de quadros                    | Renderiza uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra a ID do quadro do servidor atual, que é incrementada continuamente conforme o processamento ocorre. |
|Contagem de polígonos                    | Renderiza uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra a quantidade de polígonos processados no momento, o mesmo valor que foi consultado por [consultas de desempenho no lado do servidor](performance-queries.md)| 
|Wireframe                        | Se habilitado, todas as geometrias de objeto carregadas no servidor serão renderizadas no modo Wireframe. Somente as bordas de polígonos serão rasterizadas nesse modo. |

O seguinte código habilita esses efeitos de depuração:

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Depurar renderização](./media/debug-rendering.png)

> [!NOTE]
> Todos os efeitos de depuração de renderização são configurações globais que afetam o quadro inteiro.

## <a name="use-cases"></a>Casos de uso

A API de depuração de renderização destina-se a tarefas de depuração simples, como verificar se a conexão de serviço está realmente funcionando corretamente. As opções de renderização de texto afetam diretamente os quadros de vídeo em fluxo baixo. Ao habilitá-las, é verificado se novos quadros são recebidos e se o vídeo foi decodificado corretamente.

No entanto, os efeitos fornecidos não dão informações detalhadas sobre a integridade do serviço. As [consultas de desempenho no lado do servidor](performance-queries.md) são recomendadas para esse caso de uso.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* A habilitação das sobreposições de texto gera pouca ou nenhuma sobrecarga de desempenho.
* A habilitação do modo Wireframe provoca uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena. Para cenas complexas, esse modo pode fazer com que a taxa de quadros seja descartada abaixo da meta de 60 Hz.

## <a name="api-documentation"></a>Documentação da API

* [C++ RenderingConnection::D ebugRenderingSettings ()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Próximas etapas

* [Modos de renderização](../../concepts/rendering-modes.md)
* [Consultas de desempenho no lado do servidor](performance-queries.md)