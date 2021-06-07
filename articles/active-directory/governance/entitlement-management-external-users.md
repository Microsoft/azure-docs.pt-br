---
title: Controlar o acesso para usuários externos no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba mais sobre as configurações que você pode especificar para controlar o acesso para usuários externos no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c78dcbc34deca769739f82964df41ebfc596ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176783"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Controlar o acesso para usuários externos no gerenciamento de direitos do Azure AD

O gerenciamento de direitos do Azure AD utiliza o [B2B (Business-to-Business) do Azure ad](../external-identities/what-is-b2b.md) para colaborar com pessoas de fora da sua organização em outro diretório. Com o Azure AD B2B, os usuários externos fazem a autenticação em seu diretório base, mas têm uma representação em seu diretório. A representação em seu diretório permite que o usuário receba acesso aos seus recursos.

Este artigo descreve as configurações que você pode especificar para controlar o acesso para usuários externos.

## <a name="how-entitlement-management-can-help"></a>Como o gerenciamento de direitos pode ajudar

Ao usar a experiência de convite [B2B do Azure ad](../external-identities/what-is-b2b.md) , você já deve conhecer os endereços de email dos usuários convidados externos que deseja colocar em seu diretório de recursos e trabalhar com o. Isso funciona muito bem quando você está trabalhando em um projeto de menor ou curto prazo e já conhece todos os participantes, mas isso é mais difícil de gerenciar se você tiver muitos usuários com os quais deseja trabalhar ou se os participantes mudarem ao longo do tempo.  Por exemplo, você pode estar trabalhando com outra organização e ter um ponto de contato com essa organização, mas com o passar do tempo, usuários adicionais dessa organização também precisarão de acesso.

Com o gerenciamento de direitos, você pode definir uma política que permite aos usuários de organizações que você especificar para poder solicitar automaticamente um pacote de acesso. Você pode especificar se a aprovação é necessária e uma data de validade para o acesso. Se a aprovação for necessária, você também poderá convidar um ou mais usuários da organização externa para seu diretório e designá-los como aprovadores – já que é provável que eles saibam quais usuários externos de sua organização precisam de acesso. Depois de configurar o pacote de acesso, você pode enviar o link do pacote de acesso para sua pessoa de contato (patrocinador) na organização externa. Esse contato pode compartilhar com outros usuários na organização externa e pode usar esse link para solicitar o pacote de acesso. Os usuários dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando uma solicitação é aprovada, o gerenciamento de direitos provisionará o usuário com o acesso necessário, que pode incluir o convite do usuário se ele ainda não estiver em seu diretório. O Azure AD criará automaticamente uma conta de convidado B2B para eles. Observe que um administrador pode ter limitado anteriormente quais organizações têm permissão para colaboração, definindo uma [lista de permissão ou negação B2B](../external-identities/allow-deny-list.md) para permitir ou bloquear convites para outras organizações.  Se o usuário não for permitido pela lista de permissões ou bloqueios, eles não serão convidados.

Como você não deseja que o acesso do usuário externo seja feito por último, especifique uma data de validade na política, como 180 dias. Após 180 dias, se o acesso não for estendido, o gerenciamento de direitos removerá todo o acesso associado a esse pacote de acesso. Por padrão, se o usuário que foi convidado por meio do gerenciamento de direitos não tiver outras atribuições de pacote de acesso, quando perderem sua última atribuição, sua conta de convidado será impedida de entrar por 30 dias e, subsequentemente, removida. Isso impede a proliferação de contas desnecessárias. Conforme descrito nas seções a seguir, essas configurações são configuráveis.

## <a name="how-access-works-for-external-users"></a>Como o Access funciona para usuários externos

O diagrama e as etapas a seguir fornecem uma visão geral de como os usuários externos recebem acesso a um pacote do Access.

![Diagrama mostrando o ciclo de vida de usuários externos](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Você [adiciona uma organização conectada](entitlement-management-organization.md) para o diretório ou domínio do Azure AD com o qual você deseja colaborar.

1. Você cria um pacote do Access em seu diretório que inclui uma política [para os usuários que não estão em seu diretório](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Você envia um [link meu portal de acesso](entitlement-management-access-package-settings.md) para seu contato na organização externa que eles podem compartilhar com seus usuários para solicitar o pacote de acesso.

1. Um usuário externo (**solicitante a** neste exemplo) usa o link meu portal de acesso para [solicitar acesso](entitlement-management-request-access.md) ao pacote de acesso. A forma como o usuário entra depende do tipo de autenticação do diretório ou do domínio definido na organização conectada.

1. Um Aprovador [aprova a solicitação](entitlement-management-request-approve.md) (ou a solicitação é aprovada automaticamente).

1. A solicitação entra no [estado entrega](entitlement-management-process.md).

1. Usando o processo de convite B2B, uma conta de usuário convidado é criada em seu diretório (**solicitante a (convidado)** neste exemplo). Se uma [lista de permissões ou uma lista de negações](../external-identities/allow-deny-list.md) for definida, a configuração da lista será aplicada.

1. O usuário convidado recebe acesso a todos os recursos no pacote de acesso. Pode levar algum tempo para que as alterações sejam feitas no Azure AD e em outros serviços online da Microsoft ou aplicativos SaaS conectados. Para obter mais informações, consulte [quando as alterações são aplicadas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. O usuário externo recebe um email indicando que o acesso foi [entregue](entitlement-management-process.md).

1. Para acessar os recursos, o usuário externo pode clicar no link no email ou tentar acessar qualquer um dos recursos de diretório diretamente para concluir o processo de convite.

1. Dependendo das configurações de política, à medida que o tempo passa, a atribuição de pacote de acesso para o usuário externo expira e o acesso do usuário externo é removido.

1. Dependendo do ciclo de vida de configurações de usuários externos, quando o usuário externo não tiver mais nenhuma atribuição de pacote de acesso, o usuário externo será impedido de entrar e a conta de usuário convidado será removida do seu diretório.

## <a name="settings-for-external-users"></a>Configurações para usuários externos

Para garantir que as pessoas fora de sua organização possam solicitar pacotes de acesso e obter acesso aos recursos nesses pacotes de acesso, há algumas configurações que você deve verificar que devem ser configuradas corretamente.

### <a name="enable-catalog-for-external-users"></a>Habilitar catálogo para usuários externos

- Por padrão, quando você cria um [novo catálogo](entitlement-management-catalog-create.md), ele é habilitado para permitir que usuários externos solicitem pacotes de acesso no catálogo. Certifique-se **de que habilitado para usuários externos** esteja definido como **Sim**.

    ![Editar configurações do catálogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configurar suas configurações de colaboração externa do Azure AD B2B

- Permitir que os convidados convidem outros convidados para seu diretório significa que os convites convidados podem ocorrer fora do gerenciamento de direitos. Recomendamos que a configuração de **convidados possa convidar** para **não** permitir apenas convites devidamente controlados.
- Se você estiver usando a lista de permissões B2B, deverá ter certeza de que qualquer domínio ao qual você deseja usar o gerenciamento de direitos é adicionado à lista. Como alternativa, se você estiver usando a lista de negações de B2B, deverá certificar-se de que qualquer domínio com o qual você deseja fazer o parceiro não seja adicionado à lista.
- Se você criar uma política de gerenciamento de direitos para **todos os usuários** (todas as organizações conectadas + quaisquer novos usuários externos) e um usuário não pertencer a uma organização conectada em seu diretório, uma organização conectada será criada automaticamente para eles quando solicitarem o pacote. Qualquer configuração de lista de permissões ou de negação B2B terá precedência. Portanto, certifique-se de incluir os domínios que você pretende incluir nessa política na sua lista de permissões se você estiver usando um, e exclua-os da sua lista de negações se estiver usando uma lista de negações.
- Se você quiser criar uma política de gerenciamento de direitos que inclua **todos os usuários** (todas as organizações conectadas + quaisquer novos usuários externos), primeiro você deve habilitar a autenticação de senha de uso único de email para seu diretório. Para obter mais informações, consulte [email de autenticação de senha de uso único](../external-identities/one-time-passcode.md).
- Para obter mais informações sobre as configurações de colaboração externa B2B do Azure AD, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](../external-identities/delegate-invitations.md).

    ![Configurações de colaboração externa do Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Examinar suas políticas de acesso condicional

- Certifique-se de excluir convidados de quaisquer políticas de acesso condicional que novos usuários convidados não poderão atender, pois isso os bloqueará de poder entrar no seu diretório. Por exemplo, os convidados provavelmente não têm um dispositivo registrado, não estão em um local conhecido e não querem se registrar novamente para a MFA (autenticação multifator), portanto, a adição desses requisitos em uma política de acesso condicional bloqueará o uso do gerenciamento de direitos. Para obter mais informações, consulte [o que são condições em Azure Active Directory acesso condicional?](../conditional-access/concept-conditional-access-conditions.md).

    ![Configurações de exclusão de política de acesso condicional do Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Examinar suas configurações de compartilhamento externo do SharePoint Online

- Se você quiser incluir sites do SharePoint Online em seus pacotes de acesso para usuários externos, verifique se a configuração de compartilhamento externo de nível de organização está definida como **qualquer pessoa** (os usuários não precisam entrar) ou **convidados novos e existentes** (convidados devem entrar ou fornecer um código de verificação). Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Se você quiser restringir qualquer compartilhamento externo fora do gerenciamento de direitos, poderá definir a configuração de compartilhamento externo como **convidados existentes**. Em seguida, somente os novos usuários convidados por meio do gerenciamento de direitos poderão obter acesso a esses sites. Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Verifique se as configurações de nível de site habilitam o acesso de convidado (as mesmas seleções de opção conforme listado anteriormente). Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo para um site](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Examine as configurações de compartilhamento de grupo de Microsoft 365

- Se você quiser incluir grupos de Microsoft 365 em seus pacotes de acesso para usuários externos, verifique se a permissão **permitir que os usuários adicionem novos convidados à organização** está definida como **ativada** para permitir o acesso de convidado. Para obter mais informações, consulte [gerenciar o acesso de convidado a grupos de Microsoft 365](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Se você quiser que usuários externos possam acessar o site do SharePoint Online e os recursos associados a um grupo de Microsoft 365, certifique-se de ativar o compartilhamento externo do SharePoint Online. Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Para obter informações sobre como definir a política de convidado para grupos de Microsoft 365 no nível de diretório no PowerShell, consulte [exemplo: configurar a política de convidado para grupos no nível do diretório](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Examine as configurações de compartilhamento de suas equipes

- Se você quiser incluir equipes em seus pacotes de acesso para usuários externos, verifique se a **permissão permitir acesso de convidado no Microsoft Teams** está definida como **ativado** para permitir o acesso de convidado. Para obter mais informações, consulte [Configurar o acesso de convidado no centro de administração do Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Gerenciar o ciclo de vida de usuários externos

Você pode selecionar o que acontece quando um usuário externo, que foi convidado para seu diretório por meio de uma solicitação de pacote de acesso que está sendo aprovada, não tiver mais nenhuma atribuição de pacote de acesso. Isso pode acontecer se o usuário ceder todas as suas atribuições de pacote de acesso ou sua última atribuição de pacote de acesso expirar. Por padrão, quando um usuário externo não tem mais nenhuma atribuição de pacote de acesso, ele é impedido de entrar no seu diretório. Após 30 dias, a conta de usuário convidado dele é removida do seu diretório.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

    ![Configurações para gerenciar o ciclo de vida de usuários externos](./media/entitlement-management-external-users/settings-external-users.png)

1. Na seção **gerenciar o ciclo de vida de usuários externos** , selecione as configurações diferentes para usuários externos.

1. Depois que um usuário externo perder sua última atribuição para qualquer pacote de acesso, se você quiser bloqueá-las de entrar nesse diretório, defina o **bloco usuário externo de entrar nesse diretório** como **Sim**.

    > [!NOTE]
    > Se um usuário estiver impedido de entrar nesse diretório, o usuário não poderá solicitar novamente o pacote de acesso ou solicitar acesso adicional nesse diretório. Não configure o bloqueio da entrada se eles posteriormente precisarem solicitar acesso a outros pacotes de acesso.

1. Depois que um usuário externo perder sua última atribuição para qualquer pacote de acesso, se você quiser remover sua conta de usuário convidado nesse diretório, defina **remover usuário externo** como **Sim**.

    > [!NOTE]
    > O gerenciamento de direitos remove apenas as contas que foram convidadas por meio do gerenciamento de direitos. Além disso, observe que um usuário será impedido de entrar e removido desse diretório, mesmo que ele tenha sido adicionado a recursos desse diretório que não eram atribuições de pacote de acesso. Se o convidado estava no diretório antes de receber atribuições de pacote de acesso, ele permanecerá. Porém, se o convidado foi convidado por meio de uma atribuição de pacote de acesso e, depois de ser convidado também foi atribuído a um site do OneDrive for Business ou do SharePoint Online, ele ainda será removido.

1. Se você quiser remover a conta de usuário convidado nesse diretório, poderá definir o número de dias antes de ela ser removida. Se você quiser remover a conta de usuário convidado assim que o usuário perder a última atribuição a qualquer pacote de acesso, defina **Número de dias antes de remover o usuário externo desse diretório** como **0**.

1. Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma organização conectada](entitlement-management-organization.md)
- [Para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Solucionar problemas](entitlement-management-troubleshoot.md)
