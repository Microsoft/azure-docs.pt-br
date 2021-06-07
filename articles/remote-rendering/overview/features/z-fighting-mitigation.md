---
title: Mitigação de Z-fighting
description: Descreve as técnicas para mitigar os artefatos de Z-fighting
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594054"
---
# <a name="z-fighting-mitigation"></a>Mitigação de Z-fighting

Quando duas superfícies se sobrepõem, não fica claro qual delas deve ser renderizada sobre a outra. O resultado até varia por pixel, resultando em artefatos dependentes da exibição da câmera. Consequentemente, quando a câmera ou a malha se move, esses padrões se deslocam visivelmente. Esse artefato é chamado *Z-fighting*. No caso de aplicativos de RA e RV, o problema é intensificado porque os dispositivos com capacete naturalmente sempre se movem. Para evitar desconforto do usuário, a funcionalidade de mitigação de Z-fighting está disponível no Azure Remote Rendering.

## <a name="z-fighting-mitigation-modes"></a>Modos de mitigação de Z-fighting

|Situação                        | Result                               |
|---------------------------------|:-------------------------------------|
|Z-fighting regular               |![Nenhuma precedência determinística entre os quádruplos vermelho e verde](./media/zfighting-0.png)|
|Mitigação de Z-fighting habilitada    |![Vermelho quad tem precedência](./media/zfighting-1.png)|
|Realce de padrão quadriculado habilitado|![Preferência de alternância quádrupla vermelha e verde no padrão quadriculado](./media/zfighting-2.png)|

O código a seguir habilita a mitigação do Z-fighting:

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> A mitigação do Z-fighting é uma configuração global que afeta todas as malhas renderizadas.

## <a name="reasons-for-z-fighting"></a>Motivos para Z-fighting

O Z-fighting ocorre principalmente por dois motivos:

1. Quando as superfícies estão longe da câmera, a precisão dos valores de profundidade diminui, e os valores se tornam indistinguíveis
1. quando as superfícies em uma malha se sobrepõem fisicamente

O primeiro problema sempre pode acontecer e é difícil de eliminar. Se isso acontecer em seu aplicativo, a proporção da distância do *próximo plano* à distância do *plano distante* deve ser tão baixa quanto prática. Por exemplo, um plano próximo à distância 0,01 e o plano distante na distância 1.000 criarão esse problema muito antes do que com o plano próximo a 0,1 e o plano distante na distância 20.

O segundo problema é um indicador de conteúdo criado incorretamente. No mundo real, dois objetos não podem estar no mesmo local ao mesmo tempo. Dependendo do aplicativo, os usuários talvez queiram saber se existem superfícies sobrepostas e onde elas estão. Por exemplo, uma cena do CAD de um edifício que é a base para uma construção do mundo real não deve conter interseções de superfície fisicamente impossíveis. Para permitir a inspeção visual, o modo de realce está disponível, que exibe o Z-fighting potencial como um padrão quadriculado animado.

## <a name="limitations"></a>Limitações

A mitigação de Z-fighting apresentada é um melhor esforço. Não há nenhuma garantia de que todo o Z-fighting seja removido. Além disso, ele preferirá automaticamente uma superfície sobre outra. Assim, quando você tem superfícies muito próximas entre si, a superfície "errada" pode acabar na parte superior. Um caso de problema comum é quando o texto e outros decals são aplicados a uma superfície. Com a mitigação do Z-fighting habilitada, esses detalhes podem simplesmente desaparecer.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* A habilitação da mitigação das sobreposições de texto gera pouca ou nenhuma sobrecarga de desempenho.
* Além disso, a habilitação da sobreposição do Z-fighting provoca uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena.

## <a name="api-documentation"></a>Documentação da API

* [Propriedade C# RenderingConnection. ZFightingMitigationSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection:: ZFightingMitigationSettings ()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>Próximas etapas

* [Modos de renderização](../../concepts/rendering-modes.md)
* [Reprojeção de fase tardia](late-stage-reprojection.md)