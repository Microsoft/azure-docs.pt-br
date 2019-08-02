---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172539"
---
Um experimento é executado em um destino de computação, um recurso de computação anexado ao workspace.  Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **Executar** na parte inferior para executar o teste.

     ![Executar o experimento](./media/aml-ui-create-training-compute/run-experiment.png)

1. Quando a caixa de diálogo **Configurar Destinos de Computação** for exibida, se o workspace já tiver um recurso de computação, você poderá selecioná-lo agora.  Caso contrário, selecione **Criar**.

    > [!NOTE]
    > A interface visual só pode executar experimentos em destinos de Computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Executar**.

    ![Configurar o destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

    O recurso de computação agora será criado. Veja o status no canto superior direito do experimento. 

    > [!NOTE]
    > São necessários aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação será dimensionado automaticamente para 0 nó quando estiver ocioso para economia de custos.  Quando você o usar novamente após um atraso, talvez precise aguardar aproximadamente 5 minutos enquanto ele é escalado verticalmente mais uma vez.
