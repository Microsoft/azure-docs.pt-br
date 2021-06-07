---
title: Gerenciar usuários excluídos das políticas de Acesso Condicional – Azure AD
description: Saiba como usar revisões de acesso do Azure AD (Azure Active Directory) para gerenciar usuários que foram excluídos de políticas de Acesso Condicional
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532681"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usar revisões de acesso do Azure AD para gerenciar usuários excluídos das políticas de Acesso Condicional

Em um mundo ideal, todos os usuários seguem as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Este artigo aborda alguns exemplos de situações em que podem ser necessárias exclusões. Você, como administrador de TI, pode gerenciar essa tarefa, evitar negligência com relação às exceções à política e fornecer aos auditores comprovação de que essas exceções são revisadas regularmente usando as revisões de acesso do Azure AD (Azure Active Directory).

>[!NOTE]
> Para usar as revisões de acesso do Azure AD, é necessário ter uma licença válida do Azure AD Premium P2, uma licença paga do Enterprise Mobility + Security E5 ou uma licença de avaliação. Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Por que excluir usuários das políticas?

Digamos que, como administrador, você decida usar o [Acesso Condicional do Azure AD](../conditional-access/concept-conditional-access-policy-common.md) para exigir a MFA (autenticação multifator) e limitar as solicitações de autenticação a redes ou dispositivos específicos. Durante o planejamento da implantação, você percebe que nem todos os usuários podem atender a esses requisitos. Por exemplo, você pode ter usuários que trabalham em escritórios remotos e que não fazem parte da sua rede interna. Também pode ser necessário acomodar os usuários que se conectam usando dispositivos sem suporte enquanto aguardam a substituição desses dispositivos. Em suma, a empresa precisa que esses usuários entrem e realizem o trabalho deles para que você os exclua das políticas de Acesso Condicional.

Em outro exemplo, você pode usar [localizações nomeadas](../conditional-access/location-condition.md) no Acesso Condicional para especificar um conjunto de países e regiões dos quais não deseja permitir que os usuários acessem seu locatário.

![Localizações nomeadas no Acesso Condicional](./media/conditional-access-exclusion/named-locations.png)

Alguns usuários ainda podem ter um motivo válido para entrar nesses países/regiões bloqueados. Por exemplo, os usuários podem estar viajando a trabalho e precisam acessar recursos corporativos. Nesse caso, a política de Acesso Condicional para bloquear esses países/essas regiões poderia usar um grupo de segurança de nuvem para os usuários excluídos da política. Usuários que precisam de acesso quando estão viajando podem adicionar a si mesmos ao grupo usando o [Gerenciamento de Grupos de Autoatendimento do Azure AD](../enterprise-users/groups-self-service-management.md).

Outro exemplo pode ser quando você tem uma política de Acesso Condicional que [bloqueia a autenticação herdada para a maioria dos usuários](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). No entanto, se houver alguns usuários que precisem usar métodos de autenticação herdados para acessar recursos por meio do Office 2010 ou de clientes baseados em SMTP/IMAP/POP, você poderá excluir esses usuários da política que bloqueia métodos de autenticação herdados.

>[!NOTE]
>A Microsoft recomenda enfaticamente que você bloqueie o uso de protocolos herdados em seu locatário para melhorar sua postura de segurança.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são um desafio?

No Azure AD, é possível definir o escopo de uma política de Acesso Condicional como um conjunto de usuários. Você também pode configurar exclusões selecionando funções, usuários individuais ou convidados do Azure AD. Você deve ter em mente que, quando as exclusões são configuradas, a intenção da política não pode ser imposta a usuários excluídos. Se as exclusões forem configuradas usando uma lista de usuários ou grupos de segurança locais herdados, você terá visibilidade limitada sobre elas. Como resultado:

- Os usuários podem não saber que eles foram excluídos.

- Os usuários podem entrar no grupo de segurança para ignorar a política.

- Os usuários excluídos podem ter se qualificado para a exclusão antes, mas podem não ser mais qualificados para ela.

Frequentemente, quando você configura uma exclusão pela primeira vez, há uma breve lista de usuários que ignoram a política. Com o passar do tempo, cada vez mais usuários são adicionados à exclusão e a lista aumenta. Em algum ponto, você precisa examinar a lista e confirmar se cada um desses usuários ainda se qualifica para a exclusão. Do ponto de vista técnico, o gerenciamento da lista de exclusões pode ser relativamente fácil, mas quem toma as decisões de negócios e como você garante que todo o processo seja auditável? No entanto, se você configurar a exclusão usando um grupo do Azure AD, poderá usar revisões de acesso como um controle de compensação, para aumentar a visibilidade e reduzir o número de usuários excluídos.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão em uma política de Acesso Condicional

Siga estas etapas para criar um grupo do Azure AD e uma política de Acesso Condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Entre no portal do Azure.

2. No painel de navegação esquerdo, clique em **Azure Active Directory** e clique em **Grupos**.

3. No menu superior, clique em **Novo Grupo** para abrir o painel do grupo.

4. Na lista **Tipo de grupo**, selecione **Segurança**. Especifique um nome e uma descrição.

5. Certifique-se de definir o tipo de **Associação** como **Atribuído**.

6. Selecione os usuários que devem fazer parte do grupo de exclusão e, em seguida, clique em **Criar**.

![Painel Novo grupo no Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de Acesso Condicional que exclui o grupo

Agora, você pode criar uma política de Acesso Condicional que usa esse grupo de exclusão.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e, em seguida, clique em **Acesso Condicional** para abrir a folha **Políticas**.

2. Clique em **Nova política** para abrir o painel **Novo**.

3. Especifique um nome.

4. Em Atribuições, clique em **Usuários e grupos**.

5. Na guia **Incluir**, selecione **Todos os Usuários**.

6. Na guia **Excluir**, adicione uma marca de seleção a **Usuários e grupos** e, em seguida, clique em **Selecionar usuários excluídos**.

7. Selecione o grupo de exclusão que você criou.

   > [!NOTE] 
   > Como melhor prática, é recomendável excluir pelo menos uma conta de administrador da política ao fazer testes para garantir que você não esteja bloqueado de seu locatário.

8. Continue configurando a política de Acesso Condicional com base nos requisitos da organização.

![Painel Selecionar usuários excluídos no Acesso Condicional](./media/conditional-access-exclusion/select-excluded-users.png)
  
Vamos abordar dois exemplos em que você pode usar as revisões de acesso para gerenciar exclusões nas políticas de Acesso Condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: Revisão de acesso para usuários que acessam em regiões/países bloqueados

Digamos que você tenha uma política de Acesso Condicional que bloqueia o acesso de certos países/regiões. Ela inclui um grupo que é excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

> [!NOTE] 
> É necessário ter a função Administrador global ou Administrador de usuário para criar revisões de acesso.

1. A revisão acontecerá semanalmente.

2. Ela nunca será encerrada para que você mantenha esse grupo de exclusão o mais atualizado.

3. Todos os membros do grupo estarão no escopo da revisão.

4. Cada usuário precisará atestar que ainda precisa de acesso desses países/regiões bloqueados e, portanto, eles ainda precisam ser membros do grupo.

5. Se o usuário não responder à solicitação de revisão, ele será removido automaticamente do grupo e não poderá mais ter acesso ao locatário quando estiver viajando para esses países/regiões.

6. Habilite as notificações por email para informar os usuários sobre o início e a conclusão da revisão de acesso.

    ![Painel Criar uma revisão de acesso para o exemplo 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: Revisão de acesso para usuários que acessam com a autenticação herdada

Digamos que você tenha uma política de acesso condicional que bloqueia o acesso para usuários que usam autenticação herdada e versões de cliente mais antigas e que ela inclua um grupo excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

1. Essa revisão precisaria ser recorrente.

2. Todos no grupo precisam passar por ela.

3. Ela poderia ser configurada para listar os proprietários das unidades de negócios como revisores selecionados.

4. Aplique os resultados automaticamente e remova usuários que não foram aprovados para continuar usando métodos de autenticação herdados.

5. Pode ser útil habilitar as recomendações para que os revisores de grandes grupos possam tomar decisões com facilidade.

6. Habilite as notificações por email para que os usuários sejam notificados do início e da conclusão da revisão de acesso.

    ![Painel Criar uma revisão de acesso para o exemplo 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Se você tem vários grupos de exclusão e, portanto, precisa criar várias revisões de acesso, agora temos uma API no ponto de extremidade beta do Microsoft Graph que permite criar e gerenciá-los de maneira programática. Para começar, consulte a [Referência da API de revisões de acesso do Azure AD](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) e o [Exemplo de recuperação de revisões de acesso do Azure AD por meio do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados e logs de auditoria da revisão de acesso

Agora que você tem tudo em vigor – grupo, política de Acesso Condicional e revisões de acesso –, chegou a hora de monitorar e acompanhar os resultados dessas revisões.

1. No portal do Azure, abra a folha **Revisões de acesso**.

2. Abra o controle e o programa que você criou para gerenciar o grupo de exclusão.

3. Clique em **Resultados** para ver quem foi aprovado para permanecer na lista e quem foi removido.

    ![Os resultados das revisões de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

4. Em seguida, clique em **Logs de auditoria** para ver as ações que foram executadas durante a revisão.

    ![Ações de listagem de logs de auditoria das revisões de acesso](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de TI, você sabe que o gerenciamento de grupos de exclusão de suas políticas, às vezes, é inevitável. No entanto, com as revisões de acesso do Azure AD, pode ficar mais fácil realizar a manutenção desses grupos, a revisão regular deles pelo proprietário da empresa ou pelos próprios usuários e a auditoria das alterações.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)