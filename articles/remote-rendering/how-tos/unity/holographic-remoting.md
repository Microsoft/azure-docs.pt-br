---
title: Usar o Remote Rendering e a comunicação remota holográfica no Unity
description: Como a visualização de comunicação remota do Holographic pode ser usada em combinação com a renderização remota do Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201810"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Usar o Remote Rendering e a comunicação remota holográfica no Unity

A comunicação remota do [Holographic](/windows/mixed-reality/holographic-remoting-player) e o processamento remoto do Azure são mutuamente exclusivos dentro de um aplicativo. Como tal, o [modo de reprodução do Unity](/windows/mixed-reality/unity-play-mode) também não está disponível.

Para cada execução do editor do Unity, apenas um dos dois pode ser usado. Para usar a outra, reinicie o Unity primeiro.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Usar o modo de reprodução do Unity para visualizar no HoloLens 2

 O modo de reprodução do Unity ainda pode ser usado, por exemplo, para testar a interface do usuário do aplicativo. No entanto, é vital que o ARR nunca seja inicializado. Caso contrário, ele falhará.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Usar um headset WMR VR para visualizar na área de trabalho

Se uma realidade misturada do Windows estiver presente em Headset, ela poderá ser usada para visualizar dentro do Unity. Nesse caso, é bom inicializar ARR, no entanto, não será possível se conectar a uma sessão enquanto o headset WMR for usado.