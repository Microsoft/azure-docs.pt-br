---
title: Visão geral das contas de usuários no Azure Active Directory B2C
description: Saiba mais sobre os tipos de contas de usuário que podem ser usados no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3e6c1d6bfa83ef238ef38b25b189510cf142a38
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661078"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Visão geral das contas de usuários no Azure Active Directory B2C

No Azure Active Directory B2C (Azure AD B2C), há vários tipos de contas que podem ser criadas. Azure Active Directory, Active Directory B2B e Active Directory B2C compartilhamento nos tipos de contas de usuário que podem ser usados.

Os seguintes tipos de contas estão disponíveis:

- **Conta corporativa** - Uma conta corporativa pode acessar recursos em um locatário e, com uma função de administrador, pode gerenciar locatários.
- **Conta Convidado** - Uma conta convidado pode ser apenas uma conta Microsoft ou um usuário do Azure Active Directory que pode ser usado para acessar aplicativos ou gerenciar locatários.
- **Conta de consumidor** -uma conta de consumidor é usada por um usuário dos aplicativos que você registrou com Azure ad B2C. As contas de consumidor podem ser criadas por:
  - O usuário que está passando por um fluxo de usuário de inscrição em um aplicativo Azure AD B2C
  - Usando a API Microsoft Graph
  - Usando o portal do Azure

## <a name="work-account"></a>Conta corporativa

Uma conta corporativa é criada da mesma maneira para todos os locatários com base no Azure AD. Para criar uma conta corporativa, você pode usar as informações em [Início rápido: Adicionar novos usuários ao Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Uma conta corporativa é criada usando a opção **Novo usuário** no portal do Azure.

Ao adicionar uma nova conta corporativa, será necessário considerar as seguintes configurações:

- **Nome** e **Nome de usuário** - A propriedade **Nome** contém o nome e sobrenome do usuário. O **Nome de usuário** é o identificador que o usuário insere para entrar. O nome de usuário inclui o domínio completo. A parte do nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial *seu-domínio.onmicrosoft.com* ou um nome de domínio personalizado [não federado verificado,](../active-directory/fundamentals/add-custom-domain.md) como *contoso.com*.
- **Perfil** - A conta é configurada com um perfil de dados do usuário. É possível inserir um nome, sobrenome, cargo e nome do departamento. É possível editar o perfil depois que a conta é criada.
- **Grupos** – Use grupos para executar tarefas de gerenciamento, como atribuir licenças ou permissões a muitos usuários ou dispositivos de uma vez. Você pode colocar a nova conta em um [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existente no locatário.
- **Função do diretório** - É necessário especificar o nível de acesso que a conta de usuário tem aos recursos no locatário. Os níveis de permissão a seguir estão disponíveis:

    - **Usuário** - Os usuários podem acessar recursos atribuídos, mas não podem gerenciar a maioria dos recursos do locatário.
    - **Administrador global** - Os administradores globais têm controle total sobre todos os recursos do locatário.
    - **Administrador limitado** - Selecione a função ou funções administrativas para o usuário. Para obter mais informações sobre as funções que podem ser selecionadas, consulte [Atribuindo funções de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Crie uma conta corporativa

É possível usar as seguintes informações para criar uma nova conta corporativa:

- [Portal do Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Atualizar um perfil do usuário

É possível usar as seguintes informações para atualizar o perfil de um usuário:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Redefinir uma senha para um usuário

É possível usar as seguintes informações para redefinir a senha de um usuário:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Usuário Convidado

É possível convidar usuários externos para o locatário como usuário convidado. Um cenário típico para convidar um usuário convidado para o locatário do Azure AD B2C é compartilhar responsabilidades de administração. Para um exemplo de uso de uma conta Convidado, consulte [Propriedades de um usuário de colaboração do Azure Active Directory B2C](../active-directory/external-identities/user-properties.md).

Ao convidar um usuário convidado para o locatário, você fornece o endereço de email do destinatário e uma mensagem descrevendo o convite. O link do convite leva o usuário para a página de consentimento. Se uma caixa de entrada não estiver anexada ao endereço de email, o usuário poderá navegar até a página de consentimento, acessando uma página da Microsoft usando as credenciais de convidado. O usuário é forçado a resgatar o convite da mesma maneira que clicar no link no email. Por exemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

Também é possível usar a [API do Microsoft Graph](/graph/api/invitation-post?view=graph-rest-beta) para convidar um usuário convidado.

## <a name="consumer-user"></a>Usuário Consumidor

O usuário consumidor pode entrar em aplicativos protegidos pelo Azure AD B2C, mas não pode acessar recursos do Azure, como o portal do Azure. O usuário consumidor pode usar uma conta local ou contas federadas como Facebook ou Twitter. Uma conta de consumidor é criada usando um [fluxo de usuário de inscrição ou de entrada](user-flow-overview.md), usando a API de Microsoft Graph ou usando o portal do Azure.

Você pode especificar os dados que são coletados quando uma conta de usuário do consumidor é criada. Para obter mais informações, consulte [adicionar atributos de usuário e personalizar entrada do usuário](configure-user-input.md).

Para obter mais informações sobre como gerenciar contas de consumidor, consulte [gerenciar contas de usuário Azure ad B2C com Microsoft Graph](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Migrar contas de usuários Consumidor

Talvez seja necessário migrar as contas de usuários Consumidor existentes de qualquer provedor de identidade para o Azure AD B2C. Para obter mais informações, consulte [migrar usuários para Azure ad B2C](user-migration.md).
