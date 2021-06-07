---
title: Mover as assinaturas de solução do Azure VMware EA e CSP
description: Saiba como mover a nuvem privada de uma assinatura para outra. A movimentação pode ser feita por vários motivos, como cobrança.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555225"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Mover as assinaturas de solução do Azure VMware EA e CSP

Neste artigo, você aprenderá a mover a nuvem privada de uma assinatura para outra. A movimentação pode ser feita por vários motivos, como cobrança. 

>[!IMPORTANT]
>Você deve ter pelo menos direitos de colaborador nas assinaturas de origem e de destino. O gateway vnet e VNet não podem ser movidos de uma assinatura para outra. Além disso, mover suas assinaturas não tem impacto sobre o gerenciamento e as cargas de trabalho, como o vCenter, o NSX e as máquinas virtuais de carga de trabalho.

1. Entre no portal do Azure e selecione a nuvem privada que você deseja mover.

1. Selecione o link **assinatura (alterar)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Captura de tela mostrando os detalhes da nuvem privada.":::

1. Forneça os detalhes da assinatura para **destino** e selecione **Avançar**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Captura de tela do recurso de destino." lightbox="media/move-resources-subscription-target.png":::

1. Confirme a validação dos recursos que você selecionou para mover e selecione **Avançar**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Captura de tela mostrando o recurso que está sendo movido." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Marque a caixa de seleção indicando que você entendeu que as ferramentas e os scripts associados não funcionarão até que você os atualize para usar as novas IDs de recurso. Em seguida, selecione **mover**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Captura de tela mostrando o resumo do recurso selecionado que está sendo movido. " lightbox="media/review-move-resources-subscription-target.png":::

   Uma notificação é exibida depois que a movimentação de recursos é concluída. A nova assinatura é exibida na visão geral da nuvem privada.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Captura de tela mostrando uma nova assinatura." lightbox="media/moved-subscription-target.png":::

