---
title: Gerenciar o acesso externo com Azure Active Directory acesso condicional
description: Como usar Azure Active Directory políticas de acesso condicional para proteger o acesso externo aos recursos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222302"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Gerenciar o acesso externo com políticas de acesso condicional 

O [acesso condicional](../conditional-access/overview.md) é a ferramenta que o Azure ad usa para reunir sinais, impor políticas e determinar se um usuário deve ter permissão de acesso a recursos. Para obter informações detalhadas sobre como criar e usar políticas de acesso condicional (políticas de acesso condicional), consulte [planejar uma implantação de acesso condicional](../conditional-access/plan-conditional-access.md). 

![Diagrama de sinais e decisões de acesso condicional](media/secure-external-access//7-conditional-access-signals.png)



Este artigo aborda a aplicação de políticas de acesso condicional a usuários externos e pressupõe que você não tem acesso à funcionalidade de [Gerenciamento de direitos](../governance/entitlement-management-overview.md) . As políticas de acesso condicional podem ser e são usadas junto com o gerenciamento de direitos.

Anteriormente neste conjunto de documentos, você [criou um plano de segurança](3-secure-access-plan.md) que descreveu:

* Aplicativos e recursos têm os mesmos requisitos de segurança e podem ser agrupados para acesso.

* Requisitos de entrada para usuários externos.

Você usará esse plano para criar suas políticas de acesso condicional para acesso externo. 

> [!IMPORTANT]
> Crie algumas contas de teste de usuário externo para que você possa testar as políticas criadas antes de aplicá-las a todos os usuários externos.

## <a name="conditional-access-policies-for-external-access"></a>Políticas de acesso condicional para acesso externo

Veja a seguir as práticas recomendadas relacionadas ao controle de acesso externo com políticas de acesso condicional.

* Se você não puder usar organizações conectadas no gerenciamento de direitos, crie um grupo de segurança do Azure AD ou grupo de Microsoft 365 para cada organização parceira com a qual você trabalha. Atribua todos os usuários desse parceiro ao grupo. Você pode usar esses grupos em políticas de acesso condicional.

* Crie o máximo possível de políticas de acesso condicional. Para aplicativos que têm as mesmas necessidades de acesso, adicione todos eles à mesma política.  
‎ 
   > [!NOTE]
   > As políticas de acesso condicional podem se aplicar a um máximo de 250 aplicativos. Se mais de 250 aplicativos tiverem as mesmas necessidades de acesso, crie políticas duplicadas. A política A será aplicada aos aplicativos 1-250, a política B será aplicada aos aplicativos 251-500, etc.

* Claramente nomes de políticas específicas ao acesso externo com uma Convenção de nomenclatura. Uma Convenção de nomenclatura é *ExternalAccess_actiontaken_AppGroup*. Por exemplo ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Bloquear todos os usuários externos de recursos

Você pode impedir que usuários externos acessem conjuntos específicos de recursos com políticas de acesso condicional. Depois de determinar o conjunto de recursos para o qual você deseja bloquear o acesso, crie uma política.

Para criar uma política que bloqueia o acesso de usuários externos a um conjunto de aplicativos:

1. Acesse o **portal do Azure**, selecione **Azure Active Directory**, selecione **segurança** e, em seguida, selecione **acesso condicional**.

2. Selecione **nova política** e insira um **nome**, por exemplo ExternalAccess_Block_FinanceApps

3. Selecione **usuários e grupo** s. Na guia incluir, escolha **Selecionar usuários e grupos** e, em seguida, selecione **todos os convidados e usuários externos**. 

4. Selecione **excluir** e insira os grupos de administradores e as contas de acesso de emergência (divisão-vidro).

5. Selecione **aplicativos de nuvem ou ações**, escolha **selecionar aplicativos**, selecione todos os aplicativos para os quais você deseja bloquear o acesso externo e escolha **selecionar**.

6. Selecione **condições**, selecione **locais**, em configurar Selecione **Sim** e selecione **qualquer local**.

7. Em controles de acesso, selecione **conceder**, altere a alternância para **Bloquear** e escolha **selecionar**.

8. Verifique se a configuração Habilitar política está definida como **somente relatório** e, em seguida, selecione **criar**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Bloquear o acesso externo a todos, exceto usuários externos específicos

Pode haver ocasiões em que você deseja bloquear usuários externos, exceto um grupo específico. Por exemplo, você pode querer bloquear todos os usuários externos, exceto aqueles que trabalham para a equipe de finanças dos aplicativos financeiros. Para fazer isso:

1. Crie um grupo de segurança para manter os usuários externos que devem acessar o grupo Finanças.

2. Siga as etapas 1-3 em bloquear acesso externo dos recursos acima.

3. Na etapa 4, adicione o grupo de segurança que você deseja excluir de ser bloqueado dos aplicativos de finanças.

4. Execute o restante das etapas.

## <a name="implement-conditional-access"></a>Implementar o acesso condicional

Muitas políticas de acesso condicional comuns são documentadas. Veja o seguinte que você pode adaptar para usuários externos.

* [Exigir autenticação multifator para todos os usuários](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Acesso condicional baseado em risco de usuário](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Exigir autenticação multifator para acesso de redes não confiáveis](../conditional-access/untrusted-networks.md) 

* [Exigir termos de uso](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar a postura de segurança desejada para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md) (você está aqui)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
