---
title: 'Início Rápido: Vincular um workspace do Power BI a um workspace do Synapse'
description: Vincule um workspace do Power BI a um workspace do Azure Synapse Analytics seguindo as etapas deste guia.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9c63e5e24495f373f288d2789780a6c671a7cc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218392"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Início Rápido: Vincular um workspace do Power BI a um workspace do Synapse

Neste início rápido, você aprenderá a conectar um workspace do Power BI a um workspace do Azure Synapse Analytics para criar relatórios e conjunto de dados do Power BI por meio do Synapse Studio.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um workspace do Azure Synapse e uma conta de armazenamento associada](quickstart-create-workspace.md)
- [Um workspace do Power BI Professional ou Premium](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Vincular o workspace do Power BI ao workspace do Synapse

1. Começando no Synapse Studio, clique em **Gerenciar**.

    ![No Synapse Studio, clique em gerenciar.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Em **Conexões externas**, clique em **Serviços vinculados**.

    ![Serviços vinculados é realçado.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Clique em **+ Novo**.

    ![+ Novos serviços vinculados é realçado.](media/quickstart-link-powerbi/new-highlighted.png)

4. Clique em **Power BI** e depois em **Continuar**.

    ![Exibindo o serviço vinculado do Power BI.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Insira um nome para o serviço vinculado e selecione um workspace na lista suspensa.

    ![Como exibir a configuração do serviço vinculado do Power BI.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Clique em **Criar**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Exibir o workspace do Power BI no Synapse Studio

Depois que seus workspaces estiverem vinculados, você poderá navegar em seus conjuntos de dados do Power BI e editar/criar relatórios do Power BI por meio do Synapse Studio.

1. Clique em **Desenvolver**.

    ![No Synapse Studio, clique em desenvolver.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Expanda o Power BI e o workspace que você deseja usar.

    ![Expanda o Power BI e o workspace.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Novos relatórios podem ser criados clicando em **+** , na parte superior da guia **Desenvolver**. Os relatórios existentes podem ser editados clicando em seus respectivos nomes. Todas as alterações salvas serão gravadas de volta no workspace do Power BI.

![Exibir e editar relatórios do Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Como criar relatórios do Power BI em arquivos armazenados no Armazenamento do Azure](sql/tutorial-connect-power-bi-desktop.md).