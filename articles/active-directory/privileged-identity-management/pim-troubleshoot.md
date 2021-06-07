---
title: Solucionar um problema com o Privileged Identity Management no Microsoft Azure Active Directory | Microsoft Docs
description: Saiba como solucionar problemas de erros do sistema com funções no PIM (Azure AD Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcbebcb32e912abdf0112007c743c6890fae36e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372388"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Solucionar um problema com Privileged Identity Management

Você tem algum problema com PIM no Azure AD (Microsoft Azure AD)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso negado aos recursos do Microsoft Azure

### <a name="problem"></a>Problema

Como proprietário ativo ou administrador de acesso do usuário para um recurso do Azure, você pode ver seu recurso dentro do Privileged Identity Management, mas não pode executar ações, como fazer uma atribuição qualificada ou exibir uma lista de atribuições de função na página Visão geral do recurso. Todas essas ações resultam em um erro de autorização.

### <a name="cause"></a>Causa

Esse problema pode ocorrer quando a função Administrador de Acesso do Usuário para a entidade de serviço PIM foi removida da assinatura acidentalmente. Para que o serviço de Privileged Identity Management seja capaz de acessar recursos do Azure, a entidade de serviço do MS-PIM sempre deve ter atribuída a [função de Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) pela assinatura do Azure.

### <a name="resolution"></a>Resolução

Atribua a função de Administrador de Acesso do Usuário ao nome da entidade de serviço do Privileged Identity Management (MS – PIM) no nível da assinatura. Essa atribuição deve permitir que o serviço Privileged Identity Management acesse os recursos do Azure. A função pode ser atribuída no nível de grupo de gerenciamento ou no nível de assinatura, dependendo de seus requisitos. Para saber mais sobre entidades de serviço, consulte [Atribuir um aplicativo em uma função](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application).

## <a name="next-steps"></a>Próximas etapas

- [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md)
- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar o Privileged Identity Management](pim-deployment-plan.md)