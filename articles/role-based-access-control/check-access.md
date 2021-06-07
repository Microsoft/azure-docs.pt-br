---
title: Início rápido – Verificar o acesso que um usuário tem aos recursos do Azure – RBAC do Azure
description: Neste início rápido, você aprenderá a verificar o acesso para si próprio ou outro usuário para recursos do Azure usando o portal do Azure e o Azure RBAC (controle de acesso baseado em função).
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5e4f3314ba580dddbd995855bc0f0512b7597107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98115711"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Início Rápido: Verificar o acesso que um usuário tem aos recursos do Azure

Às vezes, você precisa verificar o acesso que um usuário tem a um conjunto de recursos do Azure. Você verifica o acesso dele listando as atribuições que ele tem. Uma forma rápida de verificar o acesso de um usuário é usar o recurso **Verificar acesso** na página **Controle de acesso (IAM)** .

## <a name="step-1-open-the-azure-resources"></a>Etapa 1: Abrir os recursos do Azure

Para verificar o acesso de um usuário, primeiro você precisa abrir os recursos do Azure para os quais deseja verificar o acesso. Os recursos do Azure são organizados em níveis que normalmente são chamados de *escopo*. No Azure, você pode especificar um escopo em quatro níveis de amplo até restrito: grupo de gerenciamento, assinatura, grupo de recursos e recurso.

![Níveis de escopo para o Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

Siga estas etapas para abrir o conjunto de recursos do Azure para o qual você deseja verificar o acesso.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Abra o conjunto de recursos do Azure, como **Grupos de gerenciamento**, **Assinaturas**, **Grupos de recursos** ou um recurso específico.

1. Clique no recurso específico nesse escopo.

    A seguir é mostrado um exemplo de grupo de recursos.

    ![Visão geral do grupo de recursos](./media/shared/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Etapa 2: Verificar o acesso de um usuário

Siga estas etapas para verificar o acesso de um usuário, um grupo, uma entidade de serviço ou uma identidade gerenciada aos recursos do Azure previamente selecionados.

1. Clique em **IAM (Controle de Acesso)**.

    O exemplo a seguir mostra a página Controle de acesso (IAM) para um grupo de recursos.

    ![Controle de acesso do grupo de recursos – Guia Verificar acesso](./media/shared/rg-access-control.png)

1. Na guia **Verificar acesso**, na lista **Localizar**, selecione o usuário, o grupo, a entidade de serviço ou a identidade gerenciada para o qual você deseja acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar no diretório nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/shared/rg-check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições**.

    Nesse painel, você pode ver o acesso para a entidade de segurança selecionada nesse escopo e herdada para esse escopo. As atribuições em escopos filho não estão listadas. Você verá as seguintes atribuições:

    - Atribuições de função adicionadas com o Azure RBAC.
    - Atribuições de negação adicionadas usando o Azure Blueprints ou os aplicativos gerenciados do Azure.
    - Atribuições de Administrador de serviços ou Coadministrador clássicas para implantações clássicas. 

    ![Painel de atribuições de função e negação para um usuário](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Etapa 3: Verifique seu acesso

Siga estas etapas para verificar seu acesso aos recursos do Azure selecionados anteriormente.

1. Clique em **IAM (Controle de Acesso)**.

1. Na guia **Verificar acesso**, clique no botão **Exibir meus acessos**.

    Um painel atribuições é exibido para listar seu acesso nesse escopo e herdado para esse escopo. As atribuições em escopos filho não estão listadas.

    ![Painel de atribuições de função e negação](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Listar atribuições de função do Azure usando o portal do Azure](role-assignments-list-portal.md)
