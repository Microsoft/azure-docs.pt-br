---
title: Criar um aplicativo acessível com o Azure Mapas | Microsoft Docs
description: Como criar um aplicativo acessível usando o Azure Mapas
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769695"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Este artigo mostra como criar um aplicativo de mapa que pode ser usado por leitores de tela.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Criar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Criar um aplicativo acessível</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O mapa é pré-compilado com alguns recursos de acessibilidade. Os usuários podem navegar o mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário de alterações em seu estado.
Por exemplo, os usuários serão notificados das alterações do mapa quando o mapa é deslocado ou ampliado. Qualquer informação adicional é colocada no mapa de base deve ter informações textuais correspondentes para os usuários de leitores de tela.

Usar o [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) é uma maneira de fazer isso. No exemplo acima de pesquisa, um pop-up com informações textuais é adicionado ao mapa para cada marcador colocado no mapa. Usar o método anexar[do pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [anexar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) permite que o popup seja visto por um leitor de tela sem exibir visualmente o pop-up no mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos de pop-up usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Confira mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
