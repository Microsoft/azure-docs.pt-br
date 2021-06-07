---
title: Gerenciar grupos privilegiados do Azure AD no PIM (Privileged Identity Management) | Microsoft Docs
description: Como gerenciar membros e proprietários de grupos com acesso privilegiado no PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512385"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Funcionalidades de gerenciamento para grupos do Azure AD com acesso privilegiado (versão prévia)

No PIM (Privileged Identity Management), agora você pode atribuir qualificação para associação ou propriedade de grupos com acesso privilegiado. Começando com essa versão prévia, você pode atribuir funções internas do Azure AD (Azure Active Directory) a grupos de nuvem e usar o PIM para gerenciar a qualificação e a ativação de proprietários e membros do grupo. Para obter mais informações sobre grupos passíveis de atribuição de função no Azure AD, confira [Usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../roles/groups-concept.md).

>[!Important]
> Para atribuir uma função de acesso administrativo a um grupo de acesso privilegiado no Exchange, no Centro de Conformidade e Segurança ou no SharePoint, use a experiência **Funções e Administradores** do portal do Azure AD, não a experiência Grupos de Acesso Privilegiado para tornar o usuário ou o grupo qualificado para ativação no grupo.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Exigir políticas diferentes para cada grupo passível de atribuição de função

Algumas organizações usam ferramentas como a colaboração B2B (entre empresas) do Azure AD para convidar os respectivos parceiros como convidados para a organização do Azure AD delas. Em vez de apenas uma política just-in-time para todas as atribuições a uma função com privilégios, você pode criar dois grupos de acesso privilegiado diferentes, cada um com suas próprias políticas. Você pode impor requisitos menos rígidos para seus funcionários confiáveis e requisitos mais estritos, como o fluxo de trabalho de aprovação, para seus parceiros quando eles solicitam ativação no grupo ao qual foram atribuídos.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Ativar várias atribuições de função em uma única solicitação

Com a versão prévia dos grupos com acesso privilegiado, você pode usar apenas uma solicitação just-in-time para fornecer a administradores de cargas de trabalho específicas acesso rápido a várias funções. Por exemplo, os administradores da Camada 3 do Office podem precisar de acesso just-in-time às funções de administrador do Exchange, administrador dos Aplicativos do Office, administrador do Teams e administrador da Pesquisa para investigar os incidentes minuciosamente e todos os dias. No passado, isso exigiria quatro solicitações consecutivas, que representam um processo que leva algum tempo. Em vez disso, você pode criar um grupo de atribuições de função chamado "Administradores do Office da Camada 3", atribuí-lo a cada uma das quatro funções mencionadas anteriormente (ou a qualquer função interna do Azure AD) e habilitá-lo para Acesso Privilegiado na seção Atividade do grupo. Uma vez habilitado para acesso privilegiado, será possível definir as configurações just-in-time para os membros do grupo e atribuir seus administradores e proprietários conforme eles forem qualificados. Quando os administradores se elevarem a participantes do grupo, eles se tornarão membros de todas as quatro funções do Azure AD.

## <a name="extend-and-renew-group-assignments"></a>Estender e renovar atribuições de grupo

Depois de configurar suas atribuições de proprietário ou de membro associadas ao tempo, a primeira pergunta que você pode fazer é: o que acontecerá se uma atribuição expirar? Nesta nova versão, fornecemos duas opções para esse cenário:

- Estender – quando uma atribuição de função se aproxima da expiração, o usuário pode usar o Privileged Identity Management para solicitar uma extensão para a atribuição de função
- Renovar – quando uma atribuição de função já expirou, o usuário pode usar o Privileged Identity Management para solicitar uma renovação da atribuição de função

As duas ações iniciadas pelo usuário exigem uma aprovação de um Administrador global ou de um Administrador de funções com privilégios. Os administradores não precisarão mais se preocupar com o gerenciamento dessas expirações. Eles podem apenas esperar pelas solicitações de renovação e aprová-las se forem válidas.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir um proprietário ou membro do grupo de acesso privilegiado](groups-assign-member-owner.md)
- [Aprovar ou negar solicitações de ativação para membros e proprietários do grupo de acesso privilegiado](groups-approval-workflow.md)
