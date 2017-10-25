---
title: "Lição 12 do tutorial do Azure Analysis Services: Analisar no Excel | Microsoft Docs"
description: Descreve como usar Analizar no Excel no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: e257862a88d39b96360703117f544c43e82b0e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-12-analyze-in-excel"></a>Lição 12: analisar no Excel

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você usa o recurso Analisar no Excel para abrir o Microsoft Excel, criar automaticamente uma conexão ao espaço de trabalho de modelo e adicionar automaticamente uma tabela dinâmica à planilha. O recurso Analisar no Excel destina-se a fornecer uma maneira rápida e fácil de testar a eficácia do design de modelo antes de implantar seu modelo. Você não executará nenhuma análise de dados nesta lição. O objetivo desta lição é familiarizar você, o autor do modelo, com as ferramentas que você pode usar para testar seu design de modelo.   
  
Para concluir esta lição, o Excel deverá estar instalado no mesmo computador que o SSDT.
  
Tempo estimado para a conclusão desta lição: **Cinco minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 11: criar funções](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Procurar usando as perspectivas Padrão e Vendas pela Internet  
Nestas primeiras tarefas, procure seu modelo usando tanto a perspectiva padrão, que inclui todos os objetos de modelo, quanto a perspectiva de Vendas pela Internet, que você viu anteriormente. A perspectiva de vendas pela Internet exclui o objeto de tabela Customer.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Para navegar usando a perspectiva Padrão  
  
1.  Clique no menu **Modelo** > **Analisar no Excel**.  
  
2.  Na caixa de diálogo **Analisar no Excel**, clique em **OK**.  
  
    O Excel abre com uma nova pasta de trabalho. Uma conexão de fonte de dados é criado usando a conta de usuário atual e a perspectiva Padrão é usada para definir campos visíveis. Uma tabela dinâmica é adicionada automaticamente à planilha.  
  
3.  No Excel, na **lista Campo da Tabela Dinâmica**, observe os grupos de medidas **DimDate** e **FactInternetSales** exibidos. As tabelas **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales** com as respectivas colunas também aparecem.  
  
4.  Feche o Excel sem salvar a pasta de trabalho.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Para navegar usando a perspectiva de Vendas pela Internet  
  
1.  Clique no menu **Modelo** e depois em **Analisar no Excel**.  
  
2.  Na caixa de diálogo **Analisar no Excel**, deixe **Usuário Atual do Windows** selecionado, em seguida, na caixa de listagem suspensa **Perspectiva**, selecione **Vendas pela Internet** e clique em **OK**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  No Excel, nos **Campos de Tabela Dinâmica**, observe que a tabela DimCustomer é excluída da lista de campos.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Feche o Excel sem salvar a pasta de trabalho.  
  
## <a name="browse-by-using-roles"></a>Navegar usando funções  
As funções são parte importante de qualquer modelo tabular. Sem pelo menos uma função à qual os usuários são adicionados como membros, os usuários não poderão acessar e analisar dados usando seu modelo. O recurso Analisar no Excel fornece uma maneira de testar as funções definidas por você.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Para navegar usando a função de usuário do Gerente de Vendas  
  
1.  No SSDT, clique no menu **Modelo** e depois em **Analisar no Excel**.  
  
2.  Em **Especificar o nome de usuário ou função a ser usada para se conectar ao modelo**, selecione **Função** e, em seguida, na caixa de listagem suspensa, selecione **Gerente de Vendas** e clique em **OK**.  
  
    O Excel abre com uma nova pasta de trabalho. Uma tabela dinâmica é criada automaticamente. A lista de campos de tabela dinâmica inclui todos os campos de dados disponíveis no novo modelo.  
      
3.  Feche o Excel sem salvar a pasta de trabalho.  
  
## <a name="whats-next"></a>O que vem a seguir?
Vá para a próxima lição: [Lição 13: implantar](../tutorials/aas-lesson-13-deploy.md).

  
  
  
