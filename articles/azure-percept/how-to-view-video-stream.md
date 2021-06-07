---
title: Exibir o fluxo de vídeo RTSP do Azure Percept DK
description: Saiba como exibir o fluxo de vídeo RTSP do Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095996"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Exibir o fluxo de vídeo RTSP do Azure Percept DK

Siga este guia para exibir o fluxo de vídeo RTSP do Azure Percept DK no Azure Percept Studio. Inferência modelos de ia de visão implantados em seu dispositivo poderão ser exibidos no fluxo da Web.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT

## <a name="view-the-rtsp-video-stream"></a>Exibir o fluxo de vídeo RTSP

1. Ligue o devkit.

1. Navegar para o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. No lado esquerdo da página Visão geral, clique em **dispositivos**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Tela de visão geral do Azure Percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Selecione seu devkit na lista.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Captura de tela dos dispositivos disponíveis no Azure Percept Studio.":::

1. Clique em **Exibir o fluxo do dispositivo**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Captura de tela da página do dispositivo mostrando as ações disponíveis do projeto de visão.":::

    Isso abre uma guia separada mostrando o fluxo da Web ao vivo do Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Captura de tela do fluxo da Web do dispositivo.":::

## <a name="next-steps"></a>Próximas etapas

Saiba como exibir a [telemetria do Azure PERCEPT DK](./how-to-view-telemetry.md).