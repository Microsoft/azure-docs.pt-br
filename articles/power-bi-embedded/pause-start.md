---
title: Pausar e iniciar a capacidade do Power BI Embedded no Portal do Azure | Microsoft Docs
description: Este artigo explica como pausar e iniciar uma capacidade do Power BI Embedded no Microsoft Azure.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Pausar e iniciar a capacidade do Power BI Embedded no Portal do Azure

Este artigo explica como pausar e iniciar uma capacidade do Power BI Embedded no Microsoft Azure. Isso pressupõe que você tenha criado uma capacidade do Power BI Embedded. Se não tiver criado, consulte [Criar uma capacidade do Power BI Embedded no Portal do Azure](create-capacity.md) para começar.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="pause-your-capacity"></a>Pausar sua capacidade

Pausar a capacidade evita que você seja cobrado. Pausar sua capacidade é ótimo se você não precisa usá-la por um período. Use as etapas a seguir para pausar a capacidade.

> [!NOTE]
> Pausar uma capacidade pode impedir que o conteúdo fique disponível no Power BI. Certifique-se de remover a atribuição de espaços de trabalho da sua capacidade antes de pausar para evitar a interrupção.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Mais serviços** > **Power BI Embedded** para ver suas capacidades.

    ![Mais serviços no Portal do Azure](media/pause-start/azure-portal-more-services.png)

3. Selecione a capacidade que deseja pausar.

    ![Lista de capacidade do Power BI Embedded no Portal do Azure](media/pause-start/azure-portal-capacity-list.png)

4. Selecione **Pausar** nos detalhes da capacidade.

    ![Pausar sua capacidade](media/pause-start/azure-portal-pause-capacity.png)

5. Selecione **Sim**, o que confirma que você deseja pausar a capacidade.

    ![Confirmar pausar](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Iniciar sua capacidade

Retome o uso iniciando sua capacidade. Iniciar sua capacidade também retoma a cobrança.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Mais serviços** > **Power BI Embedded** para ver suas capacidades.

    ![Mais serviços no Portal do Azure](media/pause-start/azure-portal-more-services.png)

3. Selecione a capacidade que deseja iniciar.

    ![Lista de capacidade do Power BI Embedded no Portal do Azure](media/pause-start/azure-portal-capacity-list.png)

4. Selecione **Iniciar** nos detalhes da capacidade.

    ![Iniciar sua capacidade](media/pause-start/azure-portal-start-capacity.png)

5. Selecione **Sim**, o que confirma que você deseja iniciar a capacidade.

    ![Confirmar início](media/pause-start/azure-portal-confirm-start.png)

Se algum conteúdo estiver atribuído a essa capacidade, ele estará disponível após o início.

## <a name="next-steps"></a>Próximas etapas

Se você deseja escalar ou reduzir verticalmente sua capacidade, consulte [Escalar a capacidade do Power BI Embedded](scale-capacity.md).

Para começar a inserir conteúdo do Power BI em seu aplicativo, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)