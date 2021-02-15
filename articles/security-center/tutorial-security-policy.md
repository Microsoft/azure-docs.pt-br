---
title: Trabalhar com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 19128f0372f9a5bda0d16155167a507eccaf436a
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986570"
---
# <a name="manage-security-policies"></a>Gerenciar políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. 

## <a name="introduction-to-security-policies"></a>Introdução às políticas de segurança

Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir que você esteja em conformidade com os requisitos de segurança de sua empresa ou reguladores.

A central de segurança do Azure faz suas recomendações de segurança com base em suas políticas escolhidas. As políticas da central de segurança são baseadas em iniciativas de políticas criadas no Azure Policy. Você pode usar [Azure Policy](../governance/policy/overview.md) para gerenciar suas políticas e definir políticas em grupos de gerenciamento e em várias assinaturas.

A central de segurança oferece as seguintes opções para trabalhar com políticas de segurança:

* **Exibir e editar a política padrão interna** – quando você habilita a central de segurança, a iniciativa denominada "Azure Security benchmark" é automaticamente atribuída a todas as assinaturas registradas da central de segurança. Para personalizar essa iniciativa, você pode habilitar ou desabilitar políticas individuais dentro dela. Consulte a lista de [políticas de segurança internas](./policy-reference.md) para entender as opções disponíveis de forma integrada.

* **Adicione suas próprias políticas personalizadas** -se desejar personalizar as iniciativas de segurança aplicadas à sua assinatura, você poderá fazer isso na central de segurança. Em seguida, você receberá recomendações se os computadores não seguirem as políticas que você criar. Para obter instruções sobre como criar e atribuir políticas personalizadas, consulte [usando políticas de segurança personalizadas](custom-security-policies.md).

* **Adicionar políticas de conformidade regulatória** – o painel de conformidade regulatória da central de segurança mostra o status de todas as avaliações em seu ambiente no contexto de um padrão ou regulamento específico (como o Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Para obter mais informações, consulte [melhorar sua conformidade regulatória](security-center-compliance-dashboard.md).


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

    1. Para trabalhar com as políticas do setor, selecione **adicionar mais padrões**. Para obter mais informações, consulte [atualizar para pacotes de conformidade dinâmica](update-regulatory-compliance-packages.md).

    1. Para atribuir e gerenciar iniciativas personalizadas, selecione **Adicionar iniciativas personalizadas**. Para obter mais informações, consulte [usando políticas de segurança personalizadas](custom-security-policies.md).

    1. Para exibir e editar a política padrão, selecione **Exibir política efetiva** e prossiga conforme descrito abaixo. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Tela de política efetiva":::

       Essa tela de **política de segurança** reflete a ação executada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
       
       * Use os links na parte superior para abrir uma **atribuição** de política que se aplica à assinatura ou ao grupo de gerenciamento. Esses links permitem que você acesse a atribuição e edite ou desabilite a política. Por exemplo, se você vir que uma atribuição de política específica está efetivamente negando o Endpoint Protection, use o link para editar ou desabilitar a política.
       
       * Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. As configurações de cada política que se aplicam ao escopo são levadas em consideração e o resultado cumulativo das ações executadas pela política é mostrado. Por exemplo, se em uma atribuição da política estiver desabilitada, mas em outra definida como AuditIfNotExist, o efeito cumulativo aplicará AuditIfNotExist. O efeito mais ativo sempre tem precedência.
       
       * O efeito das políticas pode ser: Anexar, Auditar, AuditIfNotExists, Negar, DeployIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?

Você pode editar as políticas de segurança no portal Azure Policy usando a API REST ou o Windows PowerShell.

A central de segurança usa o Azure RBAC (controle de acesso baseado em função), que fornece funções internas que você pode atribuir a usuários, grupos e serviços do Azure. Quando os usuários abrem a central de segurança, eles veem apenas as informações relacionadas aos recursos que eles podem acessar. Isso significa que os usuários recebem a função de *proprietário*, *colaborador* ou *leitor* para a assinatura do recurso. Há também duas funções específicas da central de segurança:

- **Leitor de segurança**: tem direitos para exibir itens da central de segurança, como recomendações, alertas, política e integridade. Não é possível fazer alterações.
- **Administrador de segurança**: tem os mesmos direitos de exibição que o *leitor de segurança*. Também é possível atualizar a política de segurança e ignorar os alertas.


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

1. Selecione **Salvar**.

   > [!NOTE]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.

## <a name="next-steps"></a>Próximas etapas
Esta página explicou as políticas de segurança. Para obter informações relacionadas, consulte as seguintes páginas:

- [Saiba como definir políticas usando o PowerShell](../governance/policy/assign-policy-powershell.md) - 
- [Saiba como editar uma política de segurança no Azure Policy](../governance/policy/tutorials/create-and-manage.md) - 
- [Saiba como definir uma política em assinaturas ou em grupos de gerenciamento usando Azure Policy](../governance/policy/overview.md).
- [Saiba como habilitar a central de segurança em todas as assinaturas em um grupo de gerenciamento](onboard-management-group.md)