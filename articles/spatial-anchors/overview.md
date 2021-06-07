---
title: Visão geral de Âncoras Espaciais do Azure
description: Saiba como as Âncoras Espaciais do Azure ajudam a desenvolver experiências multiplataforma de realidade misturada.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0b4c3b281f2eac6263ef578dee5419bd9f2eb8a7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058554"
---
# <a name="azure-spatial-anchors-overview"></a>Visão geral de Âncoras Espaciais do Azure

Bem-vindo a Âncoras Espaciais do Azure. Âncoras Espaciais do Azure capacita os desenvolvedores com recursos essenciais para criar aplicativos de realidade misturada com reconhecimento do ambiente. Esses aplicativos podem dar suporte a Microsoft HoloLens, dispositivos baseados em iOS compatíveis com ARKit e dispositivos baseados no Android que dão suporte a ARCore. Âncoras Espaciais do Azure permite aos desenvolvedores trabalhar com plataformas de realidade misturada para perceber espaços, designar pontos precisos de interesse e lembrar-se dos pontos de interesse de dispositivos com suporte.
Esses pontos de interesse precisos são chamados de Âncoras Espaciais.

![Multiplataforma](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de uso de exemplo habilitados por Âncoras Espaciais incluem:

- [Experiências multiusuários](tutorials/tutorial-share-anchors-across-devices.md). O recurso Âncoras Espaciais do Azure torna mais fácil para pessoas no mesmo lugar participarem de aplicativos de realidade misturada multiusuário. Por exemplo, duas pessoas podem iniciar um jogo de xadrez de realidade misturada colocando um tabuleiro de xadrez virtual em uma tabela. Em seguida, apontando o dispositivo para a tabela, elas podem exibir e interagir com o tabuleiro de xadrez virtual juntas.

- [Localização de caminho](concepts/anchor-relationships-way-finding.md). Os desenvolvedores também podem conectar Âncoras Espaciais criando relações entre elas. Por exemplo, um aplicativo pode incluir uma experiência com dois ou mais pontos de interesse com que um usuário deve interagir para concluir uma tarefa. Esses pontos de interesse podem ser criados de maneira conectada. Posteriormente, quando o usuário estiver concluindo a tarefa de várias etapas, o aplicativo poderá solicitar âncoras que estejam próximas da atual para direcionar o usuário para a próxima etapa na tarefa.

- [Manter o conteúdo virtual no mundo real](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Um aplicativo pode permitir que um usuário coloque um calendário virtual em uma parede de sala de conferência que as pessoas possam ver usando um aplicativo de telefone ou dispositivo HoloLens. Em uma configuração industrial, um usuário pode receber informações contextuais sobre um computador apontando uma câmera de dispositivos com suporte para ele.

O recurso Âncoras Espaciais do Azure é composto por um serviço gerenciado e os SDKs do cliente para plataformas de dispositivos com suporte. As seções a seguir fornecem informações sobre como começar a criar aplicativos usando Âncoras Espaciais do Azure.

## <a name="next-steps"></a>Próximas etapas

Criar seu primeiro aplicativo com Âncoras Espaciais do Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
