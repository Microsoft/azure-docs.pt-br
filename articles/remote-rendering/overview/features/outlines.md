---
title: Renderização de estrutura de tópicos
description: Explica como fazer renderização de contorno de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592000"
---
# <a name="outline-rendering"></a>Renderização de estrutura de tópicos

Os objetos selecionados podem ser realçados visualmente adicionando renderização de contorno através do [Componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md). Este capítulo explica como os parâmetros globais para renderização de contornos são alterados através da API do cliente.

As propriedades de contorno são uma configuração global. Todos os objetos que usam a renderização de contorno usarão a mesma configuração, não é possível usar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para `OutlineSettings`

A classe `OutlineSettings` contém as configurações relacionadas às propriedades globais de contorno. Ela apresenta os seguintes membros:

| Parâmetro      | Type    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor usada para desenhar o contorno. A parte alfa é ignorada.         |
| `PulseRateHz`    | FLOAT   | A taxa de oscilação por segundo do contorno|
| `PulseIntensity` | FLOAT   | A intensidade do efeito de pulso do contorno. Deve estar entre 0,0 para nenhuma pulsação e 1,0 para pulsação total. A intensidade define implicitamente a opacidade mínima do contorno como `MinOpacity = 1.0 - PulseIntensity`. |

![Um objeto renderizado três vezes com parâmetros de estrutura de tópicos diferentes ](./media/outlines.png) o efeito de alterar o `color` parâmetro de amarelo (esquerdo) para Magenta (centro) e `pulseIntensity` de 0 a 0,8 (à direita).

## <a name="example"></a>Exemplo

O código a seguir mostra um exemplo de configuração de parâmetros de contorno através da API:

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Desempenho

A renderização do contorno pode ter um impacto significativo no desempenho da renderização. Esse impacto varia conforme a relação espacial de espaço na tela entre objetos selecionados e não selecionados para um determinado quadro.

## <a name="api-documentation"></a>Documentação da API

* [Propriedade C# RenderingConnection. OutlineSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection:: OutlineSettings ()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>Próximas etapas

* [Componente de substituição do estado hierárquico](../../overview/features/override-hierarchical-state.md)