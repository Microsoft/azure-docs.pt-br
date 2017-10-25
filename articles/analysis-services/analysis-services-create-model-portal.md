---
title: Criar um modelo de tabela usando o designer de Web do Azure Analysis Services | Microsoft Docs
description: Saiba como criar um modelo de tabela do Azure Analysis Services usando o designer de Web no portal do Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.openlocfilehash: bd58f1845dabf6afb47ce27236d14479677a8808
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-model-in-azure-portal"></a>Criar um modelo no portal do Azure

O recurso de Web designer do Azure Analysis Services (Versão Prévia) no portal do Azure fornece uma maneira rápida e fácil de criar e editar modelos de tabela e consultar dados de modelo diretamente em seu navegador. 

Tenha em mente que o Web designer é uma **versão prévia**. Embora novas funcionalidades estão sendo adicionadas o tempo todo, na versão prévia, a funcionalidade é limitada. Para desenvolvimento e teste de modelos mais avançados, é melhor usar o Visual Studio (SSDT) e SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor do Azure Analysis Services na camada Padrão ou Desenvolvedor. Novos modelos criados usando o designer de Web são DirectQuery com suporte apenas dessas camadas.
- Um Banco de Dados SQL Azure, Azure SQL Data Warehouse ou arquivo Power BI Desktop (.pbix) como fonte de dados. Novos modelos criados por meio de arquivos Power BI Desktop dão suporte às fontes de dados de Banco de Dados SQL Azure, Azure SQL Data Warehouse, Oracle e Teradata.
- Uma conta e senha do SQL Server para se conectar a fontes de dados do Banco de Dados SQL Azure ou Azure SQL Data Warehouse.

## <a name="to-create-a-new-tabular-model"></a>Para criar um novo modelo de tabela

1. Na folha **Visão geral** > **Designer de Web** do seu servidor, clique em **Abrir**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Em **Designer de Web** > **Modelos**, clique em **+ Adicionar**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Em **Novo modelo**, digite um nome de modelo e, em seguida, selecione uma fonte de dados.

    ![Caixa de diálogo Novo modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. Em **Conectar**, insira as propriedades de conexão. Nome de usuário e senha devem ser uma conta do SQL Server.

     ![Caixa de diálogo Conectar no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. Em **Tabelas e exibições**, selecione as tabelas para incluir em seu modelo e, em seguida, clique em **Criar**. São criadas automaticamente relações entre tabelas com um par de chaves.

     ![Selecionar tabelas e exibições](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

O novo modelo é exibido no navegador. A partir daqui, você pode:   

- Consultar dados de modelo arrastando campos para o designer de consulta e adicionando filtros.
- Crie novas medidas nas tabelas.
- Edite metadados do modelo usando o editor json.
- Abra o modelo no Visual Studio (SSDT), Power BI Desktop ou Excel.

![Selecionar tabelas e exibições](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Quando você edita os metadados de modelo ou cria novas medidas em seu navegador, você está salvando as alterações em seu modelo no Azure. Se você também estiver trabalhando em seu modelo no SSDT, Power BI Desktop ou Excel, o modelo pode ficar fora de sincronia.


## <a name="next-steps"></a>Próximas etapas 
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[Conectar com o Excel](analysis-services-connect-excel.md)  


