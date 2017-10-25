---
title: "Lição 1 do tutorial do Azure Analysis Services: criar um novo projeto de modelo tabular | Microsoft Docs"
description: Descreve como criar um novo projeto de tutorial do Azure Analysis Services.
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
ms.date: 06/01/2017
ms.author: owend
ms.openlocfilehash: d523e3e103b4c351d01af6f1eb3c396f9a63016a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-1-create-a-tabular-model-project"></a>Lição 1: criar um projeto de modelo de tabela

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você usará as SQL Server Data Tools (SSDT) para criar um novo projeto de modelo tabular no nível de compatibilidade 1400. Depois de criar o novo projeto, você pode começar a adicionar dados e criar seu modelo. Esta lição fornece uma breve introdução ao ambiente de criação de modelos tabulares no SSDT.  
  
Tempo estimado para conclusão desta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico é a primeira lição em um tutorial de criação de modelos tabulares. Para concluir esta lição, há diversos pré-requisitos necessários. Para saber mais, veja [Azure Analysis Services – tutorial da Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Criar um novo projeto de modelo tabular  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Para criar um novo projeto de modelo tabular  
  
1.  No SSDT, no menu **Arquivo**, clique em **Novo** > **Projeto**.  
  
2.  Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Business Intelligence** > **Analysis Services** e, em seguida, clique em **Projeto tabular do Analysis Services**.  
  
3.  Em **Nome**, digite **Vendas pela Internet da AW** e, em seguida, especifique um local para os arquivos de projeto.  
  
    Por padrão, o **Nome da Solução** será o mesmo que o nome do projeto; no entanto, você pode digitar outro nome da solução.  
  
4.  Clique em **OK**.  
  
5.  Na caixa de diálogo **Designer de modelos tabulares**, selecione **Espaço de trabalho integrado**.  
  
    O espaço de trabalho hospedará um modelo de banco de dados tabelar com o mesmo nome que o projeto durante a criação do modelo. Espaço de trabalho integrado significa que o SSDT usará uma instância interna, eliminando a necessidade de instalar uma instância de servidor do Analysis Services separada apenas para a criação de modelos.
      
6.  Em **Nível de compatibilidade**, selecione **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Se você não vê a opção SQL Server 2017 / Azure Analysis Services (1400) na caixa de listagem Nível de compatibilidade, você não está usando a versão mais recente do SQL Server Data Tools. Para obter a versão mais recente, veja [Instalar SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>Noções básicas sobre o ambiente de criação de modelos tabulares do SSDT  
Agora que você criou um novo projeto de modelo tabular, exploraremos o ambiente de criação de modelos tabulares no SSDT.  
  
Depois que o projeto é criado, ele é aberto no SSDT. No lado direito, em **Gerenciador de Modelos tabulares**, você verá um modo de exibição de árvore dos objetos em seu modelo. Já que você ainda não terá importado dados, as pastas estarão vazias. Clique com o botão direito do mouse em uma pasta de objeto para executar ações, de modo similar a uma barra de menus. Conforme você percorre este tutorial, você usará o Gerenciador de Modelos tabulares para navegar por diferentes objetos no projeto de modelo.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Clique na guia **Gerenciador de Soluções**. Aqui, você verá seu arquivo **Model.bim**. Se você não vir a janela do designer à esquerda (a janela vazia com a guia Model.bim), no **Gerenciador de Soluções**, em **Projeto de Vendas pela Internet da AW**, clique duas vezes no arquivo **Model.bim**. O arquivo Model.bim contém os metadados para o seu projeto de modelo. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Clique em **Model.bim**. Na janela **Propriedades**, você verá as propriedades do modelo, dentre as quais a mais importante é a propriedade **Modo DirectQuery**. Esta propriedade especifica se o modelo é implantado no modo Na Memória (Desativado) ou no modo DirectQuery (Ativado). Para este tutorial, você criará e implantará o modelo no modo Na Memória.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Quando você cria um novo projeto modelo, determinadas propriedades de modelo são definidas automaticamente de acordo com as configurações de modelagem de dados que podem ser especificadas no menu **Ferramentas** > caixa de diálogo **Opções**. As propriedades Backup de Dados, Servidor de Espaço de Trabalho e Retenção de Espaço de Trabalho especificam como e onde o banco de dados do espaço de trabalho (o banco de dados de criação de modelos) tem seu backup feito, é retido na memória e compilado. Você pode alterar essas configurações mais tarde se necessário, mas por enquanto, deixe essas propriedades como estão.  

No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Vendas pela Internet da AW** (projeto) e depois clique em **Propriedades**. A caixa de diálogo **Páginas de Propriedades de Vendas pela Internet da AW** é exibida. Você definirá algumas dessas propriedades posteriormente quando implantar seu modelo.  
  
Quando você instalou o SSDT, vários novos itens de menu foram adicionados ao ambiente do Visual Studio. Clique na guia **Modelo**. Daqui em diante, você poderá importar dados, atualizar dados do espaço de trabalho, navegar pelo seu modelo no Excel, criar perspectivas e funções, selecionar a exibição de modelo e definir opções de cálculo. Clique o menu **Tabela**. Daqui em diante, você poderá criar e gerenciar relações, especificar configurações de tabela de data, criar partições e editar propriedades da tabela. Se você clicar no menu **Coluna**, poderá adicionar e excluir colunas em uma tabela, congelar colunas e especificar a ordem de classificação. O SSDT também adiciona alguns botões à barra. O recurso AutoSoma, para criação de uma medida de agregação padrão para uma coluna selecionada, é extremamente útil. Outros botões da barra de ferramentas fornecem acesso rápido aos comandos e recursos usados com frequência.  
  
Explore algumas das caixas de diálogo e localizações para vários recursos específicos para a criação de modelos tabulares. Embora alguns itens ainda não estejam ativos, você pode obter uma boa ideia do ambiente de criação de modelos tabulares.  
  

## <a name="whats-next"></a>O que vem a seguir?
[Lição 2: obter dados](../tutorials/aas-lesson-2-get-data.md).

  
  
  
