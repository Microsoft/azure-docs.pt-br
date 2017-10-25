---
title: Prever despesas com o Gerenciamento de Custos do Azure | Microsoft Docs
description: "Prever despesas usando o histórico de uso e os dados de gastos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/29/2017
ms.topic: article
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: decae695544e8b9e0ff48cca32a38f533a86a563
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="forecast-future-spending"></a>Prever despesas futuras

O Gerenciamento de Custos do Azure da Cloudyn ajuda você a prever as despesas futuras usando o histórico de uso e os dados de gastos. Você pode usar os relatórios da Cloudyn para exibir todos os dados de projeção de custos. Os exemplos neste tutorial orientarão na revisão das projeções de custos usando os relatórios. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prever despesas futuras

## <a name="forecast-future-spending"></a>Prever despesas futuras

A Cloudyn inclui relatórios de projeção de custos para ajudá-lo a prever os gastos com base no seu uso ao longo do tempo. A finalidade principal dos relatórios é ajudá-lo a garantir que as tendências de custos não excedam as expectativas de sua organização. Os relatórios que você usa são o Custo projetado do mês atual e Custo projetado anual. Ambos mostram a projeção dos gastos futuros se o seu uso permanece relativamente consistente nos últimos 30 dias de uso.

O relatório de Custo projetado do mês atual mostra os custos dos seus serviços. Ele usa os custos desde o início do mês e os do mês anterior para mostrar o custo projetado. No menu de relatórios, na parte superior do portal, clique em **Custo** > **Projeção e Orçamento** > **Custo Projetado do Mês Atual**. A imagem a seguir mostra um exemplo.

![custo projetado do mês atual](./media/tutorial-forecast-spending/project-month01.png)

No exemplo, você pode ver quais serviços gastaram mais. Os custos do Azure foram menores do que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![Custo projetado do mês atual de VM do Azure](./media/tutorial-forecast-spending/project-month02.png)

Siga as mesmas etapas básicas anteriores para ver projeções de custo mensais de outros serviços nos quais você tenha interesse.

O relatório de Custo projetado anual mostra uma extrapolação do custo de seus serviços nos próximos 12 meses.

No menu de relatórios, na parte superior do portal, clique em **Custo** > **Projeção e Orçamento** > **Custo Projetado Anual**. A imagem a seguir mostra um exemplo.

![relatório de custo projetado anual](./media/tutorial-forecast-spending/project-annual01.png)

No exemplo, você pode ver quais serviços gastaram mais. Assim como no exemplo mensal, os custos do Azure foram menores que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![custo projetado anual de VMs](./media/tutorial-forecast-spending/project-annual02.png)

Na imagem acima, o custo projetado anual das VMs do Azure é de $28.374.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Prever despesas futuras


Acesse a documentação da Cloudyn para saber mais sobre como começar com a Cloudyn e como usar seus recursos.

> [!div class="nextstepaction"]
> [Documentação da Cloudyn](https://support.cloudyn.com/hc/)
