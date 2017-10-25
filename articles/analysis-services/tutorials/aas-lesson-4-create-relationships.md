---
title: "Lição 4 do tutorial do Azure Analysis Services: criar relações | Microsoft Docs"
description: "Descreve como criar relações no projeto de tutorial do Azure Analysis Services."
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
ms.openlocfilehash: 70812790348bbf525c7ed6299c656f7dd8e83dff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-4-create-relationships"></a>Lição 4: criar relações

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você verifica as relações que foram criadas automaticamente quando você importou dados e adicionou novas relações entre tabelas diferentes. Uma relação é uma conexão entre duas tabelas que estabelece como os dados nessas tabelas devem ser correlacionados. Por exemplo, as tabelas DimProduct e DimProductSubcategory têm uma relação baseada no fato de que cada produto pertence a uma subcategoria. Para saber mais, consulte [Relações](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Tempo estimado para conclusão desta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 3: marcar como tabela de data](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Examinar relações existentes e adicionar novas relações  
Quando você importou dados por meio de Obter Dados, você obteve sete tabelas do banco de dados AdventureWorksDW2014. Geralmente, quando você importa dados de uma fonte relacional, as relações existentes são importadas automaticamente junto com os dados. Para que Get Data crie automaticamente relações no modelo de dados, deve haver relações entre tabelas na fonte de dados.

Antes de prosseguir com a criação de seu modelo, você deve verificar se essas relações entre tabelas foram criadas corretamente. Para este tutorial, você também adicionará três novas relações.  

  
#### <a name="to-review-existing-relationships"></a>Para examinar relações existentes  
  
1.  Clique no menu **Modelo** > **Exibição de Modelo** > **Exibição de Diagrama**.  

    O designer de modelo agora aparece na Exibição de Diagrama, um formato gráfico que exibe todas as tabelas que você importou com linhas entre elas. As linhas entre as tabelas indicam as relações que foram criadas automaticamente quando você importou os dados.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Se você não vir relações entre tabelas, provavelmente significa que não há nenhuma relação entre essas tabelas na fonte de dados.

    Inclua o maior número possível de tabelas usando os controles de minimapa no canto inferior direito do designer de modelos. Também é possível clicar e arrastar tabelas para locais diferentes, aproximando as tabelas ou colocando-as em uma ordem específica. A movimentação de tabelas não afeta as relações já existentes entre elas. Para exibir todas as colunas em uma tabela específica, clique e arraste uma borda de tabela para expandi-la ou diminuí-la.  
  
2.  Clique na linha sólida entre a tabela **DimCustomer** e a tabela **DimGeography**. A linha sólida entre essas duas tabelas mostra que essa relação está ativa, ou seja, ela é usada por padrão ao calcular fórmulas DAX.  
  
    Observe que ambas a coluna **GeographyKey** na tabela **DimCustomer** e a coluna **GeographyKey** na tabela **DimGeography** aparecem agora cada uma dentro de uma caixa. Essas colunas são usadas na relação. As propriedades da relação agora também aparecem na janela **Propriedades**.  
  
    > [!TIP]  
    > Além de usar o designer de modelo na exibição de diagrama, você também pode usar a caixa de diálogo Gerenciar Relações para mostrar as relações entre todas as tabelas em um formato de tabela. No Gerenciador de Modelos tabulares, clique com o botão direito do mouse em **Relações** > **Gerenciar Relações**.
  
3.  Verifique se as seguintes relações foram criadas quando cada uma das tabelas foi importada do banco de dados AdventureWorksDW:  
  
    |Ativo|Tabela|Tabela de Pesquisa Relacionada|  
    |----------|---------|------------------------|  
    |Sim|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Sim|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Sim|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Sim|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Sim|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Se qualquer uma das relações estiver ausente, verifique se o modelo inclui as tabelas a seguir: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory e FactInternetSales. Se as tabelas da mesma conexão de fonte de dados forem importadas em momentos distintos, eventuais relações entre essas tabelas não serão criadas e deverão ser criadas manualmente. Se nenhuma relação aparecer, isso significa que não há nenhuma relação na fonte de dados. Você pode criá-los manualmente no modelo de dados.

### <a name="take-a-closer-look"></a>Veja uma análise mais detalhada
No modo de Exibição de Diagrama, você observará uma seta, um asterisco e um número de linhas que mostram a relação entre as tabelas.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

A seta mostra a direção do filtro. O asterisco que mostra essa tabela é o lado muitos na cardinalidade da relação e o número um mostra que esta tabela é o lado um da relação. Se você precisar editar uma relação; Por exemplo, alterar a direção de filtragem ou de cardinalidade da relação, clique duas vezes na linha de relação para abrir a caixa de diálogo Editar Relação.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Esses recursos são destinados para modelagem de dados avançada e estão fora do escopo deste tutorial. Para saber mais, consulte [filtros cruzados bidirecionais para modelos tabulares no Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

Em alguns casos, talvez seja necessário criar relações adicionais entre tabelas no modelo para dar suporte a determinadas lógicas de negócios. Para este tutorial, você precisa criar três relações adicionais entre a tabela FactInternetSales e a tabela DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Para adicionar novas relações entre tabelas  
  
1.  No designer de modelos, na tabela **FactInternetSales**, clique na coluna **OrderDate** e mantenha o botão do mouse pressionado sobre ela; em seguida, arraste o cursor para a coluna **Data** na tabela **DimDate** e solte.  

    Uma linha sólida aparece, indicando que você criou uma relação ativa entre a coluna **OrderDate** na tabela **Vendas Pela Internet** e a coluna **Data** na tabela **Data**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Ao criar relações, a direção de cardinalidade e de filtragem entre a tabela primária e a tabela de pesquisa relacionada é selecionada automaticamente.  
  
2.  Na tabela **FactInternetSales**, clique na coluna **DueDate** e mantenha o botão do mouse pressionado sobre ela; em seguida, arraste o cursor para a coluna **Data** na tabela **DimDate** e solte.  
  
    Uma linha pontilhada aparece, indicando que você criou uma relação inativa entre a coluna **DueDate** na tabela **FactInternetSales** e a coluna **Data** na tabela **DimDate**. Você pode ter várias relações entre tabelas, mas somente uma relação pode estar ativa por vez. Relações inativas podem passar a ficar ativas para executar agregações especiais em expressões do DAX personalizadas.  
  
3.  Por fim, crie mais uma relação. Na tabela **FactInternetSales**, clique na coluna **ShipDate** e mantenha o botão do mouse pressionado sobre ela; em seguida, arraste o cursor para a coluna **Data** na tabela **DimDate** e solte.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 5: criar colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  
