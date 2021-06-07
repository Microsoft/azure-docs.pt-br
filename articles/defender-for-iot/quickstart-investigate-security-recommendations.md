---
title: Investigar as recomendações de segurança
description: Investigue as recomendações de segurança com o serviço de segurança Defender para IoT.
ms.topic: quickstart
ms.date: 09/09/2020
ms.openlocfilehash: e7a9e8160c809fd53b681900b2283778b0023610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780966"
---
# <a name="quickstart-investigate-security-recommendations"></a>Início Rápido: Investigar recomendações de segurança


A análise oportuna e a mitigação de recomendações feitas pelo Defender para IoT é a melhor maneira de aprimorar a postura de segurança e reduzir a superfície de ataque na sua solução de IoT.

Neste guia de início rápido, exploraremos as informações disponíveis em cada recomendação de segurança da IoT e explicaremos como fazer busca detalhada e usar os detalhes de cada recomendação e os dispositivos relacionados a fim de reduzir o risco.

Vamos começar.

## <a name="investigate-new-recommendations"></a>Investigar novas recomendações

A lista de recomendações do Hub IoT exibe todas as recomendações de segurança agregadas do seu Hub IoT.

1.  No portal do Azure, abra o **Hub IoT** que você deseja investigar se há novas recomendações.

1.  No menu **Segurança**, selecione **Recomendações**. Todas as recomendações de segurança para o hub IoT serão exibidas. As recomendações com um sinalizador **Nova** marcam as recomendações das últimas 24 horas. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Investigar as recomendações de segurança com o ASC para IoT] (media/quickstart/investigate-security-recommendations-inline.png)":::


1.  Selecione e abra qualquer recomendação da lista para abrir os detalhes de recomendação e analisar detalhadamente as especificidades.

## <a name="security-recommendation-details"></a>Detalhes da recomendação de segurança

Abra cada recomendação agregada para exibir a descrição detalhada da recomendação, as etapas de correção, a ID do dispositivo de cada dispositivo que disparou uma recomendação. Isso também exibe a severidade da recomendação e o acesso de investigação direto usando o Log Analytics.

1.  Selecione e abra qualquer recomendação de segurança da lista **Hub IoT** > **Segurança** > **Recomendações**.

1.  Examine a **descrição** da recomendação, a **severidade**, os **detalhes do dispositivo** de todos os dispositivos que emitiram essa recomendação no período de agregação. 

1.  Depois de examinar as especificidades da recomendação, use as instruções da **etapa de correção manual** para ajudar a corrigir e resolver o problema que causou a recomendação. 

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="Corrigir recomendações de segurança com o ASC para IoT" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1.  Explore os detalhes da recomendação para um dispositivo específico selecionando o dispositivo desejado na página de busca detalhada.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="Investigar recomendações de segurança específicas para um dispositivo com ASC para IoT" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1.  Se for necessária uma investigação adicional, **investigue a recomendação no Log Analytics** usando o link. 

## <a name="next-steps"></a>Próximas etapas

Avance ao próximo artigo para aprender como criar alertas personalizados...

> [!div class="nextstepaction"]
> [Criar alertas personalizados](quickstart-create-custom-alerts.md)
