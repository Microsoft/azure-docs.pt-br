---
title: "Lição 10 do tutorial do Azure Analysis Services: criar partições | Microsoft Docs"
description: "Descreve como criar partições no projeto de tutorial do Azure Analysis Services."
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
ms.openlocfilehash: b6b5bcd1d766376bd0af71e1fa91f8f2f96b9605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-10-create-partitions"></a>Lição 10: criar partições

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você cria partições para dividir a tabela FactInternetSales em partes lógicas menores que podem ser processadas (atualizadas) independentemente de outras partições. Por padrão, cada tabela incluída em seu modelo tem uma partição, o que inclui todas as colunas e linhas da tabela. Para a tabela FactInternetSales, desejamos dividir os dados por ano; uma partição para cada um dos cinco anos da tabela. Cada partição pode ser então processada independentemente. Para saber mais, confira [Partições](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Tempo estimado para conclusão desta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 9: criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Criar partições  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Para criar partições na tabela FactInternetSales  
  
1.  No Gerenciador de Modelos tabular, expanda **Tabelas** e, em seguida, clique com o botão direito do mouse em **FactInternetSales** > **Partições**.  
  
2.  No Gerenciador de Partições, clique em **Copiar** e, em seguida, altere o nome para **FactInternetSales2010**.
  
    Já que você deseja que a partição inclua somente as linhas em um determinado período, para o ano de 2010, você deve modificar a expressão de consulta.
  
4.  Clique em **Design** para abrir o Editor de Consultas e, em seguida, clique na consulta **FactInternetSales2010**.

5.  Na versão prévia, clique na seta para baixo no título de coluna **OrderDate** e clique em **Filtros de Data/Hora** > **Entre**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  Na caixa de diálogo Filtrar Linhas, em **Mostrar linhas em que: OrderDate**, deixe **é depois ou igual a** e, em seguida, no campo de data, digite **1/1/2010**. Deixe o operador **E** selecionado, selecione **está antes de** e então, no campo de data, digite **1/1/2011** e clique em **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Observe que, no Editor de Consultas, em ETAPAS APLICADAS, você verá outra etapa chamada Linhas Filtradas. Esse filtro serve para selecionar apenas as datas de pedido de 2010.

8.  Clique em **Importar**.

    No Gerenciador de Partições, observe que a expressão de consulta agora tem uma cláusula adicional de Linhas Filtradas.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Essa instrução especifica que essa partição deve incluir somente os dados nessas linhas aos quais OrderDate refere-se no ano civil de 2010, conforme especificado na cláusula de linhas filtradas.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Para criar uma partição para o ano de 2011  
  
1.  Na lista de partições, clique na partição **FactInternetSales2010** que você criou e, em seguida, clique em **Copiar**.  Altere o nome da partição para **FactInternetSales2011**. 

    Você não precisa usar o Editor de Consultas para criar uma nova cláusula de linhas filtradas. Já que você criou uma cópia da consulta referente a 2010, tudo que você precisa fazer é fazer uma pequena alteração na consulta referente a 2011.
  
2.  Em **Expressão de Consulta**, para que essa partição inclua somente as linhas para o ano de 2011, substitua os anos na cláusula Linhas Filtradas com **2011** e **2012**, respectivamente, como:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Para criar partições para 2012, 2013 e 2014.  
  
- Siga as etapas anteriores, criando partições para 2012, 2013 e 2014, alterando os anos na cláusula Linhas Filtradas para incluir apenas as linhas para o ano em questão. 
  

## <a name="delete-the-factinternetsales-partition"></a>Excluir a partição FactInternetSales
Agora que você tem partições para cada ano, você pode excluir a partição FactInternetSales, impedindo a sobreposição ao escolher Processar tudo ao processar partições.

#### <a name="to-delete-the-factinternetsales-partition"></a>Para excluir a partição FactInternetSales
-  Clique na partição FactInternetSales e, em seguida, clique em **Excluir**.



## <a name="process-partitions"></a>Processar partições  
No Gerenciador de partições, observe que a coluna **Último Processamento** para cada uma das novas partições que você criou mostra que essas partições nunca foram processadas. Quando você cria partições, você deve executar uma operação Processar Partições ou Processar Tabela para atualizar os dados nessas partições.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Para processar as partições FactInternetSales  
  
1.  Clique em **OK** para fechar o Gerenciador de Partições.  
  
2.  Clique na tabela **FactInternetSales** e, em seguida, clique no menu **Modelo** > **Processar** > **Processar Partições**.  
  
3.  Na caixa de diálogo Processar Partições, verifique se **Modo** está definido como **Processar Padrão**.  
  
4.  Selecione a caixa de seleção na coluna **Processo** para cada uma das cinco partições criadas e depois clique em **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Se for solicitado que você informe as credenciais de representação, insira o nome de usuário e senha do Windows que você especificou na Lição 2.  
  
    A caixa de diálogo de **Processamento de Dados** aparece e exibe os detalhes do processo para cada partição. Observe que um número diferente de linhas é transferido para cada partição. Cada partição inclui somente as linhas para o ano especificado na cláusula WHERE na instrução SQL. Quando o processamento for concluído, vá em frente e feche a caixa de diálogo Processamento de Dados.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>O que vem a seguir?
Vá para a próxima lição: [Lição 11: criar funções](../tutorials/aas-lesson-11-create-roles.md). 
