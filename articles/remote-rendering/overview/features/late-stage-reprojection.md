---
title: Reprojeção de fase tardia
description: Informações sobre a Reprojeção de estágio atrasado e como usá-la.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: f0951415bba22a226dadb7f2a115cede451399bc
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205635"
---
# <a name="late-stage-reprojection"></a>Reprojeção de fase tardia

A *Reprojeção de estágio tardia* (LSR) é um recurso de hardware que ajuda a estabilizar os hologramas quando o usuário se move.

Os modelos estáticos são esperados para manter suas posições visualmente quando você se movimenta ao seu respeito. Se parecerem instáveis, esse comportamento pode ser indício de problemas de LSR. Lembre-se de que as transformações dinâmicas adicionais, como animações ou exibições de explosão, podem mascarar esse comportamento.

Você pode escolher entre dois modos de LSR diferentes, ou seja, **planar LSR** ou **Depth LSR**. Qual deles está ativo é determinado pelo fato de o aplicativo cliente enviar um buffer de profundidade.

Ambos os modos LSR melhoram a estabilidade do holograma, embora tenham suas limitações distintas. Comece experimentando LSR de profundidade, pois isso é, sem dúvida, dando melhores resultados na maioria dos casos.

## <a name="choose-lsr-mode-in-unity"></a>Escolha o modo LSR no Unity

No editor do Unity, vá para *:::no-loc text="File > Build Settings":::* . Selecione *:::no-loc text="Player Settings":::* na parte inferior esquerda e, em seguida, verifique *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* se **:::no-loc text="Enable Depth Buffer Sharing":::** está marcado:

![Sinalizador de compartilhamento de buffer de profundidade habilitado](./media/unity-depth-buffer-sharing-enabled.png)

Se for, seu aplicativo usará LSR de profundidade, caso contrário, ele usará o planar LSR.

## <a name="depth-lsr"></a>LSR de profundidade

Para que a LSR de profundidade funcione, o aplicativo cliente deve fornecer um buffer de profundidade válido que contenha toda a geometria relevante a ser considerada durante o LSR.

Profundidade LSR tenta estabilizar o quadro de vídeo com base no conteúdo do buffer de profundidade fornecido. Como consequência, o conteúdo que não foi renderizado para ele, como objetos transparentes, não pode ser ajustado por LSR e pode mostrar artefatos de instabilidade e de Reprojeção. 

Para reduzir a instabilidade de Reprojeção para objetos transparentes, você pode forçar a gravação de buffer de profundidade. Consulte o sinalizador de material *TransparencyWritesDepth* para os materiais de [cor](color-materials.md) e [PBR](pbr-materials.md) . No entanto, observe que a qualidade visual da interação do objeto transparente/opaco pode ser prejudicada ao habilitar esse sinalizador.

## <a name="planar-lsr"></a>LSR planar

O planar LSR não tem informações de profundidade por pixel, como a profundidade LSR. Em vez disso, ele reprojeta todo o conteúdo com base em um plano que você deve fornecer a cada quadro.

O planar LSR reprojeta esses objetos melhor que se aproximam do plano fornecido. Quanto mais distante um objeto for, mais instável será a aparência. Embora a profundidade LSR seja melhor na Reprojeção de objetos em diferentes profundidades, o planar LSR pode funcionar melhor para conteúdo com alinhamento bem com um plano.

### <a name="configure-planar-lsr-in-unity"></a>Configurar o planar LSR no Unity

Os parâmetros de plano são derivados de um, portanto, chamado de *ponto de foco*, que você precisa fornecer a cada quadro `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Consulte a [API do ponto de foco do Unity](/windows/mixed-reality/focus-point-in-unity) para obter detalhes. Se você não definir um ponto de foco, um fallback será escolhido para você. No entanto, o fallback automático geralmente leva a resultados de qualidade inferior.

Você pode calcular o ponto de foco por conta própria, embora possa fazer sentido baseá-lo em um calculado pelo host de renderização remoto. Chame `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obtê-lo. Você será solicitado a fornecer um quadro de coordenadas no qual expressar o ponto de foco. Na maioria dos casos, você só desejará fornecer o resultado `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` aqui.

Geralmente, o cliente e o host renderizam conteúdo que o outro lado não reconhece, como elementos de interface do usuário no cliente. Portanto, pode fazer sentido combinar o ponto de foco remoto com um calculado localmente.

Os pontos de foco calculados em dois quadros sucessivos podem ser bem diferentes. Simplesmente usá-los como estão pode fazer com que os hologramas pareçam estar indo em frente. Para evitar esse comportamento, a interpolação entre os pontos de foco anteriores e atuais é aconselhável.

## <a name="next-steps"></a>Próximas etapas

* [Consultas de desempenho no lado do servidor](performance-queries.md)