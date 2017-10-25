---
title: "Lição 8 do tutorial do Azure Analysis Services: criar perspectivas | Microsoft Docs"
description: Descreve como criar perspectivas no projeto de tutorial do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 68f4e9744007bcf71c5b9239ecfb6f18cf0cd86e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-8-create-perspectives"></a>Lição 8: criar perspectivas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você criará uma perspectiva de Vendas pela Internet. Uma perspectiva define um subconjunto exibível de um modelo que fornece pontos de vista concentrados, específicos da empresa ou específicos do aplicativo. Quando um usuário se conecta a um modelo usando uma perspectiva, ele vê apenas os objetos de modelo (tabelas, colunas, medidas, hierarquias e KPIs) como campos definidos nessa perspectiva. Para saber mais, confira [Perspectivas](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
A perspectiva de vendas pela Internet que você criou nesta lição excluirá o objeto de tabela DimCustomer. Quando você cria uma perspectiva que exclui determinados objetos de exibição, esse objeto ainda existe no modelo. No entanto, não é visível em uma lista de campos de cliente de relatório. Colunas calculadas e medidas incluídas ou não em uma perspectiva ainda podem calcular com base em de dados do objeto excluído.  
  
O objetivo desta lição é descrever como criar perspectivas e se familiarizar com ferramentas de criação de modelo tabular. Se você expandir este modelo para incluir tabelas adicionais posteriormente, você poderá criar perspectivas adicionais para definir diferentes pontos de vista do modelo, por exemplo, Vendas e Inventário.  
  
Tempo estimado para a conclusão desta lição: **Cinco minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 7: criar indicadores chave de desempenho](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Criar perspectivas  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Para criar uma perspectiva de Vendas pela Internet  
  
1.  Clique no menu **Modelo** > **Perspectivas** > **Criar e Gerenciar**.  
  
2.  Na caixa de diálogo **Perspectivas**, clique em **Nova Perspectiva**.  
  
3.  Clique duas vezes no título de coluna **Nova Perspectiva** e renomeie-o para **Vendas pela Internet**.  
  
4.  Selecione todas as tabelas, *exceto* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    Em uma lição posterior, você usará o recurso Analisar no Excel para testar essa perspectiva. A Lista de Campos de Tabela Dinâmica do Excel incluirá cada tabela, com exceção da tabela DimCustomer.  

## <a name="whats-next"></a>O que vem a seguir?
[Lição 9: criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  
