---
title: Funções internas área de trabalho virtual do Windows – Azure
description: Uma visão geral das funções internas para a área de trabalho virtual do Windows disponível para o RBAC do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577601"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Funções internas para área de trabalho virtual do Windows

A área de trabalho virtual do Windows usa RBAC (controles de acesso baseado em função) do Azure para atribuir funções a usuários e administradores. Essas funções dão permissão aos administradores para realizar determinadas tarefas. Para saber mais sobre funções internas para o RBAC do Azure, confira [funções internas do Azure](../role-based-access-control/built-in-roles.md).

As funções internas padrão do Azure são proprietário, colaborador e leitor. No entanto, a área de trabalho virtual do Windows tem funções adicionais que permitem separar funções de gerenciamento para pools de hosts, grupos de aplicativos e espaços de trabalho. Essa separação permite que você tenha um controle mais granular sobre as tarefas administrativas. Essas funções são nomeadas em conformidade com as funções padrão do Azure e a metodologia de privilégios mínimos.

A área de trabalho virtual do Windows não tem uma função de proprietário específica. No entanto, você pode usar uma função de proprietário padrão para os objetos de serviço.

## <a name="desktop-virtualization-contributor"></a>Colaborador de virtualização de desktop

A função colaborador de virtualização de desktop permite que você gerencie todos os aspectos da implantação. No entanto, ele não concede a você acesso aos recursos de computação. Você também precisará da função Administrador de acesso do usuário para publicar grupos de aplicativos para usuários ou grupos de usuários.


- Microsoft. DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>Leitor de virtualização de desktop

A função leitor de virtualização de desktop permite exibir tudo na implantação, mas não permite que você faça nenhuma alteração.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>Colaborador do pool de hosts

A função colaborador do pool de hosts permite que você gerencie todos os aspectos dos pools de hosts, incluindo o acesso aos recursos. Você precisará de uma função de colaborador extra, colaborador de máquina virtual, para criar máquinas virtuais. Você precisará de funções de colaborador de AppGroup e espaço de trabalho para criar o pool de hosts usando o portal ou pode usar a função de colaborador de virtualização de desktop.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>Leitor do pool de hosts

A função leitor do pool de hosts permite exibir tudo no pool de hosts, mas não permitirá que você faça nenhuma alteração.

- Microsoft. DesktopVirtualization/hostpools/ \* /Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>Colaborador do grupo de aplicativos

A função colaborador do grupo de aplicativos permite que você gerencie todos os aspectos de grupos de aplicativo. Se você quiser publicar grupos de aplicativos para usuários ou grupos de usuários, precisará da função Administrador de acesso do usuário.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>Leitor do grupo de aplicativos

A função leitor do grupo de aplicativos permite exibir tudo no grupo de aplicativos e não permitirá que você faça nenhuma alteração.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>Colaborador do espaço de trabalho

A função colaborador do espaço de trabalho permite que você gerencie todos os aspectos dos espaços de trabalho. Para obter informações sobre os aplicativos adicionados aos grupos de aplicativos, você também precisará receber a função leitor do grupo de aplicativos.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/Workspaces/\*
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>Leitor de espaço de trabalho

A função leitor de espaço de trabalho permite exibir tudo no espaço de trabalho, mas não permitirá que você faça nenhuma alteração.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/Workspaces/Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>Operador de sessão de usuário

A função de operador de sessão de usuário permite que você envie mensagens, desconecte sessões e use a função "fazer logoff" para assinar sessões fora do host de sessão. No entanto, essa função não permite que você execute o gerenciamento de host de sessão, como remover o host da sessão, alterar o modo de descarga e assim por diante. Essa função pode ver atribuições, mas não pode modificar administradores. Recomendamos que você atribua essa função a pools de hosts específicos. Se você conceder essa permissão em um nível de grupo de recursos, o administrador terá permissão de leitura em todos os pools de hosts em um grupo de recursos.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>Operador de host de sessão

A função colaborador de host de sessão permite exibir e remover hosts de sessão, bem como alterar o modo de descarga. Eles não podem adicionar hosts de sessão usando o portal do Azure porque eles não têm permissão de gravação para objetos do pool de hosts. Se o token de registro for válido (gerado e não expirado), você poderá usar essa função para adicionar hosts de sessão ao pool de hosts fora do portal do Azure se o administrador tiver permissões de computação por meio da função de colaborador da máquina virtual.

A lista a seguir descreve quais permissões essa função pode acessar:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
