---
title: Adicionar ferramentas de migração nas migrações para Azure
description: Saiba como adicionar ferramentas de migração nas migrações para Azure.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751777"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração nas [migrações para Azure](./migrate-services-overview.md).

- Se você quiser adicionar uma ferramenta de migração e ainda não tiver configurado um projeto de migrações para Azure, siga este [artigo](create-manage-projects.md).
- Se você tiver adicionado uma ferramenta ISV para migração, [siga as etapas](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-a-migration-scenario"></a>Selecionar um cenário de migração

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de migração que você deseja usar:

    - Para migrar máquinas e cargas de trabalho para o Azure, selecione **avaliar e migrar servidores**.
    - Para migrar bancos de dados locais, selecione **avaliar e migrar bancos de dados**.
    - Para migrar aplicativos Web locais, selecione **explorar mais**  >  **aplicativos Web**.
    - Para migrar dados para o Azure usando o data Box, selecione **explorar mais**  >  **dados caixa**.

    ![Opções para selecionar um cenário de migração](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração de servidor

1. Adicione uma ferramenta:

    - Se você criou um projeto de migrações para Azure usando a opção **avaliar e migrar servidores** no portal, a ferramenta de migração de servidor migrações para Azure é adicionada automaticamente ao projeto. Para adicionar ferramentas de migração adicionais, em **servidores**, ao lado de **ferramentas de migração**, selecione **adicionar mais ferramentas**.
    
         ![Botão para adicionar ferramentas de migração adicionais](./media/how-to-migrate/add-migration-tools.png)

    - Se você tiver criado um projeto usando uma opção diferente e ainda não tiver ferramentas de migração, em **servidores**  >  **ferramentas de migração**, selecione **clique aqui**.

    ![Botão para adicionar as primeiras ferramentas de migração](./media/how-to-migrate/no-migration-tool.png)

2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione as ferramentas que você deseja adicionar. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecione Ferramentas de avaliação da lista](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de banco de dados

Se você criou um projeto de migrações para Azure usando a opção **avaliar e migrar banco de dados** no portal, a ferramenta de migração de banco de dados é adicionada automaticamente ao projeto. 

1. Se a ferramenta de migração de banco de dados não estiver no projeto, em **banco** de dados  >  **ferramentas de avaliação**, selecione **clique aqui**.
    
    ![Adicionar ferramentas de migração de banco de dados](./media/how-to-migrate/no-database-migration-tool.png)


2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione a ferramenta de migração de banco de dados. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecione a ferramenta de migração de banco de dados na lista](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativo Web

Se você criou um projeto de migrações para Azure usando a opção **explorar mais**  >  **webapps** no portal, a ferramenta de migração de aplicativo Web será adicionada automaticamente ao projeto. 

1. Se a ferramenta de migração de aplicativo Web não estiver no projeto, em ferramentas de avaliação de **aplicativos Web**  >  , selecione **clique aqui**.

    ![Adicionar ferramentas de migração de aplicativo Web](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. Em **migrações para Azure**  >  ,**adicione ferramentas**, selecione a ferramenta de migração de aplicativo Web. Em seguida, selecione **Adicionar ferramenta**.

    ![Selecione Ferramentas de avaliação do webapp na lista](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Ordenar um Azure Data Box

Para migrar grandes quantidades de dados para o Azure, você pode solicitar um Azure Data Box para transferência de dados offline.

1. Em **visão geral**, selecione **explorar mais**.
2. Em **explorar mais**, selecione **Data Box**.
3. Em Introdução ao **Data Box**, selecione a assinatura e o grupo de recursos que você deseja usar ao ordenar uma data box.
4. O **tipo de transferência** é uma importação para o Azure. Especifique o país no qual os dados residem e a região do Azure para a qual você deseja transferir os dados. 
5. Clique em **aplicar** para salvar as configurações.

## <a name="next-steps"></a>Próximas etapas

Experimente uma migração usando a ferramenta de migração de servidor de migrações para Azure para [Hyper-V](tutorial-migrate-hyper-v.md) ou VMS [VMware](tutorial-migrate-vmware.md) .
