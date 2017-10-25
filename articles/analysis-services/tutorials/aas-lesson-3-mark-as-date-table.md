---
title: "Lição 3 do tutorial do Azure Analysis Services: marcar como tabela de data | Microsoft Docs"
description: Descreve como marcar uma tabela de data no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: 94ea44091c8a7befaf0c82349c47dce7a36c1fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-3-mark-as-date-table"></a>Lição 3: marcar como tabela de data

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Na Lição 2: obter dados, você importou uma tabela de dimensões chamada DimDate. Enquanto no seu modelo essa tabela é denominada DimDate, ela também é conhecida como uma *Tabela de data*, que contém dados de data e hora.  
  
Sempre que usar funções de inteligência de dados temporais DAX, como fará quando criar medidas mais adiante, você deverá especificar as propriedades, que incluem uma *Tabela de data* e uma *coluna Data*, que é um identificador exclusivo nessa tabela.
  
Nesta lição, você marca a tabela DimDate, como a *Tabela de data* e a coluna Data (na tabela de Data) como a *coluna Data* (identificador exclusivo).  

Antes de marcar a tabela de data e a coluna de data, é um bom momento para fazer um pouco de manutenção para facilitar a compreensão do seu modelo. Na tabela DimDate, observe uma coluna denominada **FullDateAlternateKey**. Esta coluna contém uma linha para cada dia em cada ano civil incluído na tabela. Você usará muito essa coluna em fórmulas de medida e em relatórios. No entanto, FullDateAlternateKey não é um bom identificador para essa coluna. Você o renomeará para **Data**, tornando-o mais fácil de identificar e incluir em fórmulas. Sempre que possível, é uma boa ideia para renomear objetos, como tabelas e colunas, para torná-los mais fáceis de identificar no SSDT e aplicativos cliente de relatório como o Power BI e o Excel. 
  
Tempo estimado para conclusão desta lição: **três minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 2: obter dados](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Para renomear a coluna FullDateAlternateKey

1.  No designer de modelo, clique na tabela **DimDate**.

2.  Clique duas vezes no cabeçalho para a coluna **FullDateAlternateKey** e então renomeie-a para **Data**.

  
### <a name="to-set-mark-as-date-table"></a>Para definir Marcar Como Tabela de Data  
  
1.  Selecione a coluna **Data** e, em seguida, na janela **Propriedades**, em **Tipo de Dados**, verifique se **Data** está selecionado.  
  
2.  Clique no menu **Tabela**, clique em **Data** e, em seguida, clique em **Marcar Como Tabela de Data**.  
  
3.  Na caixa de diálogo **Marcar Como Tabela de Data**, na caixa de listagem **Data**, selecione a coluna **Data** como o identificador exclusivo. Ela geralmente é selecionada por padrão. Clique em **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>O que vem a seguir?
[Lição 4: criar relações](../tutorials/aas-lesson-4-create-relationships.md).
  
