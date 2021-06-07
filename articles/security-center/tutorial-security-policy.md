---
title: Trabalhar com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança na central de segurança do Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101317"
---
# <a name="manage-security-policies"></a>Gerenciar políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. 

## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?

Você pode editar as políticas de segurança no portal Azure Policy usando a API REST ou o Windows PowerShell.

A central de segurança usa o Azure RBAC (controle de acesso baseado em função), que fornece funções internas que você pode atribuir a usuários, grupos e serviços do Azure. Quando os usuários abrem a central de segurança, eles veem apenas as informações relacionadas aos recursos que eles podem acessar. Isso significa que os usuários recebem a função de *proprietário*, *colaborador* ou *leitor* para a assinatura do recurso. Há também duas funções específicas da central de segurança:

- **Leitor de segurança**: tem direitos para exibir itens da central de segurança, como recomendações, alertas, política e integridade. Não é possível fazer alterações.
- **Administrador de segurança**: tem os mesmos direitos de exibição que o *leitor de segurança*. Também é possível atualizar a política de segurança e ignorar os alertas.

## <a name="manage-your-security-policies"></a>Gerenciar suas políticas de segurança

Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Security Center**, selecione **Security policy**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="A página de gerenciamento de política":::

   Na tela **Gerenciamento de políticas**, você pode ver o número de grupos de gerenciamento, assinaturas e workspaces, bem como a estrutura do grupo de gerenciamento.

1. Selecione a assinatura ou o grupo de gerenciamento cujas políticas você deseja visualizar.

1. A página política de segurança para essa assinatura ou grupo de gerenciamento é exibida. Ele mostra as políticas disponíveis e atribuídas.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Página política de segurança da central de segurança" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Se houver um rótulo "MG herdado" junto com a política padrão, significa que a política foi atribuída a um grupo de gerenciamento e herdada pela assinatura que você está exibindo.

1. Escolha entre as opções disponíveis nesta página:

    1. Para trabalhar com os padrões do setor, selecione **adicionar mais padrões**. Para obter mais informações, consulte [Personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).

    1. Para atribuir e gerenciar iniciativas personalizadas, selecione **Adicionar iniciativas personalizadas**. Para obter mais informações, consulte [usando regras e iniciativas de segurança personalizadas](custom-security-policies.md).

    1. Para exibir e editar a iniciativa padrão, selecione **Exibir política efetiva** e prossiga conforme descrito abaixo. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Tela de política efetiva":::

       Essa tela de **política de segurança** reflete a ação executada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
       
       * Use os links na parte superior para abrir uma **atribuição** de política que se aplica à assinatura ou ao grupo de gerenciamento. Esses links permitem que você acesse a atribuição e edite ou desabilite a política. Por exemplo, se você vir que uma atribuição de política específica está efetivamente negando o Endpoint Protection, use o link para editar ou desabilitar a política.
       
       * Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. As configurações de cada política que se aplicam ao escopo são levadas em consideração e o resultado cumulativo das ações executadas pela política é mostrado. Por exemplo, se em uma atribuição da política estiver desabilitada, mas em outra definida como AuditIfNotExist, o efeito cumulativo aplicará AuditIfNotExist. O efeito mais ativo sempre tem precedência.
       
       * O efeito das políticas pode ser: Anexar, Auditar, AuditIfNotExists, Negar, DeployIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.


## <a name="disable-security-policies-and-disable-recommendations"></a>Desabilitar políticas de segurança e desabilitar recomendações

Quando sua iniciativa de segurança disparar uma recomendação que seja irrelevante para o seu ambiente, você poderá impedir que essa recomendação apareça novamente. Para desabilitar uma recomendação, desabilite a política específica que gera a recomendação.

A recomendação que você deseja desabilitar ainda será exibida se for necessária para um padrão normativo que você aplicou com as ferramentas de conformidade regulatória da central de segurança. Mesmo que você tenha desabilitado uma política na iniciativa interna, uma política na iniciativa do padrão normativo ainda disparará a recomendação se for necessário para a conformidade. Não é possível desabilitar políticas de iniciativas normativas padrão.

Para obter mais informações sobre recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

1. Na central de segurança, na seção **política de & de conformidade** , selecione política de **segurança**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Iniciando o processo de gerenciamento de política na central de segurança do Azure":::

2. Selecione a assinatura ou o grupo de gerenciamento para o qual você deseja desabilitar a recomendação.

   > [!NOTE]
   > Lembre-se de que um grupo de gerenciamento aplica suas políticas a suas assinaturas. Portanto, se você desabilitar a política de uma assinatura e a assinatura pertencer a um grupo de gerenciamento que ainda usa a mesma política, continuará recebendo as recomendações de política. A política ainda será aplicada do nível de gerenciamento e as recomendações ainda serão geradas.

1. Selecione **Exibir política efetiva**.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Como abrir a política efetiva atribuída à sua assinatura":::

1. Selecione a política atribuída.

   ![selecionar política](./media/tutorial-security-policy/security-policy.png)

1. Na seção **parâmetros** , procure a política que invoca a recomendação que você deseja desabilitar e, na lista suspensa, selecione **desabilitada**

   ![desabilitar política](./media/tutorial-security-policy/disable-policy.png)

1. Clique em **Salvar**.

   > [!NOTE]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.

## <a name="next-steps"></a>Próximas etapas
Esta página explicou as políticas de segurança. Para obter informações relacionadas, consulte as seguintes páginas:

- [Saiba como definir políticas usando o PowerShell](../governance/policy/assign-policy-powershell.md)
- [Saiba como editar uma política de segurança no Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Saiba como definir uma política em assinaturas ou em grupos de gerenciamento usando Azure Policy](../governance/policy/overview.md)
- [Saiba como habilitar a central de segurança em todas as assinaturas em um grupo de gerenciamento](onboard-management-group.md)