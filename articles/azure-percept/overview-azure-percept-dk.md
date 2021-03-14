---
title: Visão geral do Azure Percept DK
description: Saiba mais sobre o Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c93ecdc138e757f84e995a69a6c3d7f1b5460abe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179367"
---
# <a name="azure-percept-dk-overview"></a>Visão geral do Azure Percept DK

O Azure Percept DK é um kit de ia e desenvolvimento de IoT Edge projetado para o desenvolvimento de conceitos de provas de visão e de áudio ia. Quando combinado com o [Azure Percept Studio](./overview-azure-percept-studio.md) e o [áudio do Azure Percept](./overview-azure-percept-audio.md), ele se torna uma plataforma poderosa, mas simples de usar, para a criação de soluções de ia de borda para uma ampla gama de aplicativos de AI ou de áudio ia. Ele está disponível para compra na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Dispositivo Azure Percept DK.":::

## <a name="key-features"></a>Principais recursos

- **A capacidade de executar o ia na borda**. Com a aceleração de hardware interna, ele pode executar modelos de ia de visão sem uma conexão com a nuvem.
- **Raiz de hardware da segurança de confiança interna**. Consulte esta visão geral do [Azure Percept Security](./overview-percept-security.md) para obter mais detalhes.
- **Integração direta com o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)** e outros serviços do Azure. Como o Hub IoT do Azure, os serviços cognitivas do Azure e a [análise de vídeo ao vivo](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Integração direta com o [áudio opcional do Azure Percept](./overview-azure-percept-audio.md)**
- **Suporte para as principais plataformas de ia**. Como ONNX e TensorFlow.
- **Integração com o sistema de trilhos 80/20**. Facilitando a criação de protótipos em ambientes de produção. Saiba mais sobre a [integração 80/20](./overview-8020-integration.md).

## <a name="hardware-components"></a>Componentes de hardware

- A placa de transporte do Azure Percept DK
    - Processador NXP iMX8m
    - Trusted Platform Module (TPM) versão 2,0
    - Conectividade Wi-Fi e Bluetooth
    - Veja a [folha de dados](./azure-percept-dk-datasheet.md) completa
- O sistema do Azure Percept Vision no módulo (SoM)
    - Movidius (unidade de processamento de visão MA2085) Intel de infinidade de X (VPU)
    - Sensor de câmera RGB com a capacidade de adicionar um segundo
    - Veja a [folha de dados](./azure-percept-vision-datasheet.md) completa

## <a name="get-started-with-the-azure-percept-dk"></a>Introdução ao Azure Percept DK

- Concluir estes inícios rápidos
    - [Unbox e monte o Percept do Azure DK](./quickstart-percept-dk-unboxing.md)
    - [Configurar o Azure Percept DK e executar seu primeiro modelo de ia de visão](./quickstart-percept-dk-set-up.md)
- Comece a criar provas de conceitos com estes tutoriais
    - [Criar uma solução de pesquisa visual sem código no Azure Percept Studio](./tutorial-nocode-vision.md)
    - [Criar um assistente de voz no Azure Percept Studio](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Próximas etapas

Solicite um Azure Percept DK na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
