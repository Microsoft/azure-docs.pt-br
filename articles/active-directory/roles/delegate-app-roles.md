---
title: Delegar permissões de administrador de gerenciamento de aplicativos-Azure AD | Microsoft Docs
description: Conceder permissões para o gerenciamento de acesso do aplicativo no Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fea9662b6f5890c6240e91b26cf641d6166d560a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051184"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegar permissões de registro de aplicativo no Azure Active Directory

Este artigo descreve como usar permissões concedidas por funções personalizadas no Azure Active Directory (Azure AD) para atender às necessidades de gerenciamento de aplicativos. No Azure AD, você pode delegar permissões de criação e gerenciamento de aplicativos das seguintes maneiras:

- [Restringir quem pode criar aplicativos](#restrict-who-can-create-applications) e gerenciar os aplicativos que eles criam. Por padrão, no Azure AD, todos os usuários podem registrar aplicativos e gerenciar todos os aspectos de aplicativos que criam. Isso pode ser restrito para permitir apenas pessoas selecionadas que tenham permissão.
- [Atribuição de um ou mais proprietários a um aplicativo](#assign-application-owners). Essa é uma maneira simples de conceder a alguém a capacidade de gerenciar todos os aspectos da configuração do Azure AD para um aplicativo específico.
- [Atribuição de uma função administrativa interna](#assign-built-in-application-admin-roles) que concede acesso para gerenciar a configuração no Azure ad para todos os aplicativos. Essa é a maneira recomendada para conceder aos especialistas de ti o acesso para gerenciar as amplas permissões de configuração de aplicativo sem conceder acesso para gerenciar outras partes do Azure AD não relacionadas à configuração do aplicativo.
- [Criar uma função personalizada](#create-and-assign-a-custom-role-preview) definindo permissões muito específicas e atribuindo-a a alguém para o escopo de um único aplicativo como proprietário limitado ou no escopo do diretório (todos os aplicativos) como um administrador limitado.

É importante considerar a concessão de acesso usando um dos métodos acima por dois motivos. Primeiro, delegar a capacidade de executar tarefas administrativas reduz a sobrecarga de administrador global. Em segundo lugar, o uso de permissões limitadas melhora sua postura de segurança e reduz a possibilidade de acesso não autorizado. Problemas de delegação e diretrizes gerais são discutidos em [Delegar a administração no Azure Active Directory](concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Restringir quem pode criar aplicativos

Por padrão, no Azure AD, todos os usuários podem registrar aplicativos e gerenciar todos os aspectos de aplicativos que criam. Todos também têm a capacidade de dar consentimento a aplicativos que acessam dados da empresa em seu nome. Você pode optar por conceder essas permissões seletivamente definindo as opções globais como ' não ' e adicionando os usuários selecionados à função de desenvolvedor do aplicativo.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Para desabilitar a capacidade padrão de criar registros de aplicativo ou consentimento para aplicativos

1. Entre na sua organização do Azure AD com uma conta que seja qualificada para a função de administrador global na sua organização do Azure AD.
1. Defina um ou ambos os itens a seguir:

    - Na [página Configurações do usuário da sua organização](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), defina a configuração **usuários podem registrar aplicativos** como não. Isso desabilitará a capacidade padrão para os usuários criarem registros de aplicativo.
    - Nas [configurações de usuário para aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), defina os **usuários podem consentir os aplicativos que acessam dados da empresa em sua** configuração de nome como não. Isso desabilitará a capacidade padrão para os usuários consentirem com os aplicativos que acessam os dados da empresa em seu nome.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Conceder permissões individuais para criar e consentir aplicativos quando a capacidade padrão for desabilitada

Atribua a função de desenvolvedor de aplicativo para conceder a capacidade de criar registros de aplicativo quando a configuração **usuários podem registrar aplicativos** estiver definida como não. Essa função também concede permissão para consentimento em um nome próprio quando os **usuários podem consentir que os aplicativos que acessam dados da empresa em sua** configuração de nome estão definidos como não. Como um comportamento do sistema, quando um usuário cria um novo registro de aplicativo, ele é adicionado automaticamente como o primeiro proprietário. As permissões de propriedade dão ao usuário a capacidade de gerenciar todos os aspectos de um registro de aplicativo ou aplicativo empresarial que eles possuem.

## <a name="assign-application-owners"></a>Atribuir proprietários de aplicativos

A atribuição de proprietários é uma maneira simples de conceder a capacidade de gerenciar todos os aspectos da configuração do Azure AD para um registro de aplicativo específico ou aplicativo empresarial. Como um comportamento do sistema, quando um usuário cria um novo registro de aplicativo, ele é adicionado automaticamente como o primeiro proprietário. As permissões de propriedade dão ao usuário a capacidade de gerenciar todos os aspectos de um registro de aplicativo ou aplicativo empresarial que eles possuem. O proprietário original pode ser removido e proprietários adicionais podem ser adicionados.

### <a name="enterprise-application-owners"></a>Proprietários de aplicativos empresariais

Como proprietário, um usuário pode gerenciar a configuração específica da organização do aplicativo empresarial, como a configuração de logon único, o provisionamento e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários podem gerenciar somente os aplicativos empresariais que eles possuem.

Em alguns casos, os aplicativos empresariais criados na Galeria de aplicativos incluem um aplicativo empresarial e um registro de aplicativo. Quando isso for verdadeiro, adicionar um proprietário ao aplicativo empresarial adicionará automaticamente o proprietário ao registro do aplicativo correspondente como um proprietário.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Para atribuir um proprietário a um aplicativo empresarial

1. Entre na [sua organização do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta qualificada para o administrador de aplicativos ou o administrador de aplicativos de nuvem para a organização.
1. Na [página registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) da organização, selecione um aplicativo para abrir a página Visão geral do aplicativo.
1. Selecione **Proprietários** para ver a lista de proprietários do aplicativo.
1. Selecione **Adicionar** para selecionar um ou mais proprietários para adicionar ao aplicativo.

> [!IMPORTANT]
> Os usuários e as entidades de serviço podem ser proprietários de registros do aplicativo. Somente os usuários podem ser proprietários de aplicativos empresariais. Os grupos não podem ser atribuídos como proprietários de ambos.
>
> Os proprietários podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. O aplicativo pode ter mais permissões do que o proprietário e, portanto, seria uma elevação de privilégio sobre a qual o proprietário tem acesso como um usuário ou uma entidade de serviço. Um proprietário de aplicativo poderia, potencialmente, criar ou atualizar usuários ou outros objetos ao representar o aplicativo, dependendo das permissões do aplicativo.

## <a name="assign-built-in-application-admin-roles"></a>Atribuir funções de administrador de aplicativos internas

O Azure AD tem um conjunto de funções de administrador internas para conceder acesso para gerenciar a configuração no Azure AD para todos os aplicativos. Essas funções são a maneira recomendada para conceder aos especialistas de ti acesso para gerenciar as amplas permissões de configuração de aplicativo sem conceder acesso para gerenciar outras partes do Azure AD não relacionadas à configuração do aplicativo.

- Administrador de Aplicativos: usuários nesta função podem criar e gerenciar todos os aspectos de aplicativos corporativos, registros de aplicativo e as configurações de proxy de aplicativo. Essa função também concede a capacidade de consentir com permissões delegadas e permissões de aplicativo, exceto Microsoft Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.
- Administrador de Aplicativos de Nuvem: os usuários nesta função têm as mesmas permissões que a função de administrador do aplicativo, exceto a capacidade de gerenciar o proxy de aplicativo. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

Para obter mais informações e exibir a descrição dessas funções, consulte [funções internas do Azure ad](permissions-reference.md).

Siga as instruções em [atribuir funções a usuários com Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) guia de instruções para atribuir as funções administrador de aplicativos ou administrador de aplicativos de nuvem.

> [!IMPORTANT]
> Os administradores de aplicativos e os administradores de aplicativos de nuvem podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. O aplicativo pode ter permissões que são uma elevação de privilégio sobre as permissões da função de administrador. Um administrador nessa função poderia criar ou atualizar usuários ou outros objetos ao representar o aplicativo, dependendo das permissões do aplicativo.
> Nenhuma dessas funções fornece a capacidade de gerenciar configurações de Acesso Condicional.

## <a name="create-and-assign-a-custom-role-preview"></a>Criar e atribuir uma função personalizada (visualização)

A criação de funções personalizadas e a atribuição de funções personalizadas são etapas separadas:

- [Crie uma *definição de função* personalizada](custom-create.md) e [Adicione permissões a ela de uma lista predefinida](custom-available-permissions.md). Essas são as mesmas permissões usadas nas funções internas.
- [Crie uma *atribuição de função*](custom-assign-powershell.md) para atribuir a função personalizada.

Essa separação permite que você crie uma única definição de função e, em seguida, atribua-a muitas vezes em *escopos* diferentes. Uma função personalizada pode ser atribuída em escopo de toda a organização ou pode ser atribuída no escopo se um único objeto do Azure AD. Um exemplo de escopo de objeto é um registro de aplicativo único. Usando escopos diferentes, a mesma definição de função pode ser atribuída a Sally sobre todos os registros de aplicativo na organização e, em seguida, para Naveen somente pelo registro do aplicativo de relatórios de despesas da contoso.

Dicas ao criar e usar funções personalizadas para delegar o gerenciamento de aplicativos:
- As funções personalizadas só concedem acesso nas folhas de registro de aplicativo mais atuais do portal do Azure AD. Eles não concedem acesso nas folhas de registros do aplicativo herdado.
- As funções personalizadas não concedem acesso ao portal do Azure AD quando a configuração de usuário "restringir o acesso ao portal de administração do AD do Azure" está definida como Sim.
- Registros de aplicativo o usuário tem acesso ao uso de atribuições de função somente aparece na guia ' todos os aplicativos ' na página de registro do aplicativo. Eles não aparecem na guia "aplicativos de propriedade".

Para obter mais informações sobre as noções básicas de funções personalizadas, consulte a [visão geral das funções personalizadas](custom-overview.md), bem como como [criar uma função personalizada](custom-create.md) e como [atribuir uma função](custom-assign-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- [Subtipos de registro de aplicativo e permissões](custom-available-permissions.md)
- [Referência da função de administrador do Azure AD](permissions-reference.md)
