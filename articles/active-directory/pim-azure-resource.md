---
title: Gerenciar o acesso aos recursos do Azure com o PIM (Privileged Identity Management)
description: "Saiba mais sobre como usar o Gerenciamento de Acesso Baseado em Função no PIM para acessar os recursos do Azure."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 4245d0dd9798046674f9ae3b197cec6b9b5d2545
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management-preview"></a>Gerenciar o acesso aos recursos do Azure com o Privileged Identity Management (Versão Prévia)

Para proteger contas privilegiadas contra ataques cibernéticos mal-intencionados, use o PIM (Azure Active Directory Privileged Identity Management) para reduzir o tempo de exposição de privilégios e aumentar a visibilidade de seu uso por meio de relatórios e alertas. O PIM faz isso limitando os usuários a apenas usar seus privilégios JIT (“Just-In-Time”) ou atribuindo privilégios por uma duração reduzida após a qual eles são revogados automaticamente. 

Agora você pode usar o PIM com o RBAC (Controle de Acesso Baseado em Função) para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O PIM pode gerenciar a associação de funções internas e personalizadas para ajudá-lo a: 

- Habilitar o acesso “Just-In-Time” sob demanda aos recursos do Azure
- Expirar o acesso aos recursos automaticamente para os usuários e grupos atribuídos
- Atribuir o acesso temporário aos recursos do Azure para tarefas rápidas ou escalas de plantão
- Obter alertas quando novos usuários ou grupos recebem o acesso aos recursos e quando eles ativam atribuições qualificadas

Para obter mais informações, consulte [Visão geral do Controle de Acesso Baseado em Função no Azure PIM](privileged-identity-management/azure-pim-resource-rbac.md).