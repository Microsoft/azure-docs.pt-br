---
title: "Lição 7 do tutorial do Azure Analysis Services: criar indicadores chave de desempenho | Microsoft Docs"
description: Descreve como criar indicadores chave de desempenho no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: 9671136eea4e390a7763404e402cf94f1d351cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-7-create-key-performance-indicators"></a>Lição 7: criar indicadores chave de desempenho

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você cria KPIs (indicadores chave de desempenho). Os KPIs são usados para medir o desempenho de um valor, definido por uma medida *Base* contra um valor de *Destino*, também definido por uma medida ou um valor absoluto. Em aplicativos cliente de relatório, KPIs podem fornecer aos profissionais de negócios uma maneira rápida e fácil de entender um resumo de sucesso nos negócios ou para identificar tendências. Para obter mais informações, consulte [KPIs](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)
  
Tempo estimado para conclusão desta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 6: criar medidas](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Criar indicadores chave de desempenho  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Para criar um KPI de InternetCurrentQuarterSalesPerformance  
  
1.  No designer de modelos, clique na tabela **FactInternetSales**.  
  
2.  Na grade de medida, clique em uma célula vazia.  
  
3.  Na barra de fórmulas acima da tabela, digite a fórmula a seguir: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Essa medida servirá como a medida Base para o KPI.  
  
4.  Na grade de medida, clique com o botão direito do mouse em **InternetCurrentQuarterSalesPerformance** > **Criar KPI**.   
  
5.  Na caixa de diálogo KPI (Indicador Chave de Desempenho), em **Destino**, selecione **Valor Absoluto** e, em seguida, digite **1.1**.  
  
7.  No campo de controle deslizante da esquerda (baixo), digite **1** e então, no campo de controle deslizante da direita (alto), digite **1.07**.  
  
8.  Em **Selecionar Estilo de Ícone**, selecione o tipo de ícone losango (vermelho), triângulo (amarelo) e círculo (verde).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Observe o rótulo expansível **Descrições** abaixo dos estilos de ícone disponíveis. Use as descrições para os vários elementos de KPI para torná-los mais identificáveis nos aplicativos cliente.  
  
9. Clique em **OK** para concluir o KPI.  
  
    Na grade de medida, observe o ícone ao lado da medida **InternetCurrentQuarterSalesPerformance**. Esse ícone indica que essa medida serve como um valor Base para um KPI.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Para criar um KPI de InternetCurrentQuarterMarginPerformance  
  
1.  Na grade de medida para a tabela **FactInternetSales**, clique em uma célula vazia.  
  
2.  Na barra de fórmulas acima da tabela, digite a fórmula a seguir:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Clique com o botão direito do mouse em **InternetCurrentQuarterMarginPerformance** > **Criar KPI**.  
  
4.  Na caixa de diálogo KPI (Indicador Chave de Desempenho), em **Destino**, selecione **Valor Absoluto** e, em seguida, digite **1.25**.   
  
5.  No campo de controle deslizante da esquerda (baixo), deslize até que o campo exiba **0.8** e, em seguida, deslize o campo de controle deslizante à direita (alto) até que o campo exiba **1.03**.  
  
6.  Em **Selecionar Estilo de Ícone**, selecione o tipo de ícone losango (vermelho), triângulo (amarelo), círculo (verde) e clique em **OK**.  
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 8: criar perspectivas](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
