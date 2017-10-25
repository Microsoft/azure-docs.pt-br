---
title: "Lição 11 do tutorial do Azure Analysis Services: criar funções | Microsoft Docs"
description: "Descreve como criar funções no projeto de tutorial do Azure Analysis Services."
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
ms.openlocfilehash: 0dfcb9d9fc8cd32f95c5097ec653864364b27bcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-11-create-roles"></a>Lição 11: criar funções

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você cria funções. As funções fornecem segurança de dados e objetos de modelo de banco de dados, limitando o acesso aos usuários somente àqueles usuários que são membros da função. Cada função é definida com uma única permissão: Nenhum, Leitura, Leitura e Processo, Processo ou Administrador. Funções podem ser definidas durante a criação do modelo usando o Gerenciador de Funções. Depois que um modelo tiver sido implantado, você poderá gerenciar funções usando o SQL Server Management Studio (SSMS). Para saber mais, consulte [Funções](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Não é necessário criar funções para concluir este tutorial. Por padrão, a conta com a qual você está conectado terá privilégios de Administrador no modelo. No entanto, para permitir que outros usuários na sua organização procurem usando um cliente de relatório, você deve criar pelo menos uma função com permissões de Leitura e adicionar esses usuários como membros.  
  
Você cria três funções:  
  
-   **Gerente de Vendas** – essa função pode incluir usuários em sua organização para os quais você deseja ter permissão de Leitura para todos os objetos de modelo e dados.  
  
-   **Analista de Vendas dos EUA** – essa função pode incluir usuários em sua organização para os quais você deseja apenas ser capaz de procurar dados relacionados a vendas nos Estados Unidos. Para essa função, você usará uma fórmula DAX para definir um *Filtro de Linha*, que restringe os membros para procurar dados apenas para os Estados Unidos.  
  
-   **Administrador** – essa função pode incluir usuários para os quais você deseja ter permissão de Administrador, que fornece acesso ilimitado e permissões para executar tarefas administrativas no modelo de banco de dados.  
  
Já que as contas de usuário e de grupo do Windows em sua organização são exclusivas, você pode adicionar contas da sua organização privada para membros. No entanto, para este tutorial, você também pode deixar os membros em branco. Você testará o efeito de cada função posteriormente na Lição 12: Analisar no Excel.  
  
Tempo estimado para conclusão desta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 10: criar partições](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Criar funções  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Para criar uma função de usuário de Gerente de Vendas  
  
1.  No Gerenciador de Modelos tabular, clique com o botão direito do mouse em **Funções** > **Funções**.  
  
2.  No Gerenciador de Funções, clique em **Novo**.  
  
3.  Clique na nova função e, na coluna **Nome**, renomeie a função para **Gerente de Vendas**.  
  
4.  Na coluna **Permissões**, clique na lista suspensa e, em seguida, selecione a permissão **Leitura**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Opcional: clique na guia **Membros** e, em seguida, clique em **Adicionar**. Na caixa de diálogo **Selecionar Usuários ou Grupos**, digite os grupos os usuários do Windows da sua organização que você deseja incluir na função.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Para criar uma função de usuário de Analista de Vendas dos EUA  
  
1.  No Gerenciador de Funções, clique em **Novo**.    
  
2.  Renomeie a função para **Analista de Vendas dos EUA**.  
  
3.  Dê a essa função a permissão **Leitura**.  
  
4.  Clique na guia Filtros de Linha e, em seguida, somente para a tabela **DimGeography**, na coluna Filtro DAX, digite a seguinte fórmula:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Uma fórmula de Filtro de Linha deve ser resolvida para um valor booliano (TRUE/FALSE). Com esta fórmula, você está especificando que somente as linhas com o valor de Código do País/Região "US" estarão visíveis para o usuário.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Opcional: clique na guia **Membros** e, em seguida, clique em **Adicionar**. Na caixa de diálogo **Selecionar Usuários ou Grupos**, digite os grupos os usuários do Windows da sua organização que você deseja incluir na função.  
  
#### <a name="to-create-an-administrator-user-role"></a>Para criar uma função de usuário Administrador  
  
1.  Clique em **Novo**.  
  
2.  Renomeie a função para **Administrador**.  
  
3.  Dê permissão de **Administrador** a essa função.  
  
4.  Opcional: clique na guia **Membros** e, em seguida, clique em **Adicionar**. Na caixa de diálogo **Selecionar Usuários ou Grupos**, digite os grupos os usuários do Windows da sua organização que você deseja incluir na função. 
  
  
## <a name="whats-next"></a>O que vem a seguir?
[Lição 12: Analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  
