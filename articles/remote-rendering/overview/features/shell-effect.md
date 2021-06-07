---
title: Renderização do shell
description: Explica como usar o efeito de renderização do Shell
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594156"
---
# <a name="shell-rendering"></a>Renderização do shell

O estado do shell do [componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md) é um efeito de transparência. Ao contrário da renderização de [Visualização](../../overview/features/override-hierarchical-state.md) , somente a camada de objetos na frente é visível, semelhante à renderização opaca. Além disso, a aparência normal dos objetos pode ser alterada quando renderizado como shells. O efeito é destinado a casos de uso em que o usuário deve ser visualmente voltado para fora de partes não importantes e ainda manter o reconhecimento espacial de toda a cena.

Você pode configurar a aparência de objetos renderizados pelo shell por meio do `ShellRenderingSettings` estado global. Todos os objetos que usam a renderização de shell usarão a mesma configuração. Não há parâmetros por objeto.

## <a name="shellrenderingsettings-parameters"></a>Parâmetros de ShellRenderingSettings

`ShellRenderingSettings`A classe contém as configurações relacionadas às propriedades de renderização do Shell global:

| Parâmetro      | Type    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | FLOAT   | A quantidade de dessaturação a ser aplicada à cor comum do objeto final, no intervalo 0 (sem dessaturação) a 1 (dessaturação completa) |
| `Opacity`      | FLOAT   | A opacidade dos objetos renderizados pelo shell, no intervalo 0 (invisível) a 1 (totalmente opaco) |

Consulte também a tabela a seguir para obter exemplos dos efeitos dos parâmetros quando aplicados a uma cena inteira:

|                | 0 | 0,25 | 0.5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Dessaturação** | ![Dessaturação-0,0](./media/shell-desaturation-00.png) | ![Dessaturação-0,25](./media/shell-desaturation-025.png) | ![Dessaturação-0,5](./media/shell-desaturation-05.png) | ![Dessaturação-0,75](./media/shell-desaturation-075.png) | ![Dessaturação-1,0](./media/shell-desaturation-10.png) |
| **Opacidade**      | ![Opacidade-0,0](./media/shell-opacity-00.png) | ![Opacidade-0,25](./media/shell-opacity-025.png) | ![Opacidade-0,5](./media/shell-opacity-05.png) | ![Opacidade-0,75](./media/shell-opacity-075.png) | ![Opacidade-1,0](./media/shell-opacity-10.png) |

O efeito do Shell é aplicado na cor opaca final que a cena seria renderizada com o contrário. Isso inclui a [substituição do estado hierárquico da tonalidade](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Exemplo

O código a seguir mostra um exemplo de uso do `ShellRenderingSettings` estado por meio da API:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Desempenho

O recurso de renderização do Shell traz uma pequena sobrecarga constante em comparação com a renderização opaca padrão. É significativamente mais rápido do que usar materiais transparentes em objetos ou [ver a](../../overview/features/override-hierarchical-state.md) renderização. O desempenho pode degradar mais de segurança se apenas partes da cena forem alternadas para a renderização do Shell. Essa degradação pode ocorrer devido a objetos revelados adicionalmente que exigem renderização. Nesse aspecto, o desempenho se comporta de forma semelhante ao recurso de [planos de corte](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Próximas etapas

* [Componente de substituição do estado hierárquico](../../overview/features/override-hierarchical-state.md)