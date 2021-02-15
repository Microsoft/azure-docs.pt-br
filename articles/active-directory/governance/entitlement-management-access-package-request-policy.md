---
title: Configurações de solicitação de alteração para um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como alterar as configurações de solicitação de um pacote do Access no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7b70e8a408c22be0331bfd0dcbe01830b072ab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449974"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Configurações de solicitação de alteração para um pacote de acesso no gerenciamento de direitos do Azure AD

Como um Gerenciador de pacotes do Access, você pode alterar os usuários que podem solicitar um pacote do Access a qualquer momento editando a política ou adicionando uma nova política. Este artigo descreve como alterar as configurações de solicitação para um pacote do Access existente.

## <a name="choose-between-one-or-multiple-policies"></a>Escolha entre uma ou várias políticas

A maneira de especificar quem pode solicitar um pacote de acesso é com uma política. Antes de criar uma nova política ou editar uma política existente em um pacote do Access, você precisa determinar quantas políticas o pacote de acesso precisa. 

Ao criar um pacote do Access, você especifica a configuração de solicitação que cria uma política. A maioria dos pacotes de acesso terá uma única política, mas um único pacote de acesso pode ter várias políticas. Você criaria várias políticas para um pacote de acesso se quiser permitir que diferentes conjuntos de usuários recebam atribuições com diferentes configurações de solicitação e aprovação. 

Por exemplo, uma única política não pode ser usada para atribuir usuários internos e externos ao mesmo pacote de acesso. No entanto, você pode criar duas políticas no mesmo pacote de acesso, uma para usuários internos e outra para usuários externos. Se houver várias políticas que se aplicam a um usuário, elas serão solicitadas no momento da solicitação para selecionar a política à qual desejam ser atribuídos. O diagrama a seguir mostra um pacote de acesso com duas políticas.

![Várias políticas em um pacote do Access](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Quantas políticas serão necessárias?

| Cenário | Número de políticas |
| --- | --- |
| Quero que todos os usuários em meu diretório tenham as mesmas configurações de solicitação e aprovação para um pacote de acesso | Um |
| Quero que todos os usuários em determinadas organizações conectadas possam solicitar um pacote de acesso | Um |
| Quero permitir usuários em meu diretório e também usuários fora do meu diretório para solicitar um pacote de acesso | Vários |
| Desejo especificar configurações de aprovação diferentes para alguns usuários | Vários |
| Quero que alguns usuários acessem as atribuições de pacote para expirar enquanto outros usuários podem estender seu acesso | Vários |

Para obter informações sobre a lógica de prioridade que é usada quando várias políticas se aplicam, consulte [várias políticas](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Abrir um pacote do Access existente e adicionar uma nova política de configurações de solicitação

Se você tiver um conjunto de usuários que deve ter diferentes configurações de solicitação e aprovação, provavelmente precisará criar uma nova política. Siga estas etapas para começar a adicionar uma nova política a um pacote do Access existente:

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e em **Adicionar política**.

1. Você começará a usar a guia **noções básicas** . Digite um nome e uma descrição para a política.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique **Avançar** para abrir a guia **Solicitações**.

1. Altere os **usuários que podem solicitar** a configuração de acesso. Use as etapas nas seções a seguir para alterar a configuração para uma das seguintes opções: 
    - [Para usuários em seu diretório](#for-users-in-your-directory) 
    - [Para usuários que não estão em seu diretório](#for-users-not-in-your-directory)
    - [Nenhum (somente atribuições diretas do administrador)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Para usuários em seu diretório

Siga estas etapas se desejar permitir que os usuários em seu diretório possam solicitar esse pacote de acesso. Ao definir a política de solicitação, você pode especificar usuários individuais ou grupos de usuários mais comuns. Por exemplo, sua organização pode já ter um grupo como **todos os funcionários**.  Se esse grupo for adicionado na política para usuários que podem solicitar acesso, então qualquer membro desse grupo poderá solicitar acesso.

1. Na seção **Usuários que podem solicitar acesso**, clique em **Para usuários em seu diretório**.

    Quando você seleciona essa opção, novas opções aparecem para refinar ainda mais quem em seu diretório pode solicitar esse pacote de acesso.

    ![Pacotes de acesso-solicitações-para usuários em seu diretório](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Usuários e grupos específicos** | Escolha esta opção se desejar que apenas os usuários e grupos em seu diretório especificados possam solicitar esse pacote de acesso. |
    | **Todos os membros (exceto convidados)** | Escolha esta opção se desejar que todos os usuários Membros em seu diretório possam solicitar esse pacote de acesso. Essa opção não inclui nenhum usuário convidado que você possa ter convidado em seu diretório. |
    | **Todos os usuários (incluindo convidados)** | Escolha esta opção se desejar que todos os usuários Membros e usuários convidados em seu diretório possam solicitar esse pacote de acesso. |

    Os usuários convidados se referem a usuários externos que foram convidados em seu diretório com o [Azure ad B2B](../external-identities/what-is-b2b.md). Para obter mais informações sobre as diferenças entre usuários Membros e usuários convidados, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. Se você selecionou **usuários e grupos específicos**, clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione os usuários e grupos que você deseja adicionar.

    ![Pacotes de acesso-solicitações-Selecionar usuários e grupos](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Se você quiser exigir aprovação, use as etapas em [alterar configurações de aprovação para um pacote de acesso no gerenciamento de direitos do Azure ad](entitlement-management-access-package-approval-policy.md) para definir as configurações de aprovação.

1. Vá para a seção [habilitar solicitações](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>Para usuários que não estão em seu diretório

 **Os usuários que não estão em seu diretório** referem-se aos usuários que estão em outro diretório ou domínio do Azure AD. Esses usuários podem ainda não ter sido convidados para seu diretório. Os diretórios do AD do Azure devem ser configurados para permitir convites em **restrições de colaboração**. Para obter mais informações, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](../external-identities/delegate-invitations.md).

> [!NOTE]
> Uma conta de usuário convidado será criada para um usuário que ainda não está em seu diretório cuja solicitação foi aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um email de convite. Em vez disso, eles receberão um email quando sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando o usuário convidado não tiver mais nenhuma atribuição de pacote de acesso, porque sua última atribuição expirou ou foi cancelada, essa conta de usuário convidado será bloqueada de entrar e excluída subsequentemente. Se você quiser que os usuários convidados permaneçam em seu diretório indefinidamente, mesmo que eles não tenham atribuições de pacote de acesso, você poderá alterar as configurações de sua configuração de gerenciamento de direitos. Para obter mais informações sobre o objeto de usuário convidado, consulte [Propriedades de um usuário de colaboração Azure Active Directory B2B](../external-identities/user-properties.md).

Siga estas etapas se desejar permitir que os usuários que não estão em seu diretório solicitem este pacote de acesso:

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários que não estão em seu diretório**.

    Quando você seleciona essa opção, novas opções são exibidas.

    ![Pacotes de acesso-solicitações-para usuários que não estão em seu diretório](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha esta opção se desejar selecionar em uma lista de organizações que o administrador adicionou anteriormente. Todos os usuários das organizações selecionadas podem solicitar esse pacote de acesso. |
    | **Todas as organizações conectadas configuradas** | Escolha esta opção se todos os usuários de todas as suas organizações conectadas configuradas puderem solicitar esse pacote de acesso. Somente os usuários de organizações conectadas configuradas podem solicitar pacotes de acesso que são mostrados aos usuários de todas as organizações configuradas. |
    | **Todos os usuários (todas as organizações conectadas + quaisquer novos usuários externos)** | Escolha esta opção se algum usuário na Internet puder solicitar esse pacote de acesso.  Se eles não pertencerem a uma organização conectada em seu diretório, uma organização conectada será criada automaticamente para eles quando solicitarem o pacote. A organização conectada criada automaticamente estará em um estado **proposto** . Para obter mais informações sobre o Estado proposto, consulte [Propriedades de estado das organizações conectadas](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

1. Se você selecionou **organizações conectadas específicas**, clique em **adicionar diretórios** para selecionar em uma lista de organizações conectadas que seu administrador adicionou anteriormente.

1. Digite o nome ou nome de domínio para procurar uma organização conectada anteriormente.

    ![Pacotes de acesso-solicitações-selecionar diretórios](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Se a organização com a qual você deseja colaborar não estiver na lista, você poderá pedir ao administrador para adicioná-lo como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada](entitlement-management-organization.md).

1. Depois de selecionar todas as suas organizações conectadas, clique em **selecionar**.

    > [!NOTE]
    > Todos os usuários das organizações conectadas selecionadas poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista de permissão ou negação B2B do Azure. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../external-identities/allow-deny-list.md).

1. Se você quiser exigir aprovação, use as etapas em [alterar configurações de aprovação para um pacote de acesso no gerenciamento de direitos do Azure ad](entitlement-management-access-package-approval-policy.md) para definir as configurações de aprovação.
 
1. Vá para a seção [habilitar solicitações](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Nenhum (somente atribuições diretas do administrador)

Siga estas etapas se desejar ignorar as solicitações de acesso e permitir que os administradores atribuam usuários específicos diretamente a esse pacote de acesso. Os usuários não precisarão solicitar o pacote de acesso. Você ainda pode definir as configurações do ciclo de vida, mas não há configurações de solicitação.

1. Na seção **usuários que podem solicitar acesso** , clique em **nenhum (somente atribuições diretas do administrador**.

    ![Pacotes de acesso-solicitações-nenhuma somente atribuições diretas de administrador](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote do Access, você pode atribuir diretamente usuários internos e externos específicos ao pacote do Access. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório. Para obter informações sobre como atribuir um usuário diretamente, consulte [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md).

1. Pule para a seção [habilitar solicitações](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Abrir e editar uma política existente de configurações de solicitação

Para alterar as configurações de solicitação e aprovação de um pacote do Access, você precisa abrir a política correspondente. Siga estas etapas para abrir e editar as configurações de solicitação para um pacote de acesso:

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que você deseja editar.

    O painel detalhes da política é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **solicitações** para abrir as configurações de solicitação.

1. Use as etapas nas seções anteriores para alterar as configurações de solicitação conforme necessário.

1. Vá para a seção [habilitar solicitações](#enable-requests) .

## <a name="enable-requests"></a>Habilitar solicitações

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para usuários na política de solicitação para solicitar, mova a opção Habilitar alternar para **Sim**.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    Se você selecionou **nenhum (somente atribuições diretas de administrador)** e definir habilitar como **não**, os administradores não poderão atribuir diretamente este pacote de acesso.

    ![Pacote de acesso-configuração de política de habilitação de política](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Clique em **Avançar**.

1. Se você quiser exigir que os solicitantes forneçam informações adicionais ao solicitar acesso a um pacote do Access, use as etapas em []() para configurar informações do solicitante (versão prévia).

1. Defina as configurações do ciclo de vida.

1. Se você estiver editando uma política, clique em **Atualizar**. Se você estiver adicionando uma nova política, clique em **criar**.

## <a name="next-steps"></a>Próximas etapas

- [Alterar as configurações de aprovação de um pacote de acesso](entitlement-management-access-package-approval-policy.md)
- [Alterar as configurações do ciclo de vida de um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Exibir solicitações para um pacote de acesso](entitlement-management-access-package-requests.md)
