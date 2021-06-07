---
title: Perguntas frequentes sobre a central de segurança do Azure-perguntas sobre permissões
description: Estas perguntas FREQUENTEs respondem a perguntas sobre permissões na central de segurança do Azure, um produto que ajuda você a prevenir, detectar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87824606"
---
# <a name="permissions"></a>Permissões

## <a name="how-do-permissions-work-in-azure-security-center"></a>Como as permissões funcionam na central de segurança do Azure?

A Central de Segurança do Azure usa o [RBAC do Azure (controle de acesso baseado em função do Azure)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.



## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?

Para modificar uma política de segurança, você deve ser um administrador de segurança ou um proprietário ou colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](tutorial-security-policy.md).