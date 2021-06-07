---
title: Criar um novo pacote de acesso no gerenciamento de direitos-Azure AD
description: Saiba como criar um novo pacote de acesso de recursos que você deseja compartilhar no gerenciamento de direitos Azure Active Directory.
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
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011078"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Criar um novo pacote de acesso no gerenciamento de direitos do Azure AD

Um pacote do Access permite que você faça uma configuração única de recursos e políticas que administram automaticamente o acesso para a vida útil do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Visão geral

Todos os pacotes de acesso devem ser colocados em um contêiner chamado catálogo. Um catálogo define quais recursos você pode adicionar ao seu pacote de acesso. Se você não especificar um catálogo, seu pacote de acesso será colocado no catálogo geral. No momento, não é possível mover um pacote do Access existente para um catálogo diferente.

Se você for um Gerenciador de pacotes do Access, não poderá adicionar recursos que você possui a um catálogo. Você está restrito a usar os recursos disponíveis no catálogo. Se precisar adicionar recursos a um catálogo, você poderá solicitar o proprietário do catálogo.

Todos os pacotes de acesso devem ter pelo menos uma política. As políticas especificam quem pode solicitar o pacote de acesso e também as configurações de aprovação e ciclo de vida. Ao criar um novo pacote do Access, você pode criar uma política inicial para os usuários em seu diretório, para os usuários que não estão em seu diretório, somente para atribuições diretas de administrador ou você pode optar por criar a política mais tarde.

![Criar um pacote de acesso](./media/entitlement-management-access-package-create/access-package-create.png)

Aqui estão as etapas de alto nível para criar um novo pacote de acesso.

1. Em governança de identidade, inicie o processo para criar um novo pacote de acesso.

1. Selecione o catálogo no qual você deseja criar o pacote de acesso.

1. Adicione recursos do catálogo ao seu pacote de acesso.

1. Atribua funções de recurso para cada recurso.

1. Especifique os usuários que podem solicitar acesso.

1. Especifique as configurações de aprovação.

1. Especifique as configurações do ciclo de vida.

## <a name="start-new-access-package"></a>Iniciar novo pacote de acesso

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. Entre no [portal do Azure](https://portal.azure.com).

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Pacotes de acesso**.

1. Clique em **Novo pacote de acesso**.
   
    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Noções básicas

Na guia **noções básicas** , você dá um nome ao pacote de acesso e especifica em qual catálogo criar o pacote de acesso.

1. Insira um nome de exibição e uma descrição para o pacote de acesso. Os usuários verão essas informações quando enviarem uma solicitação para o pacote de acesso.

1. Na lista suspensa **Catálogo** , selecione o catálogo no qual você deseja criar o pacote de acesso. Por exemplo, você pode ter um proprietário de catálogo que gerencia todos os recursos de marketing que podem ser solicitados. Nesse caso, você pode selecionar o catálogo de marketing.

    Você verá apenas os catálogos para os quais você tem permissão para criar pacotes de acesso. Para criar um pacote do Access em um catálogo existente, você deve ser um administrador global ou administrador de usuários, ou você deve ser um proprietário do catálogo ou um Gerenciador de pacotes do Access nesse catálogo.

    ![Pacote de acesso-noções básicas](./media/entitlement-management-access-package-create/basics.png)

    Se você for um administrador global, um administrador de usuário ou um criador de catálogo e quiser criar seu pacote de acesso em um novo catálogo que não esteja listado, clique em **criar novo catálogo**. Insira o nome do catálogo e a descrição e, em seguida, clique em **criar**.

    O pacote de acesso que você está criando e todos os recursos incluídos nele serão adicionados ao novo catálogo. Você também pode adicionar mais proprietários de catálogo posteriormente.

1. Clique em **Próximo**.

## <a name="resource-roles"></a>Funções de recurso

Na guia **funções de recurso** , selecione os recursos a serem incluídos no pacote de acesso. Os usuários que solicitam e recebem o pacote de acesso receberão todas as funções de recurso no pacote de acesso.

1. Clique no tipo de recurso que você deseja adicionar (**grupos e equipes**, **aplicativos** ou **sites do SharePoint**).

1. No painel Selecionar que aparece, selecione um ou mais recursos na lista.

    ![Pacotes de acesso-funções de recurso](./media/entitlement-management-access-package-create/resource-roles.png)

    Se você estiver criando o pacote de acesso no catálogo geral ou em um novo catálogo, poderá escolher qualquer recurso do diretório que você possui. Você deve ser pelo menos um administrador global, um administrador de usuário ou um criador de catálogo.

    Se você estiver criando o pacote de acesso em um catálogo existente, poderá selecionar qualquer recurso que já esteja no catálogo sem o proprietário dele.

    Se você for um administrador global, um administrador de usuário ou proprietário do catálogo, terá a opção adicional de selecionar os recursos que possui, que ainda não estão no catálogo. Se você selecionar recursos que não estão atualmente no catálogo selecionado, esses recursos também serão adicionados ao catálogo para que outros administradores de catálogo criem pacotes de acesso com. Para ver todos os recursos que podem ser adicionados ao catálogo, marque a caixa de seleção **ver tudo** na parte superior do painel Selecionar. Se você quiser selecionar apenas os recursos que estão atualmente no catálogo selecionado, deixe a caixa de seleção **ver tudo** desmarcado (estado padrão).

1. Depois de selecionar os recursos, na lista **função** , selecione a função que você deseja que os usuários sejam atribuídos para o recurso.

    ![Pacote de acesso-seleção de função de recurso](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Próximo**.

>[!NOTE]
>Você pode adicionar grupos dinâmicos a um catálogo e a um pacote do Access. No entanto, você poderá selecionar apenas a função de proprietário ao gerenciar um recurso de grupo dinâmico em um pacote de acesso.

## <a name="requests"></a>Requests

Na guia **solicitações** , você cria a primeira política para especificar quem pode solicitar o pacote de acesso e também as configurações de aprovação. Posteriormente, você pode criar mais políticas de solicitação para permitir que grupos de usuários adicionais solicitem o pacote de acesso com suas próprias configurações de aprovação.

![Pacote de acesso – guia solicitações](./media/entitlement-management-access-package-create/requests.png)

Dependendo de quem você deseja poder solicitar esse pacote de acesso, execute as etapas em uma das seções a seguir.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Examinar + criar

Na guia **revisar + criar** , você pode examinar as configurações e verificar se há erros de validação.

1. Examinar as configurações do pacote de acesso

    ![Pacote de acesso – Habilitar configuração de política](./media/entitlement-management-access-package-create/review-create.png)

1. Clique em **criar** para criar o pacote de acesso.

    O novo pacote de acesso aparece na lista de pacotes de acesso.

## <a name="creating-an-access-package-programmatically"></a>Criando um pacote de acesso programaticamente

Você também pode criar um pacote de acesso usando Microsoft Graph.  Um usuário em uma função apropriada com um aplicativo que tem a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para

1. [Liste o accessPackageResources no catálogo](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) e [crie um accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) para todos os recursos que ainda não estão no catálogo.
1. [Liste o accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) de cada accessPackageResource em um accessPackageCatalog. Essa lista de funções será usada para selecionar uma função, ao criar subsequentemente um accessPackageResourceRoleScope.
1. [Crie um accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Crie um accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Crie um accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) para cada função de recurso necessária no pacote de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md)
- [Alterar funções de recurso para um pacote de acesso](entitlement-management-access-package-resources.md)
- [Atribuir diretamente um usuário ao pacote do Access](entitlement-management-access-package-assignments.md)
- [Criar uma revisão de acesso para um pacote de acesso](entitlement-management-access-reviews-create.md)
