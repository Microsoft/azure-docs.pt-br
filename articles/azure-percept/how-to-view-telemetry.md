---
title: Exibir a telemetria de inferência de modelo do Azure Percept DK
description: Saiba como exibir a telemetria de inferência do modelo de visão do Azure Percept DK no Azure IoT Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095316"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Exibir a telemetria de inferência de modelo do Azure Percept DK

Siga este guia para exibir a telemetria de inferência do modelo de visão do Azure Percept DK no [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT
- [O modelo de ia de visão foi implantado no Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Exibir telemetria

1. Ligue o devkit.

1. Baixe e instale o [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Se você for um usuário do Windows, selecione o arquivo. msi.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Tela de download do Azure IoT Explorer.":::

1. Conecte seu hub IoT ao Azure IoT Explorer:

    1. Acesse o [portal do Azure](https://portal.azure.com).

    1. Selecione **Todos os recursos**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Página inicial do portal do Azure.":::

    1. Selecione o Hub IoT ao qual seu Azure Percept DK está conectado.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Lista de Hub IoT no portal do Azure.":::

    1. No lado esquerdo da página do Hub IoT, selecione **políticas de acesso compartilhado**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="Página do Hub IoT mostrando políticas de acesso compartilhado.":::

    1. Clique em **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Tela de políticas de acesso compartilhado com iothubowner realçado.":::

    1. Clique no ícone de cópia azul ao lado da **cadeia de conexão — chave primária**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="janela iothubowner com o botão Copiar da cadeia de conexão realçado.":::

    1. Abra o Azure IoT Explorer e clique em **+ Adicionar conexão**.

    1. Cole a cadeia de conexão na caixa **cadeia de conexão** na janela **Adicionar cadeia de conexão** e clique em **salvar**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Janela do Gerenciador IOT do Azure com caixa para colar a cadeia de conexão em.":::

    1. Aponte o SoM de visão em um objeto para o modelo inferência.

    1. Selecione **telemetria**.

    1. Clique em **Iniciar** para exibir eventos de telemetria do dispositivo.

## <a name="next-steps"></a>Próximas etapas
Saiba como exibir o [fluxo de vídeo do Azure PERCEPT DK](./how-to-view-video-stream.md).