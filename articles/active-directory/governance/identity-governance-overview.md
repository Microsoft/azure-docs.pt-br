---
title: Identity Governance – Azure Active Directory | Microsoft Docs
description: O Azure Active Directory Identity Governance permite que você equilibre a necessidade de segurança e a produtividade dos funcionários da sua organização com os processos e a visibilidade corretos.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362443"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

O Azure Active Directory (Azure AD) Identity Governance permite que você equilibre a necessidade da sua organização de segurança e a produtividade dos funcionários com os processos e a visibilidade corretos. Ele fornece recursos para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Esses recursos e aqueles relacionados ao Azure AD e Enterprise Mobility + Security permitem que você reduza o risco de acesso, protegendo, monitorando e auditando o acesso a ativos críticos, garantindo, ao mesmo tempo, a produtividade dos funcionários e dos parceiros empresariais.  

O Identity Governance dá às organizações a capacidade de fazer as seguintes tarefas entre funcionários, parceiros empresariais e fornecedores e entre serviços e aplicativos, localmente e em nuvens:

- Controlar o ciclo de vida de identidade
- Controlar o ciclo de vida de acesso
- Proteger o acesso privilegiado para administração

Especificamente, destina-se a ajudar as organizações a abordar estas quatro perguntas importantes:

- Quais usuários devem ter acesso a quais recursos?
- O que esses usuários estão fazendo com esse acesso?
- Existem controles organizacionais em vigor para gerenciar o acesso?
- Auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

O Identity Governance ajuda as organizações a alcançar um equilíbrio entre *produtividade* – Com que rapidez uma pessoa pode ter acesso aos recursos de que precisa, por exemplo, quando entra em minha organização? E *segurança* – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa?  O gerenciamento do ciclo de vida de identidades é a base para o Identity Governance e a governança efetiva em escala requer modernizar a infraestrutura de gerenciamento do ciclo de vida de identidades para aplicativos.

![Ciclo de vida de identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida de identidade para funcionários está vinculado à representação daquele usuário em um sistema HCM (gerenciamento de capital humano).  O Azure AD Premium mantém automaticamente as identidades de usuário das pessoas representadas no Workday e no SuccessFactors tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [guia de planejamento de provisionamento do usuário do aplicativo de RH na nuvem para o Azure Active Directory](../app-provisioning/plan-cloud-hr-provision.md).  O Azure AD Premium também inclui o [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais como SAP HCM, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem a colaboração com pessoas de fora da sua organização. A colaboração [B2B do Azure AD](/azure/active-directory/b2b/) permite compartilhar com segurança aplicativos e serviços da sua organização com usuários convidados e parceiros externos de qualquer organização, ao mesmo tempo mantendo o controle sobre seus próprios dados corporativos.  [O gerenciamento de direitos do Azure AD](entitlement-management-overview.md) permite que você selecione quais usuários da organização têm permissão para solicitar acesso e serem adicionados como convidados B2B ao diretório da sua organização, além de garantir que esses convidados sejam removidos quando não precisarem mais de acesso.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade do usuário foi criada.  Além disso, organizações empresariais precisam ser capazes de dimensionar com eficiência para poderem desenvolver e impor política de acesso e controles continuamente.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais.  Além disso, a IT pode envolver os próprios usuários.  Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Usuários convidados podem não estar cientes dos requisitos de manipulação para os dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../enterprise-users/groups-dynamic-membership.md), junto com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../external-identities/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](access-reviews-overview.md) recorrentes.   [O gerenciamento de direitos do Azure AD](entitlement-management-overview.md) também permite que você defina como os usuários solicitam acesso entre pacotes de associações de grupo e de equipe, funções de aplicativo e funções do SharePoint Online.

Quando um usuário tenta acessar aplicativos, o Azure AD impõe políticas de [Acesso Condicional](../conditional-access/index.yml). Por exemplo, políticas de Acesso Condicional podem incluir a exibição de [Termos de uso](../conditional-access/terms-of-use.md) e [garantir que o usuário tenha concordado com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado é descrito por outros fornecedores como uma funcionalidade separada do Identity Governance. No entanto, na Microsoft, acreditamos que controlar o acesso privilegiado é uma parte importante do Identity Governance, especialmente considerando o potencial de estrago que o uso indevido associado a esses direitos de administrador pode causar a uma organização. Funcionários, fornecedores e prestadores de serviço que assumem direitos administrativos precisam ser controlados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

O [PIM (Azure AD Privileged Identity Management)](../privileged-identity-management/pim-configure.md) fornece controles adicionais adaptados para proteger direitos de acesso para recursos, entre o Azure AD, o Azure e outros Microsoft Online Services.  O acesso Just-In-Time e as funcionalidades de alerta de mudança de função oferecidos pelo Azure AD PIM, além da autenticação multifator e do Acesso Condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger recursos da sua empresa (diretório, Microsoft 365 e funções de recurso do Azure). Assim como acontece com outras formas de acesso, as organizações podem usar revisões de acesso para configurar a recertificação recorrente de acesso para todos os usuários nas funções de administrador.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Funcionalidades de governança em outros recursos do Azure AD

Além dos recursos listados acima, os recursos adicionais do Azure AD que costumam ser usados para fornecer cenários de governança de identidade incluem:

| Funcionalidade | Cenário |Recurso
| ------- | --------------------- |-----|
|Ciclo de vida de identidade (funcionários)|Os administradores podem habilitar o provisionamento de contas de usuário de RH na nuvem do Workday ou do SuccessFactors ou de RH local.|[RH de nuvem para provisionamento de usuários do Azure AD](../app-provisioning/plan-cloud-hr-provision.md)|
|Ciclo de vida de identidade (convidados)|Os administradores podem habilitar a integração de usuários convidados por autoatendimento por meio de outro locatário do Azure AD, da federação direta, da OTP (senha de uso único) ou de contas do Google.  Os usuários convidados são provisionados e desprovisionados automaticamente, sujeitos às políticas de ciclo de vida.|[Gerenciamento de direitos](entitlement-management-overview.md) usando [B2B](../external-identities/what-is-b2b.md)|
|Gerenciamento de direitos|Os proprietários de recursos podem criar pacotes de acesso contendo aplicativos, equipes, grupos do Azure AD e do Microsoft 365 e sites do SharePoint Online.|[Gerenciamento de direitos](entitlement-management-overview.md)|
|Solicitações de acesso|Os usuários finais podem solicitar associação a grupos ou acesso a aplicativos. Os usuários finais, incluindo convidados de outras organizações, podem solicitar acesso a pacotes de acesso.|[Gerenciamento de direitos](entitlement-management-overview.md)|
|Fluxo de trabalho|Os proprietários de recursos podem definir aprovadores e aprovadores de escalonamentos para solicitações de acesso, bem como aprovadores para solicitações de ativação de função.  |[Gerenciamento de direitos](entitlement-management-overview.md) e [PIM](../privileged-identity-management/pim-configure.md)|
|Gerenciamento de políticas e funções|O administrador pode definir políticas de acesso condicional para acesso em tempo de execução a aplicativos.  Os proprietários de recursos podem definir políticas de acesso do usuário por meio de pacotes de acesso.|Políticas de [acesso condicional](../conditional-access/overview.md) e [gerenciamento de direitos](entitlement-management-overview.md)|
|Certificação de acesso|Os administradores podem habilitar recertificação de acesso recorrente para: Aplicativos SaaS ou associações a grupos de nuvem, atribuições de função do Azure AD ou de Recursos do Azure. Remover automaticamente o acesso a recursos, bloquear o acesso de convidados e excluir contas de convidado.|[Revisões de acesso](access-reviews-overview.md), também na superfície do [PIM](../privileged-identity-management/pim-how-to-start-security-review.md)|
|Cumprimento e provisionamento|Provisionamento e desprovisionamento automáticos em aplicativos conectados ao Azure AD, incluindo por meio do SCIM e em sites do SharePoint Online. |[provisionamento do usuário](../app-provisioning/user-provisioning.md)|
|Relatórios e análise|Os administradores podem recuperar logs de auditoria das atividades recentes de provisionamento e logon de usuários. Integração ao Azure Monitor e 'quem tem acesso' por meio de pacotes de acesso.|[Monitoramento](../reports-monitoring/overview-reports.md) e [relatórios do Azure AD](../reports-monitoring/overview-monitoring.md)|
|Acesso privilegiado|Fluxos de trabalho de acesso, alertas e aprovação Just-In-Time e agendados para as funções do Azure AD (incluindo funções personalizadas) e de Recurso do Azure.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Auditoria|Os administradores podem ser alertados sobre a criação de contas de administrador.|[Alertas do Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Introdução

Confira a guia Introdução do **Identity Governance** no portal do Azure para começar a usar o gerenciamento de direitos, as revisões de acesso, o Privileged Identity Management e os Termos de uso.

![Introdução ao Identity Governance](./media/identity-governance-overview/getting-started.png)


Se você tiver algum comentário sobre os recursos do Identity Governance, clique em **Tem comentários?** no portal do Azure para enviar seus comentários. A equipe revisa seus comentários regularmente.

Embora não haja nenhuma solução nem recomendação perfeita para todos os clientes, os guias de configuração a seguir também fornecem as políticas de linha de base que a Microsoft recomenda que você siga para garantir uma força de trabalho mais segura e produtiva.

- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protegendo o acesso privilegiado](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice – funções com privilégios mínimos para gerenciamento em recursos do Identity Governance

A melhor prática é usar a função menos privilegiada para executar tarefas administrativas no Identity Governance. Recomendamos que você use o Azure AD PIM para ativar uma função conforme necessário para executar essas tarefas. As seguintes funções são de diretórios menos privilegiados para configurar os recursos do Identity Governance:

| Recurso | Função com privilégios mínimos |
| ------- | --------------------- |
| Gerenciamento de direitos | Administrador do usuário (com exceção da adição de sites do SharePoint Online a catálogos, que requer Administrador global) |
| Análises de acesso | Administrador de usuário (com exceção das revisões de acesso das funções do Azure ou do Azure AD, que exige Administrador de funções com privilégios) |
|Privileged Identity Management | Administrador de função com privilégios |
| Termos de uso | Administrador de segurança ou Administrador de acesso condicional |

## <a name="next-steps"></a>Próximas etapas

- [O que é o gerenciamento de direitos do Azure AD?](entitlement-management-overview.md)
- [Quais são as revisões de acesso do Azure AD?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de uso?](../conditional-access/terms-of-use.md)