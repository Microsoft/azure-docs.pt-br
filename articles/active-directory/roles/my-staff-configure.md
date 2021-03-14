---
title: Usar minha equipe para delegar o gerenciamento de usuários-Azure AD | Microsoft Docs
description: Delegar o gerenciamento de usuários usando minha equipe e unidades administrativas
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224989"
---
# <a name="manage-your-users-with-my-staff"></a>Gerenciar seus usuários com minha equipe

Minha equipe permite que você delegue permissões para uma figura de autoridade, como um gerente de loja ou um líder de equipe, para garantir que os membros da equipe possam acessar suas contas do Azure AD. Em vez de depender de um helpdesk central, as organizações podem delegar tarefas comuns, como redefinição de senhas ou alteração de números de telefone para um gerente de equipe local. Com a minha equipe, um usuário que não consegue acessar sua conta pode reobter o acesso em apenas alguns cliques, sem necessidade de assistência técnica ou equipe de ti.

Antes de configurar minha equipe para sua organização, recomendamos que você examine esta documentação, bem como a [documentação do usuário](../user-help/my-staff-team-manager.md) , para garantir que você entenda como ela funciona e como ela afeta os usuários. Você pode aproveitar a documentação do usuário para treinar e preparar seus usuários para a nova experiência e ajudar a garantir uma distribuição bem-sucedida.

## <a name="how-my-staff-works"></a>Como minha equipe funciona

Minha equipe é baseada em unidades administrativas, que são um contêiner de recursos que podem ser usados para restringir o escopo do controle administrativo de uma atribuição de função. Para obter mais informações, consulte [Gerenciamento de unidades administrativas em Azure Active Directory](administrative-units.md). Na minha equipe, as unidades administrativas podem ser usadas para conter um grupo de usuários em uma loja ou departamento. Um gerente de equipe pode ser atribuído a uma função administrativa em um escopo de uma ou mais unidades.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.

  * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado à sua assinatura.

  * Se necessário, [crie um locatário do Azure Active Directory](../fundamentals/sign-up-organization.md) ou [associe uma assinatura do Azure à sua conta](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar a autenticação baseada em SMS.
* Cada usuário habilitado na política de método de autenticação de mensagem de texto deve ser licenciado, mesmo que não o use. Cada usuário habilitado deve ter uma das seguintes licenças do Azure AD ou do Microsoft 365:

  * [Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 ou F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 ou E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) ou [Microsoft 365 (M365) E3 ou E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Como habilitar minha equipe

Depois de configurar as unidades administrativas, você poderá aplicar esse escopo aos usuários que acessam a minha equipe. Somente os usuários que recebem uma função administrativa podem acessar minha equipe. Para habilitar minha equipe, conclua as seguintes etapas:

1. Entre no portal do Azure como um administrador de usuário.
2. Navegue até **Azure Active Directory**  >  **configurações do usuário**  >  **recursos do usuário visualizações**  >  **gerenciar configurações de recurso do usuário**.
3. Em **administradores podem acessar minha equipe**, você pode optar por habilitar para todos os usuários, usuários selecionados ou sem acesso de usuário.

> [!Note]
> Somente os usuários que receberam uma função de administrador podem acessar minha equipe. Se você habilitar minha equipe para um usuário que não recebe uma função de administrador, ela não poderá acessar a minha equipe.

## <a name="conditional-access"></a>Acesso condicional

Você pode proteger o portal minha equipe usando a política de acesso condicional do Azure AD. Use-o para tarefas como exigir a autenticação multifator antes de acessar a minha equipe.

É altamente recomendável que você proteja minha equipe usando [as políticas de acesso condicional do Azure ad](../conditional-access/index.yml). Para aplicar uma política de acesso condicional à minha equipe, primeiro você deve visitar o site minha equipe uma vez por alguns minutos para provisionar automaticamente a entidade de serviço em seu locatário para uso pelo acesso condicional.

Você verá a entidade de serviço ao criar uma política de acesso condicional que se aplica ao aplicativo de nuvem minha equipe.

![Criar uma política de acesso condicional para o aplicativo minha equipe](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Usando minha equipe

Quando um usuário vai para a minha equipe, ele mostra os nomes das [unidades administrativas](administrative-units.md) nas quais eles têm permissões administrativas. Na [documentação do usuário da minha equipe](../user-help/my-staff-team-manager.md), usamos o termo "local" para fazer referência a unidades administrativas. Se as permissões de um administrador não tiverem um escopo de unidade administrativa, as permissões se aplicarão em toda a organização. Após a habilitação da minha equipe, os usuários que estão habilitados e foram atribuídos a uma função administrativa podem acessá-la por meio do [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Eles podem selecionar uma unidade administrativa para exibir os usuários nessa unidade e selecionar um usuário para abrir seu perfil.

## <a name="reset-a-users-password"></a>Redefinir a senha de um usuário

Antes de poder colocar senhas para usuários locais, você deve atender às seguintes condições de pré-requisito. Para obter instruções detalhadas, consulte Habilitar o tutorial [de redefinição de senha de autoatendimento](../authentication/tutorial-enable-sspr-writeback.md) .

* Configurar permissões para Write-back de senha
* Habilitar write-back de senha no Azure AD Connect
* Habilitar o Write-back de senha na redefinição de senha de autoatendimento (SSPR) do Azure AD

As seguintes funções têm permissão para redefinir a senha de um usuário:

* [Administrador de autenticação](permissions-reference.md#authentication-administrator)
* [Administrador de autenticação privilegiada](permissions-reference.md#privileged-authentication-administrator)
* [Administrador global](permissions-reference.md#global-administrator)
* [Administrador de assistência técnica](permissions-reference.md#helpdesk-administrator)
* [Administrador de usuários](permissions-reference.md#user-administrator)
* [Administrador de senhas](permissions-reference.md#password-administrator)

Na **minha equipe**, abra o perfil de um usuário. Selecione **Redefinir senha**.

* Se o usuário for somente na nuvem, você poderá ver uma senha temporária que pode ser dada ao usuário.
* Se o usuário for sincronizado no local Active Directory, você poderá inserir uma senha que atenda às suas políticas do AD local. Em seguida, você pode fornecer essa senha ao usuário.

    ![Indicador de progresso de redefinição de senha e notificação de êxito](./media/my-staff-configure/reset-password.png)

O usuário precisa alterar sua senha na próxima vez que entrar.

## <a name="manage-a-phone-number"></a>Gerenciar um número de telefone

Na **minha equipe**, abra o perfil de um usuário.

* Selecione a seção **Adicionar número de telefone** para adicionar um número de telefone para o usuário
* Selecione **Editar número de telefone** para alterar o número de telefone
* Selecione **Remover número de telefone** para remover o número de telefone do usuário

Dependendo de suas configurações, o usuário pode usar o número de telefone que você configurou para entrar com o SMS, executar a autenticação multifator e executar a redefinição de senha de autoatendimento.

Para gerenciar o número de telefone de um usuário, você deve receber uma das seguintes funções:

* [Administrador de autenticação](permissions-reference.md#authentication-administrator)
* [Administrador de autenticação privilegiada](permissions-reference.md#privileged-authentication-administrator)
* [Administrador global](permissions-reference.md#global-administrator)

## <a name="search"></a>Search

Você pode pesquisar por unidades administrativas e usuários em sua organização usando a barra de pesquisa na minha equipe. Você pode pesquisar em todas as unidades administrativas e usuários em sua organização, mas só pode fazer alterações aos usuários que estão em uma unidade administrativa sobre a qual você recebeu permissões de administrador.

Você também pode pesquisar um usuário em uma unidade administrativa. Para fazer isso, use a barra de pesquisa na parte superior da lista de usuários.

## <a name="audit-logs"></a>Logs de auditoria

Você pode exibir os logs de auditoria para ações executadas em minha equipe no portal de Azure Active Directory. Se um log de auditoria foi gerado por uma ação realizada em minha equipe, você verá isso indicado em detalhes adicionais no evento de auditoria.

## <a name="next-steps"></a>Próximas etapas

Documentação do usuário [da minha equipe](../user-help/my-staff-team-manager.md) 
 [Documentação de unidades administrativas](administrative-units.md)
