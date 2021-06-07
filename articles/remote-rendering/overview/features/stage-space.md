---
title: Preparar espaço
description: Descreve as configurações de espaço de estágio e casos de uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594003"
---
# <a name="stage-space"></a>Preparar espaço

Ao executar o ARR em um dispositivo que fornece dados de acompanhamento de cabeçalho como o HoloLens 2, a pose de cabeçalho é enviada para o aplicativo do usuário e para o servidor. O espaço no qual a transformação de cabeçalho é definida é chamado de *espaço de estágio*.

Para alinhar o conteúdo local e remoto, supõe-se que o espaço do estágio e o espaço do mundo sejam idênticos no cliente e no servidor. Se o usuário decidir adicionar uma transformação adicional sobre a câmera, ela precisará ser enviada ao servidor também para alinhar o conteúdo local e remoto corretamente.

Os motivos típicos para mover o espaço de preparo são [ferramentas de bloqueio mundial](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) ou outras técnicas de ancoragem do mundo real, bem como a movimentação de um caractere virtual no VR.

## <a name="stage-space-settings"></a>Configurações de espaço de preparo

> [!CAUTION]
> EXPERIMENTAL: esse recurso é experimental e será alterado ao longo do tempo. Assim, a atualização para versões mais recentes do SDK do cliente pode exigir trabalho adicional para atualizar o código. A implementação atual interromperá o alinhamento de conteúdo local/remoto por um breve momento ao alterar a origem do espaço de estágio.
Portanto, no momento, ele só deve ser usado para fins de bloqueio mundial, como âncoras que exibem apenas alterações muito pequenas ao longo do tempo.

Para informar ao servidor que uma transformação adicional é aplicada ao espaço de preparo, sua origem definida por uma posição e uma rotação no espaço do mundo precisam ser enviadas. Essa configuração pode ser acessada por meio da *configuração de espaço de estágio*.

> [!IMPORTANT]
> Na [simulação de desktop](../../concepts/graphics-bindings.md) , o local do espaço Mundial da câmera é fornecido pelo aplicativo do usuário. Nesse caso, definir a origem do espaço de estágio deve ser ignorado, pois ele já está multiplicado na transformação da câmera.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Script de espaço de estágio do Unity

A integração do Unity fornece um script chamado **StageSpace** que pode ser adicionado ao gameobject pai da câmera. Uma vez presente, esse script cuidará da definição da origem do espaço de preparo.

## <a name="next-steps"></a>Próximas etapas

* [Associações de gráficos](../../concepts/graphics-bindings.md)
* [Reprojeção de fase tardia](late-stage-reprojection.md)
