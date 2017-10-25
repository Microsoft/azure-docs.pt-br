---
title: "Introdução ao Azure Advisor | Microsoft Docs"
description: "Introdução ao Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como acessar o Assistente por meio do portal do Azure e obter, implementar, pesquisar e atualizar recomendações.

## <a name="get-advisor-recommendations"></a>Obter recomendações do Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **Mais serviços**.

3. No painel do menu de serviço, em **Monitoramento e Gerenciamento**, clique em **Assistente do Azure**.  
 O painel Assistente é exibido.

   ![Acessar o Azure Advisor usando o Portal do Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. No painel do Assistente, selecione a assinatura para a qual você deseja receber recomendações.  
O painel do Advisor exibe recomendações personalizadas para uma assinatura selecionada. 

5. Para obter recomendações para uma categoria específica, clique em uma das guias: **Alta Disponibilidade**, **Segurança**, **Desempenho** ou **Custo**.
 
> [!NOTE]
> Para acessar as recomendações do Assistente, você deve primeiro *registrar sua assinatura* no Assistente. Uma assinatura é registrada quando um *Proprietário de assinatura* inicia o painel do Assistente e clica no botão **Obter recomendações**. Essa é uma *operação única*. Depois que a assinatura for registrada, você poderá acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura, um grupo de recursos ou um recurso específico.

  ![Painel do Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obter detalhes da recomendação do Assistente e implementar uma recomendação

A folha **Recomendação** do Assistente oferece mais informações sobre a recomendação. 

1. Entre no [portal do Azure](https://portal.azure.com) e inicie o [Assistente do Azure](https://aka.ms/azureadvisordashboard).

2. No painel **Recomendações do Assistente**, clique em **Obter recomendações**.

3. Na lista de recomendações, clique em uma recomendação que você deseja examinar com detalhes.  
A folha **Recomendação** é exibida.

4. Na folha **Recomendações**, examine as informações sobre as ações que você pode executar para resolver um possível problema ou aproveite uma oportunidade de economia de custo. 
  
  ![A folha Recomendação do Assistente](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Procurar recomendações do Advisor

Procure recomendações sobre uma assinatura ou grupo de recursos específico. Você também pode procurar recomendações por status.

1. Entre no [portal do Azure](https://portal.azure.com) e inicie o [Assistente do Azure](https://aka.ms/azureadvisordashboard).

2. Procure recomendações filtrando por assinaturas, grupos de recursos e status da recomendação (**Ativa** ou **Adiada**).

3. Para exibir uma lista de recomendações do Assistente com base nos critérios do filtro de pesquisa, clique em **Obter recomendações**.

  ![Critérios do filtro de pesquisa do Assistente](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Adiar ou descartar as recomendações do Assistente

1. Entre no [portal do Azure](https://portal.azure.com) e inicie o [Assistente do Azure](https://aka.ms/azureadvisordashboard).

2. Clique em **Obter recomendações** e, na lista de recomendações, clique em uma recomendação.

3. Na folha **Recomendação**, clique em **Adiar**.  

   ![Exemplo de ação de recomendação do Advisor](./media/advisor-get-started/advisor-snooze.png)

4. Especifique uma hora de adiamento ou selecione **Nunca** para descartar a recomendação.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
