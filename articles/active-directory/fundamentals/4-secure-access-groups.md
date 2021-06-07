---
title: Proteger o acesso externo com grupos em Azure Active Directory e Microsoft 365
description: Grupos de Azure Active Directory e Microsoft 365 podem ser usados para aumentar a segurança quando usuários externos acessam seus recursos.
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
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560609"
---
# <a name="securing-external-access-with-groups"></a>Protegendo o acesso externo com grupos 

Os grupos são uma parte essencial de qualquer estratégia de controle de acesso. Grupos de segurança do Azure Active Directory (Azure AD) e grupos de Microsoft 365 (M365) podem ser usados como base para proteger o acesso aos recursos.

Os grupos são a melhor opção para usar como base para os seguintes mecanismos de controle de acesso:

* Políticas de acesso condicional

* Pacotes de acesso de gerenciamento de direitos 

* Acesso a recursos do M365, Microsoft Teams e sites do SharePoint

Os grupos têm as seguintes funções:

* Proprietários – proprietários de grupo gerenciam as configurações de grupo e sua associação.

* Membros – Membros que herdam as permissões e o acesso atribuído ao grupo.

* Convidados – convidados são membros de fora da sua organização. 

## <a name="determine-your-group-strategy"></a>Determinar sua estratégia de grupo

Ao desenvolver sua estratégia de grupo para proteger o acesso externo aos seus recursos, considere a [postura de segurança desejada](1-secure-access-posture.md) para determinar as opções a seguir.

* **Quem deve ser capaz de criar grupos?** Você deseja que somente os administradores criem grupos ou deseja que os funcionários e ou usuários externos também criem esses grupos.

   * *Por padrão, qualquer membro de locatário pode criar grupos de segurança do Azure ad*. 

      * Você pode [restringir o acesso ao portal para não administradores](../develop/howto-restrict-your-app-to-a-set-of-users.md) e desabilitar a capacidade de criação de grupo no [PowerShell.](../enterprise-users/groups-troubleshooting.md) 

      * Você também pode [Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory](../enterprise-users/groups-self-service-management.md). 

   * *Por padrão, todos os usuários podem criar grupos e grupos M365 abertos para todos os usuários (internos e externos) em seu locatário para ingressar*. 

      * [Você pode restringir a criação de Microsoft 365 grupo](/microsoft-365/solutions/manage-creation-of-groups) aos membros de um grupo de segurança específico. Use o Windows PowerShell para definir essa configuração. 

* **Quem deve ser capaz de convidar pessoas para grupos?** Todos os membros do grupo podem adicionar outros membros ou apenas os proprietários do grupo podem adicionar membros?

* **Quem pode ser convidado para grupos?** Por padrão, usuários externos podem ser adicionados a grupos. 

### <a name="assign-users-to-groups"></a>Atribuir usuários a grupos

Os usuários podem ser atribuídos a grupos manualmente com base nos atributos de usuário em seu objeto de usuário ou em outros critérios. Os usuários só podem ser atribuídos a grupos dinamicamente com base em seus atributos.

Por exemplo, você pode atribuir usuários a grupos com base em seus:

* cargo ou departamento específico do trabalho

* organização parceira à qual eles pertencem (manualmente ou por meio de organizações conectadas)

* tipo de usuário (membro ou convidado)

* participação em um projeto específico (manualmente)

* local

Grupos dinâmicos podem conter usuários ou dispositivos, mas não ambos. Você adiciona consultas com base em atributos de usuário para atribuir usuários ao grupo dinâmico. O exemplo abaixo mostra consultas que adicionam usuários ao grupo se eles forem Membros (não convidados) e no departamento financeiro.

![Captura de tela da configuração de regras de associação dinâmica.](media/secure-external-access/4-dynamic-membership-rules.png)

Para obter mais informações sobre grupos dinâmicos, consulte [criar ou atualizar um grupo dinâmico no Azure Active Directory.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Não use grupos para várias finalidades

Ao usar grupos para fins de acesso de segurança ou recursos, é importante que eles tenham uma única função. Se um grupo for usado para conceder acesso a recursos, ele não deverá ser usado para nenhuma outra finalidade. Se um grupo for usado para fins genéricos, como para definir o local ou a associação da equipe, ele também não deverá ser usado para proteger o acesso. 

Recomendamos uma Convenção de nomenclatura para grupos de segurança que tornam a finalidade clara. Por exemplo:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Tipos de grupos

Os grupos de segurança do Azure AD e os grupos de Microsoft 365 podem ser criados no portal do Azure AD ou no portal de administração do M365. Ambos os tipos podem ser usados como base para proteger o acesso externo:

|Considerações | Grupos de segurança do Azure AD (manual e dinâmico)| Grupos de Microsoft 365 |
| - | - | - |
| O que o grupo pode conter?| Usuários<br>Grupos<br>Princípios de serviço<br>Dispositivos| Somente usuários |
| Onde o grupo é criado?| Portal do Azure AD<br>Portal do M365 (se para ser habilitado para email)<br>PowerShell<br>Microsoft Graph<br>Portal do usuário final| Portal do M365<br>Portal do Azure AD<br>PowerShell<br>Microsoft Graph<br>Em aplicativos Microsoft 365 |
| Quem cria por padrão?| Administradores <br>Usuários finais| Administradores<br>Usuários finais |
| Quem pode ser adicionado por padrão?| Usuários internos (Membros)| Membros do locatário e convidados de qualquer organização |
| A que ele concede acesso?| Somente os recursos aos quais ele foi atribuído.| Todos os recursos relacionados ao grupo:<br>(Caixa de correio de grupo, site, equipe, chats e outros recursos de M365 incluídos)<br>Quaisquer outros recursos aos quais o grupo é adicionado |
| Pode ser usado com| Acesso condicional<br>Gerenciamento de direitos<br>Licenciamento de grupo| Acesso condicional<br>Gerenciamento de direitos<br>Rótulos de confidencialidade |



Use grupos de Microsoft 365 para criar e gerenciar um conjunto de recursos de Microsoft 365, como uma equipe e seus sites e conteúdo associados. Eles são uma ótima opção para um esforço baseado em projeto. 

 

## <a name="azure-ad-security-groups"></a>Grupos de segurança do Azure AD 

Os [grupos de segurança do Azure ad](./active-directory-manage-groups.md) podem conter usuários ou dispositivos e podem ser usados para gerenciar o acesso ao 

* Recursos do Azure, como aplicativos Microsoft 365, aplicativos personalizados e aplicativos SaaS (software como serviço), como ServiceNow do dropbox.

* Dados e assinaturas do Azure.

* Serviços do Azure.

Os grupos de segurança do Azure AD também podem ser usados para:

* Atribua licenças para serviços como M365, Dynamics 365 e Enterprise Mobility e Security. Para obter mais informações, consulte [Licenciamento baseado em grupo](./active-directory-licensing-whatis-azure-portal.md).

* atribua permissões elevadas. Para obter mais informações, consulte [usar grupos de nuvem para gerenciar atribuições de função (versão prévia](../roles/groups-concept.md)). 

Para criar um grupo [no portal do Azure](./active-directory-groups-create-azure-portal.md) navegue até Azure Active Directory e, em seguida, para grupos. Você também pode criar grupos de segurança do Azure AD usando [cmdlets do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md). 

> [!NOTE]
> Um grupo de segurança pode ser usado para atribuição de até 1500 aplicativos, mas não mais. 

![Captura de tela da criação de um grupo de segurança.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Para criar um grupo de segurança habilitado para email, vá para o [centro de administração do Microsoft 365](https://admin.microsoft.com/)**. Você não pode criá-lo no portal do Azure AD. 
<br>Você deve habilitar um grupo de segurança para email no momento da criação. Você não pode habilitá-lo mais tarde.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organizações híbridas e grupos de segurança do Azure AD

As organizações híbridas têm uma infraestrutura local e uma infraestrutura de nuvem do Azure AD. Muitas organizações híbridas que usam Active Directory criar seus grupos de segurança localmente e sincronizá-los para a nuvem. Usando esse método, somente os usuários no ambiente local podem ser adicionados aos grupos de segurança.

**Proteja sua infraestrutura local contra o comprometimento, pois uma violação local pode ser usada para obter acesso ao seu locatário de Microsoft 365**. Consulte [protegendo Microsoft 365 de ataques locais](./protect-m365-from-on-premises-attacks.md) para obter diretrizes.

## <a name="microsoft-365-groups"></a>Grupos de Microsoft 365

[Grupos de Microsoft 365](/microsoft-365/admin/create-groups/office-365-groups) são o serviço de associação fundamental que controla todo o acesso em M365. Eles podem ser criados no [portal do Azure](https://portal.azure.com/)ou no portal do [M365](https://admin.microsoft.com/). Quando um grupo de M365 é criado, você concede acesso a um grupo de recursos usados para colaborar. Consulte [visão geral de grupos de Microsoft 365 para administradores](/microsoft-365/admin/create-groups/office-365-groups) para obter uma lista completa desses recursos.

Os grupos de M365 têm as nuances a seguir para suas funções

* **Proprietários** -os proprietários do grupo podem adicionar ou remover membros e ter permissões exclusivas como a capacidade de excluir conversas da caixa de entrada compartilhada ou alterar as configurações do grupo. Os proprietários do grupo podem renomear o grupo, atualizar a descrição ou a imagem e muito mais.

* **Membros** -os membros podem acessar tudo no grupo, mas não podem alterar as configurações de grupo. Por padrão, os membros do grupo podem convidar convidados para ingressar no grupo, embora você possa [controlar essa configuração](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Convidados** -grupos convidados são membros de fora da sua organização. Por padrão, os convidados têm alguns limites para a funcionalidade nas equipes.

 

### <a name="m365-group-settings"></a>Configurações do grupo M365

Você seleciona alias de email, privacidade e se deseja habilitar o grupo para equipes no momento da configuração. 

![Captura de tela da edição Microsoft 365 configurações de grupo](media/secure-external-access/4-edit-group-settings.png)

Após a instalação, você adiciona membros e define as configurações de uso de email, etc.

### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar a postura de segurança desejada para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Use grupos de segurança](4-secure-access-groups.md) (você está aqui.)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)