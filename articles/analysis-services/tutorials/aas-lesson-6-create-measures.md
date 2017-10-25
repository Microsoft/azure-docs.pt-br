---
title: "Lição 6 do tutorial do Azure Analysis Services: criar medidas | Microsoft Docs"
description: Descreve como criar medidas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: ba0030ca25adf13a3a831facec2ded9cff0bd882
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-6-create-measures"></a>Lição 6: criar medidas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você criará medidas a serem incluídas no modelo. Semelhante às colunas calculadas que você criou, uma medida é um cálculo criado usando uma fórmula DAX. No entanto, ao contrário de colunas calculadas, as medidas são avaliadas com base em um *filtro* selecionado pelo usuário. Por exemplo, uma coluna ou segmentação de dados específica adicionada ao campo Rótulos de Linha em uma Tabela Dinâmica. Um valor para cada célula no filtro é então calculado pela medida aplicada. As medidas são cálculos avançados e flexíveis que você desejará incluir em quase todos os modelos tabulares para executar cálculos dinâmicos em dados numéricos. Para saber mais, veja [Medidas](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
Para criar medidas, você deve usar a *Grade de Medida*. Por padrão, cada tabela tem uma grade de medida vazia; no entanto, você normalmente não criará medidas para todas as tabelas. A grade de medida aparece abaixo de uma tabela no designer de modelo na Exibição de Dados. Para ocultar ou mostrar a grade de medida para uma tabela, clique no menu **tabela** e clique em **Mostrar Grade de Medida**.  
  
Você pode criar uma medida clicando em uma célula vazia na grade de medida e digitando uma fórmula DAX na barra de fórmulas. Quando você clicar em ENTER para completar a fórmula, a medida aparecerá na célula. Você também pode criar medidas usando uma função de agregação padrão clicando em uma coluna e, em seguida, clicando no botão AutoSoma (**∑**) na barra de ferramentas. As medidas criadas usando o recurso AutoSoma aparecerão na célula da grade de medida diretamente abaixo da coluna, mas podem ser movidas.  
  
Nesta lição, você criará medidas inserindo uma fórmula DAX na barra de fórmulas e usando o recurso AutoSoma.  
  
Tempo estimado para conclusão desta lição: **30 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 5: criar colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Criar medidas  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Para criar uma medida DaysCurrentQuarterToDate na tabela DimDate  
  
1.  No designer de modelo, clique na tabela **DimDate**.  
  
2.  Na grade de medida, clique na célula vazia superior esquerda.  
  
3.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Observe que a célula superior esquerda agora contém um nome de medida, **DaysCurrentQuarterToDate**, seguido pelo resultado, **92**. O resultado não é relevante neste ponto porque nenhum filtro de usuário foi aplicado.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    Ao contrário do que ocorre com colunas calculadas, com as fórmulas de medida você pode digitar o nome da medida, seguido por um ponto-e-vírgula, seguido pela expressão da fórmula.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Para criar uma medida DaysInCurrentQuarter na tabela DimDate  
  
1.  Com a tabela **DimDate** ainda ativa no designer de modelo, na grade de medida, clique na célula vazia abaixo da medida que você criou.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    Ao criar uma taxa de comparação entre um período incompleto e o período anterior. A fórmula deve calcular a proporção do período decorrido e compará-la à mesma proporção do período anterior. Nesse caso, [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] fornece a proporção decorrida no período atual.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Para criar uma medida InternetDistinctCountSalesOrder na tabela FactInternetSales  
  
1.  Clique na tabela **FactInternetSales**.   
  
2.  Clique no título de coluna **SalesOrderNumber**.  
  
3.  Na barra de ferramentas, clique na seta para baixo ao lado de AutoSoma (**∑**) e selecione **DistinctCount**.  
  
    O recurso AutoSoma cria automaticamente uma medida para a coluna selecionada usando a fórmula de agregação padrão DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  Na grade de medida, clique em nova medida e, em seguida, na janela **Propriedades**, em **Nome da Medida**, renomeie a medida para **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Para criar medidas adicionais na tabela FactInternetSales  
  
1.  Usando o recurso AutoSoma, crie e nomeie as seguintes medidas:  

    |Coluna|Nome da medida|AutoSoma (∑)|Fórmula|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Contagem|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Soma|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Soma|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Soma|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Soma|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Soma|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Soma|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Soma|=SUM([Freight])|  
  
2.  Clicando em uma célula vazia na grade de medida e usando a barra de fórmulas, crie, em ordem, as seguintes medidas personalizadas:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
As medidas criadas para a tabela FactInternetSales podem ser usadas para analisar dados financeiros essenciais, como vendas, custos e margem de lucro para itens definidos pelo filtro selecionado pelo usuário.  
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 7: criar indicadores chave de desempenho](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  
