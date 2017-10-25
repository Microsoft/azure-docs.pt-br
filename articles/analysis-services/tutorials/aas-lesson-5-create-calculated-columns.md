---
title: "Lição 5 do tutorial do Azure Analysis Services: criar colunas calculadas | Microsoft Docs"
description: Descreve como criar colunas calculadas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: eab74fbadc6a143ca5a2bc57a1762539a6d489c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-5-create-calculated-columns"></a>Lição 5: criar colunas calculadas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você criará novos dados em seu modelo adicionando colunas calculadas. Você pode criar colunas calculadas (como colunas personalizadas) ao usar Obter Dados usando o Editor de Consultas ou posteriormente no designer de modelos, do mesmo modo que você fará aqui. Para saber mais, consulte [Colunas calculadas](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Você criará cinco novas colunas calculadas em três tabelas diferentes. As etapas são ligeiramente diferentes para cada tarefa, mostrando que há várias maneiras de criar colunas, renomeá-las e colocá-las em vários localizações em uma tabela.  

Nesta lição, você também usará primeiro as DAX (Expressões de Análise de Dados). DAX é uma linguagem especial para criar expressões de fórmula altamente personalizáveis para modelos tabulares. Neste tutorial, você usará o DAX para criar colunas calculadas, medidas e filtros de função. Para saber mais, veja [DAX em modelos tabulares](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular). 
  
Tempo estimado para conclusão desta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 4: criar relações](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Criar colunas calculadas  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Criar uma coluna calculada MonthCalendar na tabela DimDate  
  
1.  Clique no menu **Modelo** > **Exibição de Modelo** > **Exibição de Dados**.  
  
    Colunas calculadas só podem ser criadas usando o designer de modelo na Exibição de Dados.  
  
2.  No designer de modelos, clique na tabela **DimDate**.  
  
3.  Clique com o botão direito do mouse no cabeçalho de coluna **CalendarQuarter** e clique em **Inserir Coluna**.  
  
    Uma nova coluna chamada **Coluna Calculada 1** é inserida à esquerda da coluna **Trimestre do Calendário**.  
  
4.  Na barra de fórmulas acima da tabela, digite a seguinte fórmula DAX: o Preenchimento Automático ajuda você a digitar os nomes totalmente qualificados de colunas e tabelas e lista as funções que estão disponíveis.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Os valores são populados para todas as linhas na coluna calculada. Se você rolar para baixo na tabela, verá que linhas podem ter valores diferentes para esta coluna, com base nos dados que estão em cada linha.    
  
5.  Renomeie esta coluna para **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
A coluna calculada MonthCalendar fornece um nome classificável para o Mês.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Criar uma coluna calculada DayOfWeek na tabela DimDate  
  
1.  Com a tabela **DimDate** ainda ativa, clique no menu **Coluna** e depois em **Adicionar Coluna**.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Quando você terminar de criar a fórmula, pressione ENTER. A nova coluna é adicionada mais à direita da tabela.  
  
3.  Renomeie a coluna para **DayOfWeek**.  
  
4.  Clique no título de coluna e arraste a coluna entre a coluna **EnglishDayNameOfWeek** e a coluna **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Mover as colunas na tabela facilita a navegação.  
  
A coluna calculada DayOfWeek fornece um nome classificável para o dia da semana.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Criar uma coluna calculada ProductSubcategoryName na tabela DimProduct  
  
  
1.  Na tabela **DimProduct**, role até mais à direita da tabela. Observe que a coluna mais à direita é denominada **Adicionar Coluna** (em itálico), clique no título de coluna.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Renomeie a coluna para **ProductSubcategoryName**.  
  
A coluna calculada ProductSubcategoryName é usada para criar uma hierarquia na tabela DimProduct, a qual inclui dados da coluna EnglishProductSubcategoryName na tabela DimProductSubcategory. Hierarquias não podem abranger mais de uma tabela. Você criará hierarquias posteriormente, na Lição 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Criar uma coluna calculada ProductCategoryName na tabela DimProduct  
  
1.  Com a tabela **DimProduct** ainda ativa, clique no menu **Coluna** e depois em **Adicionar Coluna**.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Renomeie a coluna para **ProductCategoryName**.  
  
A coluna calculada ProductCategoryName é usada para criar uma hierarquia na tabela DimProduct, a qual inclui dados da coluna EnglishProductCategoryName na tabela DimProductCategory. Hierarquias não podem abranger mais de uma tabela.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Criar uma coluna calculada Margin na tabela FactInternetSales  
  
1.  No designer de modelos, selecione a tabela **FactInternetSales**.  
  
2.  Crie uma nova coluna calculada entre a coluna **SalesAmount** e a coluna **TaxAmt**.  
  
3.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Renomeie a coluna para **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    A coluna calculada Margin é usada para analisar as margens de lucro de cada venda.  
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 6: criar medidas](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
