---
title: "Lição 2 do tutorial do Azure Analysis Services: obter dados | Microsoft Docs"
description: Descreve como obter e importar dados no projeto de tutorial do Azure Analysis Services.
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
ms.date: 09/19/2017
ms.author: owend
ms.openlocfilehash: 07151fdf79d3b06f3144fbebff9015645b1ed000
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-2-get-data"></a>Lição 2: obter dados

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você usa Obter Dados no SSDT para se conectar ao banco de dados de exemplo AdventureWorksDW2014, selecionar dados, visualizar e filtrar e, em seguida, importar para o seu espaço de trabalho de modelo.  
  
Pelo uso do Obter Dados, você pode importar dados de uma ampla variedade de fontes: Banco de Dados SQL do Azure, Oracle, Sybase, OData Feed, Teradata, arquivos e muito mais. Os dados também podem ser consultados usando uma expressão de fórmula Power Query M.

> [!NOTE]
> As tarefas e as imagens neste tutorial mostram como se conectar a um banco de dados AdventureWorksDW2014 em um servidor local. Em alguns casos, um banco de dados AdventureWorksDW2014 no Azure pode ser diferente.
  
Tempo estimado para conclusão desta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 1: criar um novo projeto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Criar uma conexão  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Para criar uma conexão ao banco de dados AdventureWorksDW2014  
  
1.  No Gerenciador de Modelos tabulares, clique com o botão direito do mouse em **Fontes de Dados** > **Importar de Fonte de Dados**.  
  
    Isso inicializa o Obter Dados, que orienta você pela conexão a uma fonte de dados. Se você não vir o Gerenciador de Modelos tabulares, no **Gerenciador de Soluções**, clique duas vezes em **Model.bim** para abrir o modelo no designer. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Em Obter Dados, clique em **Banco de Dados** > **Banco de Dados do SQL Server** > **Conectar**.  
  
3.  Na caixa de diálogo **Banco de Dados do SQL Server**, em **Servidor**, digite o nome do servidor em que você instalou o banco de dados AdventureWorksDW2014 e, em seguida, clique em **Conectar**.  

4.  Quando for solicitado que você insira as credenciais, você precisará especificar as credenciais que o Analysis Services usa para se conectar à fonte de dados ao importar e processar dados. Em **Modo de Representação**, selecione **Representar Conta** e, em seguida, insira as credenciais e clique em **Conectar**. É recomendável que você use uma conta em que a senha não expira.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Usar uma conta de usuário e senha do Windows fornece o método mais seguro de se conectar a uma fonte de dados.
  
5.  No navegador, selecione o banco de dados **AdventureWorksDW2014** e, em seguida, clique em **OK**. Isso cria a conexão ao banco de dados. 
  
6.  No navegador, marque a caixa de seleção para as tabelas a seguir: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Depois que você clicar em OK, o Editor de Consultas será aberto. Na próxima seção, você selecionará somente os dados que deseja importar.

  
## <a name="filter-the-table-data"></a>Filtrar os dados da tabela  
As tabelas no banco de dados de exemplo AdventureWorksDW2014 têm dados que não precisam ser incluídos em seu modelo. Quando possível, é recomendável filtrar os dados desnecessários para economizar espaço na memória usado pelo modelo. Você filtrará algumas das colunas de tabelas para que elas não sejam importadas para o banco de dados do espaço de trabalho ou então para o modelo de banco de dados após ele ter sido implantado. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Para filtrar os dados da tabela antes de importar  
  
1.  No Editor de Consultas, selecione a tabela **DimCustomer**. Uma exibição da tabela DimCustomer na fonte de dados (seu banco de dados de exemplo AdventureWorksDW2014) é exibida. 
  
2.  Faça uma seleção múltipla (Ctrl + clique) de **SpanishEducation**, **FrenchEducation**, **SpanishOccupation** e **FrenchOccupation** e, em seguida, clique com o botão direito do mouse e clique em **Remover Colunas**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Como os valores dessas colunas não são pertinentes à análise de vendas pela Internet, não é necessário importá-las. A eliminação das colunas desnecessárias torna seu modelo menor e mais eficiente.  

    > [!TIP]
    > Se você cometer um erro, poderá fazer backup ao excluir uma etapa em **ETAPAS APLICADAS**.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Filtre as tabelas restantes, removendo as seguintes colunas em cada tabela:  
    
    **DimDate**
    
      |Coluna|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Coluna|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Coluna|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Coluna|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Coluna|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Nenhuma coluna foi removida.
  
## <a name="Import"></a>Importar os dados de colunas e tabelas selecionadas  
Agora que você visualizou e filtrou os dados desnecessários, você pode importar o restante dos dados que você deseja. O assistente importa os dados da tabela juntamente com quaisquer relacionamentos entre tabelas. As novas tabelas e colunas são criadas no modelo e dados que você filtrou não serão importados.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Para importar os dados de colunas e tabelas selecionadas  
  
1.  Examine suas seleções. Se tudo estiver ok, clique em **Importar**. A caixa de diálogo Processamento de Dados mostra o status dos dados que estão sendo importados de sua fonte de dados para seu banco de dados do espaço de trabalho.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Clique em **fechar**  

  
## <a name="save-your-model-project"></a>Salvar o projeto de modelo  
É importante salvar frequentemente o projeto de modelo.  
  
#### <a name="to-save-the-model-project"></a>Para salvar o projeto de modelo  
  
-   Clique em **Arquivo** > **Salvar Tudo**.  
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 3: marcar como tabela de data](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  
