---
title: Criar um grupo básico e adicionar membros – Azure Active Directory | Microsoft Docs
description: Instruções sobre como criar um grupo básico usando o Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8041656a11689d0cca64c3edae1af2e70409028
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027706"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Criar um grupo básico e adicionar membros usando o Azure Active Directory
Você pode criar um grupo básico usando o portal do Azure AD (Azure Active Directory). Para os fins deste artigo, um grupo básico é adicionado a um único recurso pelo proprietário do recurso (administrador) e inclui membros específicos (funcionários) que precisam acessar o recurso. Para cenários mais complexos, incluindo associações dinâmicas e criação de regra, confira a [documentação de gerenciamento de usuário do Azure Active Directory](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Tipos de grupo e associação
Há vários tipos de grupo e associação. As informações a seguir explicam cada tipo de grupo e associação e por que eles são usados, para ajudar você a decidir quais opções usar ao criar um grupo.

### <a name="group-types"></a>Tipos de grupo:
- **Segurança**. Usado para gerenciar o acesso de membros e computadores a recursos compartilhados para um grupo de usuários. Por exemplo, você pode criar um grupo de segurança para uma política de segurança específica. Com isso, você pode dar um conjunto de permissões a todos os membros ao mesmo tempo, em vez de precisar adicionar permissões a cada membro individualmente. Um grupo de segurança pode ter usuários, dispositivos, grupos e entidades de serviço como seus membros e pode ter usuários e entidades de serviço como seus proprietários. Para obter mais informações sobre como gerenciar o acesso aos recursos, confira [Gerenciar o acesso a recursos com grupos do Azure Active Directory](active-directory-manage-groups.md).
- **Microsoft 365**. Fornece oportunidades de colaboração, oferecendo acesso de membros a uma caixa de correio compartilhada, calendário, arquivos, site do SharePoint e muito mais. Essa opção também permite dar acesso ao grupo a pessoas fora da sua organização. Um grupo de Microsoft 365 pode ter somente usuários como seus membros. Os usuários e as entidades de serviço podem ser proprietários de um grupo de Microsoft 365. Para obter mais informações sobre grupos de Microsoft 365, consulte [saiba mais sobre grupos de Microsoft 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Tipos de associação:
- **Atribuído.** Permite adicionar usuários específicos para que sejam membros desse grupo e tenham permissões exclusivas. Para os fins deste artigo, estamos usando essa opção.
- **Usuário dinâmico.** Permite usar regras de associação dinâmica para adicionar e remover membros automaticamente. Se os atributos de um membro mudarem, o sistema examinará suas regras de grupo dinâmico para que o diretório verifique se o membro atende aos requisitos da regra (é adicionado) ou não mais atende aos requisitos de regras (é removido).
- **Dispositivo dinâmico.** Permite o uso de regras de grupo dinâmico para adicionar e remover dispositivos automaticamente. Se os atributos de um dispositivo mudarem, o sistema examinará suas regras de grupo dinâmico para que o diretório verifique se o dispositivo atende aos requisitos da regra (é adicionado) ou não mais atende aos requisitos de regras (é removido).

    > [!IMPORTANT]
    > Você pode criar um grupo dinâmico para dispositivos ou usuários, mas não para ambos. Você também não pode criar um grupo de dispositivos com base em atributos dos proprietários do dispositivo. Regras de associação de dispositivo só podem fazer referência a atributos do dispositivo. Para obter mais informações sobre como criar um grupo dinâmico para usuários e dispositivos, confira [Criar um grupo dinâmico e verificar o status](../enterprise-users/groups-create-rule.md)

## <a name="create-a-basic-group-and-add-members"></a>Criar um grupo básico e adicionar membros
Você pode criar um grupo básico e adicionar seus membros ao mesmo tempo. Para criar um grupo básico e adicionar membros, use o seguinte procedimento:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

1. Pesquise **Azure Active Directory** e selecione-o.

1. Na página **Active Directory**, selecione **Grupos** e selecione **Novo grupo**.

    ![Página do Azure AD, com Grupos exibidos](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. O painel **Novo Grupo** será exibido e você deverá preencher as informações necessárias.

    ![Nova página de grupo, preenchida com informações de exemplo](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Selecione um **Tipo de grupo** predefinido. Para obter mais informações sobre tipos de grupo, confira [Tipos de grupo e associação](#group-types).

1. Crie e adicione um **Nome de grupo.** Escolha um nome que você lembrará e que faça sentido para o grupo. Uma verificação será executada para determinar se o nome já está em uso por outro grupo. Se o nome já estiver em uso, para evitar nomes duplicados, você deverá modificar o nome do grupo.

1. Adicione um **Endereço de email de grupo** para o grupo ou mantenha o endereço de email que é preenchido automaticamente.

1. **Descrição do grupo.** Adicione uma descrição opcional para seu grupo.

1. Selecione um **Tipo de associação (obrigatório)** predefinido. Para obter mais informações sobre tipos de associação, confira [Tipos de grupo e associação](#membership-types).

1. Selecione **Criar**. O grupo é criado e está pronto para que você adicione membros.

1. Selecione a área **Membros** da página **Grupo** e, em seguida, comece a procurar os membros a serem adicionados ao seu grupo da página **Selecionar membros**.

    ![Selecionando membros para o seu grupo durante o processo de criação de grupo](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Quando você terminar de adicionar membros, escolha **Selecionar**.

    A página **Visão geral do grupo** é atualizada para mostrar o número de membros que agora são adicionados ao grupo.

    ![Página de visão geral de grupo com o número de membros realçado](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Ativar ou desativar o email de boas-vindas do grupo

Quando qualquer novo grupo de Microsoft 365 é criado, seja com associação dinâmica ou estática, uma notificação de boas-vindas é enviada a todos os usuários que são adicionados ao grupo. Quando um atributo de um usuário ou dispositivo é alterado, todas as regras de grupos dinâmicos na organização são processadas para possíveis alterações de associação. Os usuários que são adicionados também recebem a notificação de boas-vindas. É possível desativar esse comportamento no [PowerShell do Exchange](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup). 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar acesso a aplicativos SaaS usando grupos](../enterprise-users/groups-saasapps.md)
- [Gerenciar grupos usando comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
