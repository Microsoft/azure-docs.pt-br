---
title: Implantar um modelo de ia de visão no Azure Percept DK
description: Saiba como implantar um modelo de ia de visão no Azure Percept DK do Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035476"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Implantar um modelo de ia de visão no Azure Percept DK

Siga este guia para implantar um modelo de ia de visão no Azure Percept DK de dentro do Azure Percept Studio.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT

## <a name="model-deployment"></a>Implantação de modelo

1. Ligue o devkit.

1. Navegar para o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. No lado esquerdo da página Visão geral, clique em **dispositivos**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Tela de visão geral do Azure Percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Selecione seu devkit na lista.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Lista de dispositivos Percept.":::

1. Na página seguinte, clique em **implantar um modelo de exemplo** se desejar implantar um dos modelos de visão de exemplo pré-treinados. Se você quiser implantar uma [solução de visão personalizada sem código](./tutorial-nocode-vision.md)existente, clique em **implantar um projeto de visão personalizada**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Opções de modelo para implantação.":::

1. Se você optou por implantar uma solução de visão sem código, selecione seu projeto e sua iteração de modelo preferencial e clique em **implantar**.

1. Se você optou por implantar um modelo de exemplo, selecione o modelo e clique em **implantar no dispositivo**.

1. Quando a implantação do modelo for bem-sucedida, você receberá uma mensagem de status no canto superior direito da tela. Para exibir seu modelo inferência em ação, clique no link **Exibir fluxo** na mensagem de status para ver o fluxo de vídeo RTSP do SoM de visão do seu devkit.

## <a name="next-steps"></a>Próximas etapas

Saiba como exibir a [telemetria do Azure PERCEPT DK](how-to-view-telemetry.md).