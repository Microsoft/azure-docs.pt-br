---
title: Permissões de usuário padrão - Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as permissões de usuário diferentes disponíveis no Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724264"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de usuário padrão no Azure Active Directory?
No Azure Active Directory (Azure AD), a todos os usuários é concedido um conjunto de permissões padrão. Um acesso do usuário é composto por tipo de usuário, suas [atribuições de função](active-directory-users-assign-role-azure-portal.md) e sua posse de objetos individuais. Este artigo descreve as permissões padrão e contém uma comparação entre os padrões do usuário membro e convidado. As permissões de usuário padrão podem ser alteradas somente nas configurações do usuário no Azure AD.

## <a name="member-and-guest-users"></a>Usuários membro e convidado
O conjunto de permissões padrão recebido depende de se o usuário é um membro nativo do locatário (usuário membro) ou se o usuário é trazido de outro diretório como um convidado de colaboração B2B (usuário convidado). Veja [O que é colaboração B2B do Azure AD?](../external-identities/what-is-b2b.md) para obter mais informações sobre como adicionar usuários convidados.
* Usuários membro podem registrar aplicativos, gerenciar seu próprio número de celular e foto perfil, alterar sua senha e convidar pessoas B2B. Além disso, os usuários podem ler todas as informações de diretório (com algumas poucas exceções). 
* Usuários convidados têm permissões de diretório restritas. Eles podem gerenciar seu próprio perfil, alterar sua própria senha e recuperar algumas informações sobre outros usuários, grupos e aplicativos, no entanto, eles não podem ler todas as informações de diretório. Por exemplo, os usuários convidados não podem enumerar a lista de todos os usuários, grupos e outros objetos de diretório. Convidados podem ser adicionados a funções de administrador, o que lhes concedem permissões totais de leitura e gravação contidas na função. Os convidados também podem convidar outros convidados.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar permissões padrão de membro e convidado

**Área** | **Permissões de usuário membro** | **Permissões de usuário convidado padrão** | **Permissões de usuário de convidado restrita (visualização)**
------------ | --------- | ---------- | ----------
Usuários e contatos | <ul><li>Enumerar lista de todos os usuários e contatos<li>Ler todas as propriedades públicas de usuários e contatos</li><li>Convidar pessoas<li>Alterar a própria senha<li>Gerenciar o próprio número de celular<li>Gerenciar a própria foto<li>Invalidar os próprios tokens de atualização</li></ul> | <ul><li>Ler as próprias propriedades<li>Ler nome de exibição, email, nome de entrada, foto, nome principal de usuário e propriedades de tipo de usuário de outros usuários e contatos<li>Alterar a própria senha<li>Pesquisar outro usuário por ObjectId (se permitido)<li>Gerenciador de leitura e informações de relatório direto de outros usuários</li></ul> | <ul><li>Ler as próprias propriedades<li>Alterar a própria senha</li></ul>
Grupos | <ul><li>Criar grupos de segurança<li>Criar grupos de Microsoft 365<li>Enumerar lista de todos os grupos<li>Ler todas as propriedades de grupos<li>Ler associações de grupos não ocultos<li>Ler associações de grupo de Microsoft 365 ocultas para o grupo associado<li>Gerenciar propriedades, posse e associação de grupos pertencentes ao usuário<li>Adicionar convidados a grupos próprios<li>Gerenciar configurações de associação dinâmica<li>Excluir grupos próprios<li>Restaurar grupos de Microsoft 365 de propriedade</li></ul> | <ul><li>Ler propriedades de grupos não ocultos, incluindo associação e Propriedade (até mesmo grupos não associados)<li>Ler associações de grupo de Microsoft 365 ocultas para grupos associados<li>Pesquisar grupos por nome de exibição ou ObjectId (se permitido)</li></ul> | <ul><li>Ler ID de objeto para grupos Unidos<li>Ler associação e propriedade de grupos Unidos em alguns Microsoft 365 aplicativos (se permitido)</li></ul>
Aplicativos | <ul><li>Registrar (criar) novo aplicativo<li>Enumerar lista de todos os aplicativos<li>Ler propriedades de aplicativos registrados e corporativos<li>Gerenciar propriedades do aplicativo, atribuições e credenciais para aplicativos próprios<li>Criar ou excluir senha de aplicativo do usuário<li>Excluir aplicativos próprios<li>Restaurar aplicativos próprios</li></ul> | <ul><li>Ler propriedades de aplicativos registrados e corporativos</li></ul> | <ul><li>Ler propriedades de aplicativos registrados e corporativos
Dispositivos</li></ul> | <ul><li>Enumerar lista de todos os dispositivos<li>Ler todas as propriedades de dispositivos<li>Gerenciar todas as propriedades de dispositivos próprios</li></ul> | Nenhuma permissão | Nenhuma permissão
Diretório | <ul><li>Ler todas as informações da empresa<li>Ler todos os domínios<li>Ler todos os contratos de parceiro</li></ul> | <ul><li>Ler nome de exibição da empresa<li>Ler todos os domínios</li></ul> | <ul><li>Ler nome de exibição da empresa<li>Ler todos os domínios</li></ul>
Funções e escopos | <ul><li>Ler todas as funções e associações administrativas<li>Ler todas as propriedades e associação de unidades administrativas</li></ul> | Nenhuma permissão | Nenhuma permissão
Assinaturas | <ul><li>Ler todas as assinaturas<li>Habilitar Membro de plano de serviço</li></ul> | Nenhuma permissão | Nenhuma permissão
Políticas | <ul><li>Ler todas as propriedades de políticas<li>Gerenciar todas as propriedades de política própria</li></ul> | Nenhuma permissão | Nenhuma permissão

## <a name="restrict-member-users-default-permissions"></a>Restringir permissões padrão de usuários Membros 

As permissões padrão para usuários Membros podem ser restritas das seguintes maneiras:

Permissão | Configurar explicação
---------- | ------------
Os usuários podem registrar o aplicativo | Definir essa opção como não impede que os usuários criem registros de aplicativo. A capacidade pode então ser concedida de volta a indivíduos específicos, adicionando-os à função de desenvolvedor de aplicativos.
Permitir que os usuários se conectem à conta corporativa ou de estudante com o LinkedIn | Definir essa opção como não impede que os usuários conectem sua conta corporativa ou de estudante com sua conta do LinkedIn. Para obter mais informações, consulte o [consentimento e compartilhamento de dados de conexões de contas do LinkedIn](../enterprise-users/linkedin-user-consent.md).
Capacidade de criar grupos de segurança | Definir essa opção como Não impede que os usuários criem grupos de segurança. Administradores globais e administradores de usuários ainda podem criar grupos de segurança. Consulte [Cmdlets do Active Directory do Azure para definir as configurações de grupo](../enterprise-users/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos de Microsoft 365 | Definir essa opção como não impede que os usuários criem grupos de Microsoft 365. Definir essa opção como alguns permite que um conjunto selecionado de usuários crie Microsoft 365 grupos. Administradores globais e administradores de usuários ainda poderão criar grupos de Microsoft 365. Consulte [Cmdlets do Active Directory do Azure para definir as configurações de grupo](../enterprise-users/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir essa opção como não permite que os não administradores usem o portal de administração do Azure AD para ler e gerenciar recursos do Azure AD. Sim restringe todos os não-administradores de acessar dados do Azure AD no portal de administração.<p>**Observação**: essa configuração não restringe o acesso aos dados do Azure ad usando o PowerShell ou outros clientes, como o Visual Studio. quando definido como Sim, para conceder a um usuário não administrador específico a capacidade de usar o portal de administração do Azure AD, atribua qualquer função administrativa, como a função leitores de diretório.<p>Essa função permite ler informações básicas do diretório, que os usuários Membros têm por padrão (convidados e entidades de serviço não).
Capacidade de ler outros usuários | Essa configuração só está disponível no PowerShell. Definir esse sinalizador como $false impede que todos os não-administradores leiam informações do usuário do diretório. Esse sinalizador não impede a leitura de informações do usuário em outros serviços da Microsoft, como o Exchange Online. Essa configuração destina-se a circunstâncias especiais e a definição desse sinalizador como $false não é recomendada.

## <a name="restrict-guest-users-default-permissions"></a>Restringir permissões padrão de usuários convidados

As permissões padrão para usuários convidados podem ser restritas das seguintes maneiras:

>[!NOTE]
>A configuração de restrições de acesso do usuário de convidados substituiu as **permissões de usuários convidados são limitadas** . Para obter orientação sobre como usar esse recurso, consulte [restringir permissões de acesso de convidado (versão prévia) no Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Permissão | Configurar explicação
---------- | ------------
Restrições de acesso do usuário de convidados (visualização) | A definição dessa opção para **os usuários convidados têm o mesmo acesso que os membros** concedem a todos os usuários Membros permissões de usuário para convidados por padrão.<p>Definir essa opção como **acesso de usuário convidado é restrito a propriedades e associações de seus próprios objetos de diretório** restringe o acesso de convidado apenas a seu próprio perfil de usuário por padrão. O acesso a outros usuários não é mais permitido mesmo ao pesquisar por nome principal do usuário, ObjectId ou nome de exibição. O acesso a informações de grupos, incluindo associações de grupos, também não é mais permitido.<p>**Observação**: essa configuração não impede o acesso a grupos Unidos em alguns serviços de Microsoft 365, como o Microsoft Teams. Consulte [acesso de convidado do Microsoft Teams](/MicrosoftTeams/guest-access) para saber mais.<p>Os usuários convidados ainda podem ser adicionados às funções de administrador, independentemente dessas configurações de permissão.
Convidados podem convidar | Definir essa opção como Sim permite que os convidados convidem outros convidados. Confira [delegar convites para colaboração B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) para saber mais.
Membros podem convidar | Definir essa opção como Sim permite que membros não administradores do seu diretório convidem convidados. Confira [delegar convites para colaboração B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) para saber mais.
Administradores e usuários na função de emissor do convite convidado podem convidar | Definir essa opção como Sim permite que administradores e usuários na função "emissor do convidado" convidem convidados. Quando definido como Sim, os usuários na função emissor de convite do convidado ainda poderão convidar convidados, independentemente da configuração dos membros podem convidar. Confira [delegar convites para colaboração B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) para saber mais.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registro de aplicativo
Quando um usuário registra um aplicativo, ele é automaticamente adicionado como um proprietário do aplicativo. Como proprietário, ele pode gerenciar os metadados do aplicativo, como o nome e as permissões que o aplicativo solicita. Eles também podem gerenciar a configuração de locatário específico do aplicativo, como configuração de SSO e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Diferentemente dos Administradores globais, proprietários só podem gerenciar os aplicativos que eles possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões do proprietário do aplicativo empresarial
Quando um usuário adiciona um novo aplicativo empresarial, ele é adicionado automaticamente como um proprietário. Como proprietário, eles podem gerenciar a configuração específica do locatário do aplicativo, como a configuração de SSO, o provisionamento e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários podem gerenciar somente os aplicativos que eles possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um usuário cria um grupo, ele é automaticamente adicionado como um proprietário desse grupo. Como proprietário, eles podem gerenciar as propriedades do grupo, como o nome, bem como gerenciar a associação ao grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de usuários, os proprietários só podem gerenciar os grupos que eles possuem. Para atribuir um proprietário do grupo, consulte [Gerenciando proprietários para um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas a seguir descrevem as permissões específicas em Azure Active Directory usuários Membros têm objetos de propriedade. O usuário só tem essas permissões em objetos de sua propriedade.

#### <a name="owned-application-registrations"></a>Registros de aplicativo de propriedade
Os usuários podem executar as seguintes ações em registros de aplicativo de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| Microsoft. Directory/Applications/Restore | Restaure aplicativos no Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Aplicativos empresariais de propriedade
Os usuários podem executar as seguintes ações em aplicativos corporativos de propriedade. Um aplicativo empresarial é composto de entidade de serviço, uma ou mais políticas de aplicativo e, às vezes, um objeto de aplicativo no mesmo locatário que a entidade de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.directory/policies/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivos de propriedade
Os usuários podem executar as seguintes ações em dispositivos de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/devices/disable | Desabilite dispositivos no Azure Active Directory. |

#### <a name="owned-groups"></a>Grupos de propriedade
Os usuários podem executar as seguintes ações em grupos próprios.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| Microsoft. Directory/groups/dynamicMembershipRule/Update | Atualize a propriedade dynamicmembershiprule no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade Groups no Azure Active Directory. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a configuração de restrições de acesso do usuário de convidados, consulte [restringir permissões de acesso de convidado (versão prévia) no Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Para saber mais sobre como atribuir funções de administrador do Microsoft Azure Active Directory consulte [Atribuir um usuário a funções de administrador no Microsoft Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerenciar usuários](add-users-azure-active-directory.md)