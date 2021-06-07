---
title: Como examinar as atribuições de função do RBAC Synapse no Synapse Studio
description: Este artigo descreve como examinar as atribuições de função do RBAC Synapse usando o Synapse Studio
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102148"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Como examinar as atribuições de função do RBAC Synapse

As funções RBAC Synapse são usadas para atribuir permissões a usuários, grupos e outras entidades de segurança para habilitar o acesso e o uso de recursos do Synapse.  [Saiba mais](./synapse-workspace-synapse-rbac.md)

Este artigo explica como examinar as atribuições de função atuais para um espaço de trabalho.

Com qualquer função RBAC Synapse, você pode listar as atribuições de função do RBAC Synapse para todos os escopos, incluindo atribuições para objetos aos quais você não tem acesso. Somente um administrador de Synapse pode conceder acesso de RBAC Synapse.  

>[!Note]
>Os usuários convidados (usuários de um locatário diferente do AD) não podem ver ou gerenciar atribuições de função, mesmo que tenham sido atribuídas à função de administrador Synapse.    

## <a name="open-synapse-studio"></a>Abrir o Synapse Studio  

Para revisar as atribuições de função, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho. 

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png) 
 
 Depois de abrir seu espaço de trabalho, selecione o Hub **gerenciar** à esquerda, em seguida, expanda a seção **segurança** e selecione **controle de acesso**. 

 ![Selecione o controle de acesso na seção segurança à esquerda](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Examinar atribuições de função do espaço de trabalho

A tela controle de acesso lista todas as atribuições de função atuais para o espaço de trabalho, agrupadas por função. Cada atribuição inclui o nome principal, o tipo de entidade de segurança, a função e seu escopo.

![Tela de controle de acesso](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Se uma entidade de segurança for atribuída à mesma função em escopos diferentes, você verá várias atribuições para a entidade de segurança, uma para cada escopo.  

Se uma função for atribuída a um grupo de segurança, você verá as funções explicitamente atribuídas ao grupo, mas não as funções herdadas dos grupos pai.  

Você pode filtrar a lista por nome principal ou por email e filtrar seletivamente os tipos de objeto, as funções e os escopos. Insira seu nome ou alias de email no filtro de nome para ver as funções atribuídas a você. Somente um administrador de Synapse pode alterar suas funções.

>[!Important] 
>Se você estiver diretamente ou indiretamente em um membro de um grupo que tenha funções atribuídas, você poderá ter permissões que não são mostradas.

>[!tip]
>Você pode encontrar suas associações de grupo usando Azure Active Directory no portal do Azure.  

Se você criar um novo espaço de trabalho, você e a entidade de serviço MSI de espaço de trabalho receberão automaticamente a função de administrador Synapse no escopo do espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Saiba [como gerenciar atribuições de função do RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md).

Saiba [qual função você precisa para realizar tarefas específicas](./synapse-workspace-understand-what-role-you-need.md)