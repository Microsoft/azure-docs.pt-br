---
title: Cenários comuns no gerenciamento de direitos-Azure AD
description: Conheça as etapas de alto nível que devem ser seguidas para cenários comuns no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f45a1481661aa350815560d795ab7411f99545
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92317838"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Cenários comuns no gerenciamento de direitos do Azure AD

Há várias formas de configurar o gerenciamento de direitos para sua organização. No entanto, se você estiver apenas começando, é útil entender os cenários comuns para administradores, proprietários de catálogo, gerenciadores de pacotes de acesso, aprovadores e solicitantes.

## <a name="delegate"></a>Delegar

### <a name="administrator-delegate-management-of-resources"></a>Administrador: delegar o gerenciamento de recursos

1. [Assista ao vídeo: Delegação a partir dele para o gerente do departamento](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegar usuários para a função de criador de catálogo](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Criador do catálogo: delegar o gerenciamento de recursos

- [Criar um novo catálogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietário do catálogo: delegar o gerenciamento de recursos

1. [Adicionar coproprietários ao catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Adicionar recursos ao catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietário do catálogo: delegar o gerenciamento de pacotes de acesso

1. [Assista ao vídeo: Delegação do proprietário do catálogo para acessar o Gerenciador de pacotes](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegar usuários a acessar a função do Gerenciador de pacotes](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Controlar o acesso dos usuários em sua organização

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gerenciador de pacotes do Access: permitir que os funcionários em sua organização solicitem acesso aos recursos

1. [Criar um pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipes, aplicativos ou sites do SharePoint para acessar o pacote](entitlement-management-access-package-create.md#resource-roles)
1. [Adicionar uma política de solicitação para permitir que os usuários em seu diretório solicitem acesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Especificar configurações de expiração](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Solicitante: solicitar acesso a recursos

1. [Entrar no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Localizar pacote de acesso
1. [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: aprovar solicitações para recursos

1. [Abrir solicitação no meu portal de acesso](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Solicitante: exiba os recursos aos quais você já tem acesso

1. [Entrar no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Exibir pacotes do Access ativos

## <a name="govern-access-for-users-outside-your-organization"></a>Controlar o acesso dos usuários fora da sua organização

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrador: colaborar com uma organização de parceiro externo

1. [Leia como o Access funciona para usuários externos](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Examinar as configurações para usuários externos](entitlement-management-external-users.md#settings-for-external-users)
1. [Adicionar uma conexão à organização externa](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gerenciador de pacotes do Access: colaborar com uma organização de parceiro externo

1. [Criar um pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipes, aplicativos ou sites do SharePoint para acessar o pacote](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Adicionar uma política de solicitação para permitir que os usuários que não estão em seu diretório solicitem acesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Especificar configurações de expiração](entitlement-management-access-package-create.md#lifecycle)
1. [Copie o link para solicitar o pacote de acesso](entitlement-management-access-package-settings.md)
1. Envie o link para o parceiro de contato do parceiro externo para compartilhar com seus usuários

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Solicitante: solicitar acesso a recursos como um usuário externo

1. Localizar o link do pacote de acesso que você recebeu do seu contato
1. [Entrar no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: aprovar solicitações para recursos

1. [Abrir solicitação no meu portal de acesso](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Solicitante: exiba os recursos aos quais você já tem acesso

1. [Entrar no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Exibir pacotes do Access ativos

## <a name="day-to-day-management"></a>Gerenciamento diário

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gerenciador de pacotes do Access: atualizar os recursos de um projeto

1. [Assista ao vídeo: gerenciamento diário: as coisas foram alteradas](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abrir o pacote de acesso
1. [Adicionar ou remover grupos, equipes, aplicativos ou sites do SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gerenciador de pacotes do Access: atualizar a duração de um projeto

1. [Assista ao vídeo: gerenciamento diário: as coisas foram alteradas](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abrir o pacote de acesso
1. [Abrir as configurações do ciclo de vida](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Atualizar as configurações de expiração](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gerenciador de pacotes do Access: atualizar como o acesso é aprovado para um projeto

1. [Assista ao vídeo: gerenciamento diário: as coisas foram alteradas](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Abrir uma política existente de configurações de solicitação](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Atualizar as configurações de aprovação](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gerenciador de pacotes do Access: atualizar as pessoas para um projeto

1. [Assista ao vídeo: gerenciamento diário: as coisas foram alteradas](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Remover usuários que não precisam mais de acesso](entitlement-management-access-package-assignments.md)
1. [Abrir uma política existente de configurações de solicitação](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Adicionar usuários que precisam de acesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gerenciador de pacotes do Access: atribuir usuários específicos diretamente a um pacote do Access

1. [Se os usuários precisarem de configurações de ciclo de vida diferentes, adicione uma nova política ao pacote de acesso](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Atribuir usuários específicos diretamente ao pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Atribuições e relatórios

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrador: Exibir quem tem atribuições a um pacote do Access

1. Abrir um pacote de acesso
1. [Exibir atribuições](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Arquivar relatórios e logs](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrador: Exibir recursos atribuídos aos usuários

1. [Exibir pacotes de acesso para um usuário](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Exibir atribuições de recursos para um usuário](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administração programática

Você também pode gerenciar pacotes de acesso, catálogos, políticas, solicitações e atribuições usando Microsoft Graph.  Um usuário em uma função adequada com um aplicativo que tem a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a [API de gerenciamento de direitos](/graph/tutorial-access-package-api?view=graph-rest-beta).

## <a name="next-steps"></a>Próximas etapas

- [Delegação e funções](entitlement-management-delegate.md)
- [Solicitar notificações de processo e email](entitlement-management-process.md)