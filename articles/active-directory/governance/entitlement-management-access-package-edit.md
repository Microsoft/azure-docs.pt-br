---
title: Editar e gerenciar um pacote existente de acesso no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como editar e gerenciar um pacote existente de acesso no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c1717249abac30847f96073ee6daf7d98112d7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190348"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Editar e gerenciar um pacote existente de acesso no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um pacote de acesso permite que você faça uma configuração única de recursos e as políticas que administra automaticamente acesso durante a vida útil do pacote de acesso. Como um Gerenciador de pacotes de acesso, você pode alterar os recursos em um pacote de acesso a qualquer momento sem se preocupar sobre o acesso do usuário para os novos recursos de provisionamento, ou remover o acesso dos recursos anteriores. As políticas também podem ser atualizadas a qualquer momento, no entanto, as alterações de política afetam apenas os acessos de novo.

Este artigo descreve como editar e gerenciar os pacotes existentes do access.

## <a name="add-resource-roles"></a>Adicionar funções de recurso

Uma função de recurso é uma coleção de permissões associadas a um recurso. A maneira que você disponibiliza os recursos para que os usuários solicitem é com a adição de funções de recurso ao seu pacote de acesso. Você pode adicionar funções de recurso para grupos, aplicativos e sites do SharePoint.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Clique em **adicionar funções de recurso** para abrir as adicionar funções de recurso para acessar a página do pacote.

    ![Acessar o pacote - adicionar funções de recurso](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Dependendo se você deseja adicionar um grupo, aplicativo ou site do SharePoint, execute as etapas em uma das seguintes seções de função de recurso.

### <a name="add-a-group-resource-role"></a>Adicionar uma função do grupo de recursos

Você pode fazer com que o gerenciamento de direitos de adicionar usuários automaticamente a um grupo quando eles forem atribuídos a um pacote de acesso. 

- Quando um grupo faz parte de um pacote de acesso e um usuário é atribuído a esse pacote de acesso, o usuário é adicionado a esse grupo, se ainda não existir.
- Quando a atribuição do pacote de acesso do usuário expira, eles serão removidos do grupo, a menos que tenham atualmente uma atribuição para outro pacote de acesso que inclui o mesmo grupo.

Você pode selecionar qualquer grupo do Office 365 ou o grupo de segurança do Azure AD.  Os administradores podem adicionar qualquer grupo a um catálogo; proprietários de catálogo podem adicionar qualquer grupo ao catálogo de se eles forem o proprietário do grupo. Lembre-se as seguintes restrições do Azure AD ao selecionar um grupo:

- Quando um usuário, incluindo um convidado é adicionado como um membro a um grupo, eles podem ver todos os outros membros desse grupo.
- Azure AD não pode alterar a associação de um grupo que foi sincronizado do Active Directory do Windows Server usando o Azure AD Connect.  
- A associação de grupos dinâmicos não pode ser atualizada, adicionando ou removendo um membro, portanto, associações de grupos dinâmicos não são adequadas para uso com o gerenciamento de direitos.

1. No **adicionar funções de recurso para acessar o pacote** , clique em **grupos** para abrir o painel de grupos selecionados.

1. Selecione os grupos que você deseja incluir no pacote de acesso.

    ![Acessar o pacote - adicionar funções de recurso - Selecione grupos](./media/entitlement-management-access-package-edit/group-select.png)

1. Clique em **Selecionar**.

1. No **função** lista, selecione **proprietário** ou **membro**.

    Normalmente, você seleciona a função de membro. Se você selecionar a função de proprietário, o que permitirá que os usuários adicionar ou remover outros membros ou proprietários.

    ![Acessar o pacote - adicionar a função de recurso para um grupo](./media/entitlement-management-access-package-edit/group-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com as atribuições existentes para o pacote de acesso tornará os membros desse grupo automaticamente quando ele é adicionado.

### <a name="add-an-application-resource-role"></a>Adicionar uma função de recurso do aplicativo

Você pode fazer com que o Azure AD automaticamente atribuir aos usuários acesso a um aplicativo do Azure AD empresarial, incluindo aplicativos SaaS e aplicativos da sua organização federados ao AD do Azure, quando um usuário é atribuído a um pacote de acesso. Para aplicativos que se integram ao Azure AD por meio de logon único federado, o Azure AD emitirá tokens de federação para que os usuários atribuídos ao aplicativo.

Aplicativos podem ter várias funções. Ao adicionar um aplicativo a um pacote de acesso, se esse aplicativo tiver mais de uma função, você precisará especificar a função apropriada para esses usuários.  Se você estiver desenvolvendo aplicativos, você pode ler mais sobre como essas funções são fornecidas para seus aplicativos no artigo sobre como [configurar a declaração de função emitida no token SAML](../develop/active-directory-enterprise-app-role-management.md).

Depois que uma função de aplicativo é parte de um pacote de acesso:

- Quando um usuário é atribuído a esse pacote de acesso, o usuário é adicionado à função de aplicativo, se não estiver presente.
- Quando a atribuição do pacote de acesso do usuário expira, seu acesso será removido do aplicativo, a menos que tenham uma atribuição para outro pacote de acesso que inclui a função de aplicativo.

Aqui estão algumas considerações ao selecionar um aplicativo:

- Aplicativos também podem ter grupos atribuídos às suas funções também.  Você pode optar por adicionar um grupo no lugar de uma função de aplicativo em um pacote de acesso, no entanto, em seguida, o aplicativo não será visível para o usuário como parte do pacote de acesso no portal do meu acesso.

1. Sobre o **adicionar funções de recurso para acessar o pacote** , clique em **aplicativos** para abrir o painel selecionar aplicativos.

1. Selecione os aplicativos que você deseja incluir no pacote de acesso.

    ![Acessar o pacote - adicionar funções de recurso - selecionar aplicativos](./media/entitlement-management-access-package-edit/application-select.png)

1. Clique em **Selecionar**.

1. No **função** , selecione uma função de aplicativo.

    ![Acessar o pacote - adicionar a função de recurso para um aplicativo](./media/entitlement-management-access-package-edit/application-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com as atribuições existentes para o pacote de acesso automaticamente terá acesso a esse aplicativo quando ele é adicionado.

### <a name="add-a-sharepoint-site-resource-role"></a>Adicionar uma função de recurso de site do SharePoint

Azure AD pode atribuir automaticamente os usuários acesso a um site do SharePoint Online ou o conjunto de sites do SharePoint Online quando eles forem atribuídos a um pacote de acesso.

1. Sobre o **adicionar funções de recurso para acessar o pacote** , clique em **sites do SharePoint** para abrir o painel de sites selecione SharePoint Online.

1. Selecione os sites do SharePoint Online que deseja incluir no pacote de acesso.

    ![Acessar o pacote - adicionar funções de recurso - selecione sites do SharePoint](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. No **função** lista, selecione uma função de site do SharePoint Online.

    ![Acesso de pacote - Adicionar função de recurso para um site do SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com as atribuições existentes para o pacote de acesso automaticamente terá acesso a este site do SharePoint Online quando ele é adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recurso

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Na lista de funções de recurso, localize a função do recurso que deseja remover.

1. Clique no botão de reticências ( **...** ) e, em seguida, clique em **Remover função de recurso**.

    Todos os usuários com as atribuições existentes para o pacote de acesso terão automaticamente seu acesso revogado a essa função de recurso quando ele é removido.

## <a name="add-a-new-policy"></a>Adicionar uma nova política

É a maneira que você especifique quem pode solicitar um pacote de acesso criar uma política. Você pode criar várias políticas para um pacote de acesso único para permitir diferentes conjuntos de usuários a serem concedidos atribuições com aprovação diferente e as configurações de expiração. Uma única política não pode ser usada para atribuir usuários internos e externos para o mesmo pacote de acesso. No entanto, você pode criar duas diretivas no mesmo pacote de acesso – uma para usuários internos e outra para usuários externos. Se houver várias políticas que se aplicam a um usuário, ele serão solicitado no momento da sua solicitação para selecionar a política que gostaria de ser atribuído a.

O diagrama a seguir mostra o processo de alto nível para criar uma política para um pacote existente de acesso.

![Criar um processo de política](./media/entitlement-management-access-package-edit/policy-process.png)

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **diretivas** e, em seguida **adicionar política**.

1. Digite um nome e uma descrição para a política.

    ![Criar política com o nome e descrição](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Com base em sua seleção para **os usuários podem solicitar acesso**, execute as etapas em uma das seguintes seções de política.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Editar uma política existente

Você pode editar uma política a qualquer momento. Se você alterar a data de validade de uma política, a data de validade para solicitações que já estão em uma aprovação pendente ou aprovado estado não será alterado.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que você deseja editar.

    O **detalhes política** painel é aberto na parte inferior da página.

    ![Painel de detalhes do pacote de acesso - política](./media/entitlement-management-access-package-edit/policy-details.png)

1. Clique em **editar** para editar a política.

    ![Pacote de acesso - Editar política](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Quando terminar, clique em **atualização**.

## <a name="directly-assign-a-user"></a>Atribuir diretamente um usuário

Em alguns casos, talvez você queira atribuir diretamente a usuários específicos a um pacote de acesso para que os usuários não precisam passar pelo processo de solicitar o pacote de acesso. Para atribuir diretamente a usuários, o pacote de acesso deve ter uma política que permite que o administrador atribuições diretas.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.

1. Clique em **atribuição de nova** para abrir Adicionar usuário ao pacote de acesso.

    ![Adicionar - atribuições de usuário para acesso de pacote](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Clique em **adicionar usuários** para selecionar os usuários que você deseja atribuir o pacote de acesso.

1. No **Selecionar política** , selecione uma política que tem o [None (atribuições diretas administrador somente)](#policy-none-administrator-direct-assignments-only) configuração.

    Se este pacote de acesso não tiver esse tipo de política, você poderá clicar **criar nova política** para adicionar um.

1. Defina a data e hora que você deseja que a atribuição dos usuários selecionados para começar e terminar. Se uma data de término não for fornecida, as configurações de expiração da política serão usadas.

1. Opcionalmente, forneça uma justificativa para sua atribuição direta para manter um registro.

1. Clique em **adicionar** atribuir diretamente os usuários selecionados para o pacote de acesso.

    Após alguns instantes, clique em **Refresh** para ver os usuários na lista de atribuições.

## <a name="view-who-has-an-assignment"></a>Modo de exibição que tem uma atribuição

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **atribuições** para ver uma lista de atribuições ativas.

1. Clique em uma atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tem todas as funções de recurso provisionadas corretamente, clique no status do filtro e selecione **entregando**.

    Você pode ver detalhes adicionais sobre erros de entrega, localizando a solicitação do usuário correspondente na **solicitações** página.

1. Para ver as atribuições expiradas, clique no status do filtro e selecione **expirado**.

1. Para baixar um arquivo CSV contendo a lista filtrada, clique em **baixar**.

## <a name="view-requests"></a>Exibir solicitações

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique em uma solicitação específica para ver detalhes adicionais.

## <a name="view-a-requests-delivery-errors"></a>Exibir erros de entrega da solicitação

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **solicitações**.

1. Selecione a solicitação que você deseja exibir.

    Se a solicitação tiver quaisquer erros de entrega, o status da solicitação será **Undelivered** e o substatus serão **parcialmente entregue**.

    Se houver quaisquer erros de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique a contagem para ver todos os erros de entrega da solicitação.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Você só pode cancelar uma solicitação pendente que ainda não foram entregues.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação para cancelar

1. No painel de detalhes de solicitação, clique em **solicitação de cancelamento**.

## <a name="copy-my-access-portal-link"></a>Copie o link do portal meu acesso

A maioria dos usuários em seu diretório podem entrar portal do meu acesso e verão automaticamente uma lista de pacotes de acesso, que eles poderão solicitar. No entanto, para usuários de parceiros comerciais externos que ainda não estão em seu diretório, você precisará enviar um link que eles podem usar para solicitar um pacote de acesso. Desde que o pacote de acesso está habilitado para usuários externos e você tem uma política para o diretório do usuário externo, o usuário externo pode usar o link do portal meu acesso para solicitar o acesso de pacote.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Na página Visão geral, copie o **link do portal de acesso Meus**.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

1. Envie um email ou envie o link para seu parceiro de negócios externos. Eles podem compartilhar o link com seus usuários para solicitar o acesso de pacote.

## <a name="change-the-hidden-setting"></a>Alterar a configuração oculto

Pacotes de acesso são detectáveis por padrão. Isso significa que se uma política permite que um usuário solicitar o acesso de pacote, ele verá automaticamente o pacote de acesso listado no seu portal meu acesso.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Na página de visão geral, clique em **editar**.

1. Defina as **Hidden** configuração.

    Se definido como **não**, o pacote de acesso será listado no portal do meu acesso do usuário.

    Se definido como **Sim**, o pacote de acesso não será listado no portal do meu acesso do usuário. A única maneira de um usuário pode exibir o pacote de acesso é se eles têm o direct **link do portal de acesso Meus** para o pacote de acesso.

## <a name="delete"></a>Excluir

Um pacote de acesso só pode ser excluído se ele tiver nenhuma atribuição de usuário do Active Directory.

**Função de pré-requisito:** Usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remover o acesso para todos os usuários.

1. No menu à esquerda, clique em **visão geral** e, em seguida, clique em **excluir**.

1. Na mensagem de exclusão que aparece, clique em **Sim**.

## <a name="when-are-changes-applied"></a>Quando as alterações são aplicadas

No gerenciamento de direitos do Azure AD irá processar alterações de atribuição e recursos em seus pacotes de acesso em massa várias vezes ao dia. Portanto, se você fizer uma atribuição, ou alterar as funções de recurso do seu pacote de acesso, pode levar até 24 horas para que essa alteração ser feita no Azure AD, somado à quantidade de tempo ele leva para propagar essas alterações para outros serviços Online da Microsoft ou aplicativo SaaS conectado s. Se a alteração afeta apenas alguns objetos, a alteração provavelmente só levará alguns minutos para ser aplicada no AD do Azure, após o qual os outros componentes do Azure AD, em seguida, irá detectar essa alteração e atualizar os aplicativos de SaaS. Se a alteração afeta a milhares de objetos, a alteração levará mais tempo. Por exemplo, se você tiver um pacote de acesso com 2 aplicativos e 100 atribuições de usuário, e você optar por adicionar uma função de site do SharePoint para o pacote de acesso, pode haver um atraso até que todos os usuários fazem parte dessa função de site do SharePoint. Você pode monitorar o andamento por meio do log de auditoria do AD do Azure, o log de provisionamento do Azure AD e os logs de auditoria de site do SharePoint.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar um proprietário do catálogo ou um Gerenciador de pacotes de acesso](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Notificações de email e o processo de solicitação](entitlement-management-process.md)
