---
title: Escalar a capacidade do Power BI Embedded | Microsoft Docs
description: Este artigo explica como escalar uma capacidade do Power BI Embedded no Microsoft Azure.
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
ms.openlocfilehash: e1ab6a2f52fa56f1e04c6c327796587daf43596e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Escalar a capacidade do Power BI Embedded

Este artigo explica como escalar uma capacidade do Power BI Embedded no Microsoft Azure. O dimensionamento permite aumentar ou diminuir o tamanho de sua capacidade.

Isso pressupõe que você tenha criado uma capacidade do Power BI Embedded. Se não tiver criado, consulte [Criar uma capacidade do Power BI Embedded no Portal do Azure](create-capacity.md) para começar.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="scale-a-capacity"></a>Escalar uma capacidade

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Mais serviços** > **Power BI Embedded** para ver suas capacidades.

    ![Mais serviços no Portal do Azure](media/scale-capacity/azure-portal-more-services.png)

3. Selecione a capacidade que deseja escalar.

    ![Lista de capacidade do Power BI Embedded no Portal do Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Selecione **Tipo de preço** em **Escalar** na capacidade.

    ![Opção Tipo de preço em Escalar](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    O tipo de preço atual está contornado em azul.

    ![Tipo de preço atual contornado em azul](media/scale-capacity/azure-portal-current-tier.png)

5. Para escalar verticalmente ou reduzir verticalmente, selecione o novo tipo para o qual mover. Selecionar uma nova camada coloca um contorno pontilhado azul ao redor da seleção. Selecione **Selecionar** para escalar o novo tipo.

    ![Selecionar o novo tipo](media/scale-capacity/azure-portal-select-new-tier.png)

    A escala da capacidade pode demorar um minuto ou dois para ser concluída.

6. Confirme o tipo exibindo a guia de visão geral. O tipo de preço atual é listado.

    ![Confirmar o tipo atual](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Próximas etapas

Para pausar ou iniciar sua capacidade, consulte [Pausar e iniciar a capacidade do Power BI Embedded no Portal do Azure](pause-start.md).

Para começar a inserir conteúdo do Power BI em seu aplicativo, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)