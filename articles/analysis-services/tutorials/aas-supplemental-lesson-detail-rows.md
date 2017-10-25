---
title: "Lição suplementar de tutorial do Azure Analysis Services: linhas de detalhes | Microsoft Docs"
description: "Descreve como criar uma expressão de linhas de detalhes no tutorial do Azure Analysis Services."
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
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: 9995ad39d9e3fd1a211c513d4097398e99eefc54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---detail-rows"></a>Lição suplementar – Linhas de Detalhes

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, você pode usar o Editor do DAX para definir uma expressão de linhas de detalhes personalizada. Uma expressão de linhas de detalhes é uma propriedade em uma medida, fornecendo aos usuários finais mais informações sobre os resultados agregados de uma medida. 
  
Tempo estimado para conclusão desta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelagem Tabular. Antes de executar as tarefas desta lição suplementar, você deve ter concluído todas as lições anteriores ou ter um projeto de modelo de amostra de Vendas pela Internet da Adventure Works concluído.  
  
## <a name="what-do-we-need-to-solve"></a>O que precisamos resolver?
Examinaremos os detalhes de nossa medida InternetTotalSales antes de adicionarmos uma expressão de linhas de detalhes.

1.  No SSDT, clique no menu **Modelo** > **Analisar no Excel** para abrir o Excel e criar uma tabela dinâmica em branco.
  
2.  Em **Campos de Tabela Dinâmica**, adicione a medida **InternetTotalSales** da tabela FactInternetSales a **Valores**, **CalendarYear** da tabela DimDate a **Colunas** e **EnglishCountryRegionName** a **Linhas**. Nossa tabela dinâmica agora nos oferece resultados agregados da medida InternetTotalSales por regiões e por ano. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Na tabela dinâmica, clique duas vezes em um valor agregado para um ano e um nome de região. Aqui, clicamos duas vezes no valor para Austrália e o ano de 2014. Uma nova planilha abre os dados contidos, mas não os dados úteis.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
O que gostaríamos de ver aqui é uma tabela contendo colunas e linhas de dados que contribuam para o resultado agregado de nossa medida InternetTotalSales. Para fazer isso, podemos adicionar uma expressão de linhas de detalhes como uma propriedade da medida.

## <a name="add-a-detail-rows-expression"></a>Adicionar uma expressão de linhas de detalhes

#### <a name="to-create-a-detail-rows-expression"></a>Para criar uma expressão de linhas de detalhes 
  
1. No SSDT, na grade de medida da tabela FactInternetSales, clique na medida **InternetTotalSales**. 

2. Em **Propriedades** > **Expressão de Linhas de Detalhes**, clique no botão do editor para abrir o Editor do DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. No Editor do DAX, digite a expressão a seguir:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Essa expressão especifica nomes de colunas, sendo que os resultados de medidas da tabela FactInternetSales e tabelas relacionadas são retornados quando um usuário clica duas vezes em um resultado agregado em uma tabela dinâmica ou relatório.

4. No Excel, exclua a planilha criada na Etapa 3, clique duas vezes um valor agregado. Desta vez, com uma propriedade de expressão de linhas de detalhes definida para a medida, uma nova planilha será aberta contendo dados muito mais úteis.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Reimplante o modelo.

  
## <a name="see-also"></a>Consulte também  
[Função SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Lição Suplementar – Segurança Dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lição Suplementar – hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  
