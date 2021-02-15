---
title: Câmera
description: Descreve as configurações de câmera e os casos de uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594122"
---
# <a name="camera"></a>Câmera

Para garantir que o conteúdo renderizado local e remotamente possa ser composto de forma direta, várias propriedades de câmera precisam permanecer sincronizadas entre o servidor e o cliente. Mais notavelmente, a transformação e a projeção da câmera. Por exemplo, o conteúdo processado localmente deve usar a mesma transformação de câmera e projeção com a qual o quadro remoto mais recente foi renderizado.

![Câmera local e remota](./media/camera.png)

Na imagem acima, a câmera local foi movida em comparação com a estrutura remota enviada pelo servidor. Como resultado, o conteúdo local precisa ser processado da mesma perspectiva que o quadro remoto e, por fim, a imagem resultante é reprojetada no espaço da câmera local, o que é explicado em detalhes na [Reprojeção de estágio atrasado](late-stage-reprojection.md).

O atraso entre a renderização remota e local significa que qualquer alteração dessas configurações é aplicada com um atraso. Portanto, qualquer valor novo não pode ser usado imediatamente no mesmo quadro.

As distâncias do plano próximo e longe podem ser definidas nas configurações da câmera. No HoloLens 2, os dados de transformação e de projeção restantes são definidos pelo hardware. Ao usar a [simulação de área de trabalho](../../concepts/graphics-bindings.md), elas são definidas por meio da entrada de sua `Update` função.

Os valores alterados podem ser usados localmente assim que o primeiro quadro remoto chegar que foi renderizado com eles. No HoloLens 2, os dados a serem usados para renderização são fornecidos pelo *HolographicFrame* , assim como em qualquer outro aplicativo Holographic. Na [simulação de área de trabalho](../../concepts/graphics-bindings.md), eles são recuperados por meio da saída da `Update` função.

## <a name="camera-settings"></a>Configurações da câmera

As propriedades a seguir podem ser alteradas nas configurações da câmera:

**Plano próximo e longe:**

Para certificar-se de que nenhum intervalo inválido possa ser definido, as propriedades **NearPlane** e **FarPlane** são somente leitura e uma função separada **SetNearAndFarPlane** existe para alterar o intervalo. Esses dados serão enviados para o servidor no final do quadro. Ao definir esses valores, **NearPlane** precisa ser menor que **FarPlane**. Caso contrário, ocorrerá um erro.

> [!IMPORTANT]
> No Unity, isso é manipulado automaticamente ao alterar os planos próximos e distantes da câmera principal.

**EnableDepth**:

Às vezes, é útil desabilitar a gravação do buffer de profundidade da imagem remota para fins de depuração. A desabilitação da profundidade também impedirá que o servidor envie dados de profundidade, reduzindo assim a largura de banda.

> [!TIP]
> No Unity, é fornecido um componente de depuração chamado **EnableDepthComponent** que pode ser usado para alternar esse recurso na interface do usuário do editor.

**InverseDepth**:

> [!NOTE]
> Essa configuração só será importante se o `EnableDepth` for definido como `true` . Caso contrário, essa configuração não terá nenhum impacto.

Buffers de profundidade normalmente registram valores z em um intervalo de ponto flutuante de [0; 1], com 0 denotando a profundidade de plano próximo e 1 denotando a profundidade do plano distante. Também é possível inverter esse intervalo e registrar valores de profundidade no intervalo [1; 0], ou seja, a profundidade de plano próximo se torna 1 e a profundidade do plano distante se torna 0. Em geral, o último melhora a distribuição da precisão de ponto flutuante no intervalo z não linear.

> [!WARNING]
> Uma abordagem comum é inverter os valores de plano próximo e de muito plano nos objetos da câmera. Isso irá falhar para a renderização remota do Azure com um erro ao tentar isso no `CameraSettings` .

A API de renderização remota do Azure precisa saber sobre a Convenção de buffer de profundidade do seu renderizador local para compor corretamente a profundidade remota no buffer de profundidade local. Se o intervalo do buffer de profundidade for [0; 1], deixe esse sinalizador como `false` . Se você usar um buffer de profundidade invertido com um intervalo de [1; 0], defina o `InverseDepth` sinalizador como `true` .

> [!NOTE]
> Para o Unity, a configuração correta já está aplicada pelo `RenderingConnection` , portanto, não há necessidade de intervenção manual.

A alteração das configurações da câmera pode ser feita da seguinte maneira:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Documentação da API

* [CameraSettings C#](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [Função C# GraphicsBindingSimD3d11. Update](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [Função C++ GraphicsBindingSimD3d11:: Update](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Próximas etapas

* [Associações de gráficos](../../concepts/graphics-bindings.md)
* [Reprojeção de fase tardia](late-stage-reprojection.md)