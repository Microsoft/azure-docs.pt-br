---
title: Adicionar ferramentas de avaliação em migrações para Azure
description: Saiba como adicionar ferramentas de avaliação nas migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786797"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação nas [migrações para Azure](./migrate-services-overview.md). 

- Se você quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto de migrações para Azure, siga este [artigo](create-manage-projects.md).
- Se você tiver adicionado uma ferramenta ISV, ou mover, para avaliação, [siga as etapas](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-an-assessment-scenario"></a>Selecionar um cenário de avaliação

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de avaliação:

    - Para descobrir, avaliar e migrar servidores (físicos ou virtuais) do seu datacenter ou outras nuvens para o Azure, selecione **descobrir, avaliar e migrar**. Agora você também pode descobrir e avaliar SQL Server de seu ambiente VMware usando essa meta de migração.
    - Para avaliar os bancos de dados SQL Server locais, selecione **avaliar e migrar bancos de dados**.
    - Para avaliar ou migrar aplicativos Web locais, selecione **explorar mais**  >  **aplicativos Web**.
    - Para avaliar sua infraestrutura de área de trabalho virtual, selecione **explorar mais**  >  **Virtual Desktop Infrastructure**.

    ![Opções para selecionar um cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Selecione uma ferramenta de descoberta e avaliação 


1. Adicione uma ferramenta:

    - Se você criou um projeto de migrações para Azure usando a opção **avaliar e migrar servidores** no portal, a ferramenta de avaliação e descoberta de migrações para Azure será adicionada automaticamente ao projeto. Para adicionar ferramentas de avaliação adicionais, no **Windows, Linux e SQL Server**, ao lado de **ferramentas de avaliação**, selecione **adicionar mais ferramentas**.

         ![Botão para adicionar ferramentas de avaliação adicionais](./media/how-to-assess/add-assessment-tool.png)

    - Se você criou um projeto usando uma opção diferente e ainda não tem ferramentas de avaliação, nas ferramentas de avaliação do **Windows, Linux e SQL Server**  >  , selecione **clique aqui**.

        ![Botão para adicionar a primeira ferramenta de avaliação](./media/how-to-assess/no-assessment-tool.png)

2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione as ferramentas que você deseja adicionar. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecione Ferramentas de avaliação da lista](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Selecionar uma ferramenta de avaliação de banco de dados

1. Adicione uma ferramenta:

    - Se você criou um projeto de migrações para Azure usando a opção **avaliar e migrar banco de dados** no portal, a ferramenta de avaliação do banco de dados será adicionada automaticamente ao projeto. Para adicionar ferramentas de avaliação adicionais, em **bancos de dados**, ao lado de ferramentas de **avaliação**, selecione **adicionar mais ferramentas**.

    - Se você tiver criado um projeto usando uma opção diferente e ainda não tiver nenhuma ferramenta de avaliação de banco de dados, em ferramentas de avaliação do **databases**  >  , selecione **clique aqui**.

2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione as ferramentas que você deseja adicionar. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecionar ferramentas de avaliação de banco de dados da lista](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativo Web

Se você criou um projeto de migrações para Azure usando a opção **explorar mais**  >  **webapps** no portal, a ferramenta de avaliação do aplicativo Web será adicionada automaticamente ao projeto. 


1. Se a ferramenta de avaliação de aplicativo Web não estiver no projeto, em ferramentas de avaliação de **aplicativos Web**  >  , selecione **clique aqui**.
    
    ![Adicionar ferramentas de avaliação do aplicativo Web](./media/how-to-assess/no-web-app-assessment-tool.png)


2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione a ferramenta de avaliação de aplicativo Web. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecione a ferramenta de migração de banco de dados na lista](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Próximas etapas

Descobrir servidores locais para avaliação usando a ferramenta de avaliação e descoberta do Azure Migrations para [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)ou [servidores físicos](./tutorial-discover-physical.md)