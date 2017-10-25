---
title: "Recomendações de custo do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para otimizar o custo de suas implantações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>Recomendações de custo do Advisor

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

![Guia Custo do Assistente](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando instâncias subutilizadas 
Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. O Assistente monitora o uso da máquina virtual durante 14 dias e, depois, identifica as máquinas virtuais com baixa utilização. As máquinas virtuais cuja utilização da CPU é de 5% ou menos e cujo uso de rede é de 7 MB ou menos durante quatro ou mais dias são consideradas máquinas virtuais de baixa utilização.

O Assistente mostra o custo estimado da continuação da execução da máquina virtual, de forma que você possa optar por desligá-la ou redimensioná-la.  

![Recomendações de custo do Advisor para redimensionamento de máquinas virtuais](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Usar uma solução econômica para gerenciar as metas de desempenho de vários bancos de dados SQL
O Advisor identifica as instâncias do SQL Server que podem se beneficiar da criação de pools de banco de dados elástico. Pools de banco de dados elástico fornecem uma solução simples e econômica para gerenciar as metas de desempenho de vários bancos de dados que têm padrões de uso variáveis. Para obter mais informações sobre os pools elásticos do Azure, consulte [O que é um pool elástico do Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

![Recomendações de custo do Advisor para pools de banco de dados elástico](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Azure Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **Mais serviços**.

3. No painel do menu de serviço, em **Monitoramento e Gerenciamento**, clique em **Assistente do Azure**.  
 O painel Assistente é exibido.

4. No painel do Assistente, clique na guia **Custo**.

5. Selecione a assinatura para a qual você deseja receber recomendações e, em seguida, clique em **Obter recomendações**.

> [!NOTE]
> Para acessar as recomendações do Assistente, você deve primeiro *registrar sua assinatura* no Assistente. Uma assinatura é registrada quando um *Proprietário de assinatura* inicia o painel do Assistente e clica no botão **Obter recomendações**. Essa é uma *operação única*. Depois que a assinatura for registrada, você poderá acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura, um grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-cost-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-cost-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)
