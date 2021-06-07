---
title: Recortar planos
description: Explica o que são planos de recorte e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594365"
---
# <a name="cut-planes"></a>Recortar planos

Um *plano de recorte* é um recurso visual que corta pixels em um lado de um plano virtual, revelando o interior das [malhas](../../concepts/meshes.md).
A imagem abaixo demonstra o efeito. À esquerda, há a malha original, à direita, é possível ver dentro da malha:

![Recortar planos](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Adicione um plano de recorte à cena criando um *CutPlaneComponent*. O local e a orientação do plano são determinados pela [entidade](../../concepts/entities.md) do proprietário do componente.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Propriedades de CutPlaneComponent

As propriedades a seguir são expostas em um componente de plano de recorte:

* `Enabled`: É possível desativar temporariamente os planos de recorte desabilitando o componente. Os planos de recorte desabilitados não geram sobrecarga de renderização e também não contam para o limite do plano de recorte global.

* `Normal`: Especifica qual direção (+X,-X,+Y,-Y,+Z,-Z) é usada como o plano normal. Essa direção é relativa à orientação da entidade do proprietário. Mova e gire a entidade do proprietário para o posicionamento exato.

* `FadeColor` e `FadeLength`:

  Se o valor alfa de *FadeColor* for diferente de zero, os pixels próximos ao plano de recorte ficarão em direção à parte RGB de FadeColor. A força do canal alfa determina se ele ficará totalmente esmaecido em direção à cor de esmaecimento ou apenas parcialmente. *FadeLength* define em qual distância esse esmaecimento será realizado.

* `ObjectFilterMask`: Uma máscara de bits de filtro que determina qual geometria é afetada pelo plano de recorte. Consulte o próximo parágrafo para obter informações detalhadas.

### <a name="selective-cut-planes"></a>Planos de corte seletivo

É possível configurar planos de corte individuais para que afetem apenas geometria específica. A figura a seguir ilustra como essa configuração pode parecer na prática:

![Planos de corte seletivo](./media/selective-cut-planes.png)

A filtragem funciona por meio da **comparação de máscara de bits lógica** entre uma máscara de bits no lado do plano de recorte e uma segunda máscara de bits definida na geometria. Se o resultado de uma `AND` operação lógica entre as máscaras não for zero, o plano de recorte afetará a geometria.

* A máscara de bits no componente do plano de recorte é definida por meio de sua `ObjectFilterMask` Propriedade
* A máscara de bits em uma subhierarquia de geometry é definida por meio de [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Exemplos:

| Máscara de filtro de plano de corte | Máscara de filtro de geometria  | Resultado de lógica `AND` | O plano de recorte afeta a geometria?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Sim |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Sim |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | Não |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | Não |

>[!TIP]
> Definir um plano de recorte `ObjectFilterMask` como 0 significa que ele não afetará nenhuma geometria porque o resultado de lógico `AND` nunca pode ser não nulo. O sistema de renderização não considerará esses planos em primeiro lugar, portanto, esse é um método leve para desabilitar planos de corte individuais. Esses planos de corte também não contam com o limite de 8 planos ativos.

## <a name="limitations"></a>Limitações

* A renderização remota do Azure dá suporte a um **máximo de oito planos de recorte ativos**. É possível criar mais componentes de plano de recorte, mas, se você tentar habilitar mais planos simultaneamente, ele ignorará a ativação. Desabilite os outros planos primeiro se desejar alternar quais componentes devem afetar a cena.
* Os planos de recorte são um recurso puramente visual, eles não afetam o resultado de [consultas espaciais](spatial-queries.md). Se você quiser projetar um raio em uma malha de corte aberto, é possível ajustar o ponto inicial do raio para estar no plano de recorte. Dessa forma, o raio só pode atingir as partes visíveis.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Cada plano de recorte ativo gera um pequeno custo durante a renderização. Desabilite ou exclua planos de recorte quando eles não forem necessários.

## <a name="api-documentation"></a>Documentação da API

* [Classe C# CutPlaneComponent](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Classe C++ CutPlaneComponent](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Próximas etapas

* [Renderização de lado único](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)