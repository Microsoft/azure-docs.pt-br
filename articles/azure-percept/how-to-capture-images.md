---
title: Capturar imagens para uma solução de visão sem código no Azure Percept Studio
description: Saiba como capturar imagens com o Azure Percept DK no Azure Percept Studio para uma solução de visão sem código
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d6cece6ee3079ba9f400f40026ca26ea36668710
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024635"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Capturar imagens de um projeto de visão no Azure Percept Studio

Siga este guia para capturar imagens usando o SoM de visão do Azure Percept DK para um projeto de visão existente no Azure Percept Studio. Se você ainda não criou um projeto de visão, consulte o [tutorial visão sem código](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT
- [Projeto de visão sem código](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Capturar imagens

1. Ligue o devkit.

1. Navegar para o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. No lado esquerdo da página Visão geral, clique em **dispositivos**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Tela de visão geral do Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Selecione seu devkit na lista.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Lista de dispositivos Percept.":::

1. Na página do seu dispositivo, clique em **capturar imagens para um projeto**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Página dispositivos Percept com as ações disponíveis listadas.":::

1. Na janela **captura de imagem** , faça o seguinte:

    1. No menu suspenso **projeto** , selecione o projeto de visão para o qual você gostaria de coletar imagens.

    1. Clique em **Exibir fluxo do dispositivo** para garantir que a câmera do SoM da visão seja colocada corretamente.

    1. Clique em **tirar foto** para capturar uma imagem.

    1. Como alternativa, marque a caixa ao lado de **captura de imagem automática** para configurar um temporizador para captura de imagem:

        1. Selecione sua taxa de geração de imagens preferida na **taxa de captura**.
        1. Selecione o número total de imagens que você gostaria de coletar no **destino**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Tela de captura de imagem.":::

Todas as imagens estarão acessíveis em [visão personalizada](https://www.customvision.ai/).

## <a name="next-steps"></a>Próximas etapas

[Teste e retreine seu modelo de visão sem código](../cognitive-services/custom-vision-service/test-your-model.md).