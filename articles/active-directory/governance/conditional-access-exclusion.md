---
title: Usar revisões de acesso para gerenciar usuários excluídos das políticas de acesso condicional-Azure Active Directory | Microsoft Docs
description: Saiba como usar as revisões de acesso do Azure Active Directory (AD do Azure) para gerenciar usuários que foram excluídos das políticas de acesso condicional
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499909"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usar as revisões de acesso do Azure AD para gerenciar usuários excluídos das políticas de acesso condicional

Em um mundo ideal, todos os usuários seguiriam as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Este artigo descreve alguns exemplos em que exclusões podem ser necessárias e descreve como você, no papel do administrador de TI, pode gerenciar essa tarefa, evitar negligência com relação às exceções à política e fornecer aos auditores comprovação de que essas exceções são revisadas regularmente usando as revisões de acesso do Azure AD (Azure Active Directory).

> [!NOTE]
> Para usar as revisões de acesso do Azure AD, é necessário ter uma licença válida do Azure AD Premium P2, uma licença paga do Enterprise Mobility + Security E5 ou uma licença de avaliação. Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Por que excluir usuários das políticas?

Como um administrador de ti, você pode usar o [acesso condicional do Azure ad](../conditional-access/overview.md) para exigir que os usuários se autentiquem usando a MFA (autenticação multifator) ou entrem de uma rede ou dispositivo confiável. Durante o planejamento de implantação, você descobre que alguns desses requisitos não podem ser atendidos por todos os usuários. Por exemplo, há usuários que trabalham em um escritório remoto que não faz parte de sua rede interna ou há um executivo que usa um telefone antigo que não é compatível. A empresa exige que esses usuários tenham permissão para entrar e realizar seu trabalho, portanto, eles são excluídos das políticas de acesso condicional.

Como outro exemplo, você pode usar [locais nomeados](../conditional-access/location-condition.md) em acesso condicional para configurar um conjunto de regiões e localizações das quais você não deseja permitir que os usuários acessem seu locatário.

![Locais nomeados no acesso condicional](./media/conditional-access-exclusion/named-locations.png)

No entanto, em alguns casos, os usuários podem ter um motivo legítimo para entrar nesses países/regiões bloqueados. Por exemplo, os usuários podem estar viajando por motivos pessoais ou a trabalho. Neste exemplo, a política de acesso condicional para bloquear esses países/regiões pode ter um grupo de segurança de nuvem dedicado para os usuários que foram excluídos da política. Usuários que precisam de acesso quando estão viajando podem adicionar a si mesmos ao grupo usando o [Gerenciamento de Grupos de Autoatendimento do Azure AD](../users-groups-roles/groups-self-service-management.md).

Outro exemplo seria que você tem uma política de acesso condicional que [bloqueia a autenticação herdada para a grande maioria dos seus usuários](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). A Microsoft recomenda enfaticamente que você bloqueie o uso de protocolos herdados em seu locatário para melhorar sua postura de segurança. No entanto, se houver alguns usuários que realmente precisam usar métodos de autenticação herdados para acessar recursos por meio do Office 2010 ou de clientes baseados em SMTP/IMAP/POP, você poderá excluir esses usuários da política que bloqueia métodos de autenticação herdados.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são um desafio?

No Azure AD, você pode definir o escopo de uma política de acesso condicional para um conjunto de usuários. Você também pode excluir alguns desses usuários selecionando funções do Azure AD, usuários individuais ou convidados de usuários. É importante se lembrar de que, quando as exclusões são configuradas, a intenção da política não pode ser imposta para os usuários em questão. Se essas exclusões tiverem sido configuradas como uma lista de usuários individuais ou por meio de um grupo de segurança local herdado, a visibilidade da lista de exclusões será limitada (os usuários talvez não saibam de sua existência), bem como o controle do administrador de TI sobre ela (os usuários poderão ingressar no grupo de segurança para ignorar a política). Além disso, usuários que se qualificavam para a exclusão em um determinado momento podem deixar de precisar ou de estar qualificados para ela.

No início de uma exclusão, há uma pequena lista de usuários que ignoram a política. Com o passar do tempo, cada mais usuários são excluídos e a lista aumenta. Em algum momento, é necessário analisar a lista e confirmar que cada um desses usuários ainda precisa ser excluído. Do ponto de vista técnico, o gerenciamento da lista pode ser relativamente fácil, mas quem toma as decisões de negócios e como você garante que todo o processo seja auditável?

No entanto, se você configurar a exclusão para a política de acesso condicional usando um grupo do Azure AD, poderá usar as revisões de acesso como um controle de compensação, para impulsionar a visibilidade e reduzir o número de usuários que têm uma exceção.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão em uma política de acesso condicional

Siga estas etapas para criar um novo grupo do Azure AD e uma política de acesso condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Entre no Portal do Azure.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e clique em **Grupos**.

1. No menu superior, clique em **Novo Grupo** para abrir o painel do grupo.

1. Na lista **Tipo de grupo**, selecione **Segurança**. Especifique um nome e uma descrição.

1. Certifique-se de definir o tipo de **Associação** como **Atribuído**.

1. Selecione os usuários que devem fazer parte do grupo de exclusão e, em seguida, clique em **Criar**.

    ![Painel novo grupo no Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de acesso condicional que exclui o grupo

Agora você pode criar uma política de acesso condicional que usa esse grupo de exclusão.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e, em seguida, clique em **acesso condicional** para abrir a folha **políticas** .

1. Clique em **Nova política** para abrir o painel **Novo**.

1. Especifique um nome.

1. Em Atribuições, clique em **Usuários e grupos**.

1. Na guia **Incluir**, selecione **Todos os Usuários**.

1. Na guia **Excluir**, adicione uma marca de seleção a **Usuários e grupos** e, em seguida, clique em **Selecionar usuários excluídos**.

1. Selecione o grupo de exclusão que você criou.

    > [!NOTE]
    > Como melhor prática, é recomendável excluir pelo menos uma conta de administrador da política ao fazer testes para garantir que você não esteja bloqueado de seu locatário.

1. Continue com a configuração da política de acesso condicional com base em seus requisitos organizacionais.

    ![Selecionar o painel usuários excluídos em acesso condicional](./media/conditional-access-exclusion/select-excluded-users.png)

Vamos abordar dois exemplos em que você pode usar as revisões de acesso para gerenciar exclusões em políticas de acesso condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: Revisão de acesso para usuários que acessam de países/regiões bloqueados

Digamos que você tenha uma política de acesso condicional que bloqueie o acesso de determinados países/regiões. Ela inclui um grupo que é excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

> [!NOTE]
> Uma função de administrador global ou de administrador de usuários é necessária para criar revisões de acesso.

1. A revisão se repetirá semanalmente.

2. Ela nunca será encerrada para garantir que você esteja mantendo o grupo de exclusão o mais atualizado possível.

3. Todos os membros do grupo estarão no escopo da revisão.

4. Cada usuário terá que atestar automaticamente que eles ainda precisam ter acesso a partir desses países/regiões bloqueados, portanto, eles ainda precisam ser membros do grupo.

5. Se o usuário não responder à solicitação de revisão, ele será removido automaticamente do grupo e, portanto, não poderá mais acessar o locatário ao viajar para esses países/regiões.

6. Habilite as notificações por email para que os usuários sejam notificados do início e da conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: Revisão de acesso para usuários que acessam com a autenticação herdada

Digamos que você tenha uma política de acesso condicional que bloqueie o acesso para usuários usando autenticação herdada e versões de cliente mais antigas. Ela inclui um grupo que é excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

1. Essa revisão precisaria ser recorrente.

2. Todos no grupo precisam passar por ela.

3. Ela poderia ser configurada para listar os proprietários das unidades de negócios como revisores selecionados.

4. Aplique os resultados automaticamente e remova usuários que não foram aprovados para continuar usando métodos de autenticação herdados.

5. Pode ser útil habilitar as recomendações para que os revisores de grandes grupos possam tomar decisões com facilidade.

6. Habilite as notificações por email para que os usuários sejam notificados do início e da conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Dica de PRO**: Se você tem vários grupos de exclusão e, portanto, precisa criar várias revisões de acesso, agora temos uma API no ponto de extremidade beta do Microsoft Graph que permite criar e gerenciá-los de forma programática. Para começar, consulte a [Referência da API de revisões de acesso do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e o [Exemplo de recuperação de revisões de acesso do Azure AD por meio do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados e logs de auditoria da revisão de acesso

Agora que você tem tudo em vigor, grupo, política de acesso condicional e revisões de acesso, é hora de monitorar e acompanhar os resultados dessas revisões.

1. No portal do Azure, abra a folha **Revisões de acesso**.

1. Abra o controle e o programa que você criou para gerenciar o grupo de exclusão.

1. Clique em **Resultados** para ver quem foi aprovado para permanecer na lista e quem foi removido.

    ![Resultados de revisões de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

1. Em seguida, clique em **Logs de auditoria** para ver as ações que foram executadas durante a revisão.

    ![Acesso examina logs de auditoria listando ações](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de TI, você sabe que o gerenciamento de grupos de exclusão de suas políticas, às vezes, é inevitável. No entanto, a manutenção desses grupos, sua revisão regular pelo proprietário da empresa ou pelos próprios usuários e a auditoria das alterações podem facilitadas com as revisões de acesso do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
