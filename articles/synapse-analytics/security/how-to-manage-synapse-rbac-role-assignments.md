---
title: Como gerenciar atribuições de RBAC Synapse no Synapse Studio
description: Este artigo descreve como atribuir e revogar funções RBAC Synapse para entidades de segurança do AAD
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: c88371eb450d5b0e2e8b821cdec0e87190e291f8
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218188"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Como gerenciar atribuições de função do RBAC Synapse no Synapse Studio

O RBAC Synapse usa funções para atribuir permissões a usuários, grupos e outras entidades de segurança para habilitar o acesso e o uso de recursos Synapse e artefatos de código.  [Saiba mais](./synapse-workspace-synapse-rbac.md)

Este artigo mostra como adicionar e excluir atribuições de função do RBAC Synapse.

>[!Note]
>- Para gerenciar as atribuições de função do RBAC Synapse, você precisa ter a função de administrador Synapse no espaço de trabalho ou em um escopo de nível inferior que inclua os objetos que você deseja gerenciar. Se você for um administrador do Synapse no espaço de trabalho, poderá conceder acesso a todos os objetos no espaço de trabalho. 
>- **Os usuários convidados** de um locatário diferente do AD não podem ver ou gerenciar atribuições de função, mesmo que tenham sido atribuídas à função de administrador Synapse.
>- Para ajudá-lo a obter acesso a um espaço de trabalho caso nenhum administrador de Synapse seja atribuído ou esteja disponível para você, os usuários com permissões para gerenciar as atribuições de função do **RBAC do Azure** no espaço de trabalho também podem gerenciar atribuições de função do **Synapse RBAC** , permitindo a adição do administrador do Synapse ou outras atribuições de função do Synapse.
>- O acesso a pools SQL é gerenciado usando permissões SQL.  Com exceção do administrador do Synapse e das funções de administrador do Synapse do SQL, as funções do Synapse RBAC não concedem acesso aos pools do SQL.

>[!important]
>- As alterações feitas nas atribuições de função RBAC Synapse podem levar de 2-5 minutos para entrar em vigor. 
>- Se você estiver gerenciando permissões RBAC Synapse modificando a associação de grupos de segurança, as alterações na associação serão gerenciadas usando Azure Active Directory.  As alterações nas associações de grupo podem levar de 10-15 minutos ou mais para entrar em vigor.

## <a name="open-synapse-studio"></a>Abrir o Synapse Studio  

Para atribuir uma função a um usuário, grupo, entidade de serviço ou identidade gerenciada, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho. 

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png) 
 
 Depois de abrir seu espaço de trabalho, expanda a seção **segurança** à esquerda e selecione **controle de acesso**. 

 ![Selecione o controle de acesso na seção segurança à esquerda](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

A tela controle de acesso lista as atribuições de função atuais.  Você pode filtrar a lista por nome principal ou por email e filtrar seletivamente os tipos de objeto, as funções ou os escopos incluídos. Nessa tela, você pode adicionar ou remover atribuições de função.  

## <a name="add-a-synapse-role-assignment"></a>Adicionar uma atribuição de função Synapse

Na tela controle de acesso, selecione **+ Adicionar** para criar uma nova atribuição de função

![Clique em + Adicionar para criar uma nova atribuição de função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Na guia adicionar atribuição de função, você pode criar atribuições de função em escopo de espaço de trabalho ou escopo de item de espaço de trabalho. 

## <a name="add-workspace-scoped-role-assignment"></a>Adicionar atribuição de função no escopo do espaço de trabalho

Primeiro, selecione **espaço de trabalho** como o escopo e, em seguida, selecione a **função RBAC Synapse**.  Selecione a **(s) entidade (ões)** à qual a função será atribuída e crie a (s) atribuição (ões) de função. 

![Adicionar atribuição de função de espaço de trabalho-selecionar função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

A função atribuída será aplicada a todos os objetos aplicáveis no espaço de trabalho.

## <a name="add-workspace-item-scoped-role-assignment"></a>Adicionar item de espaço de trabalho-atribuição de função no escopo

Para atribuir uma função em um escopo mais refinado, selecione **item de espaço de trabalho** como o escopo e, em seguida, selecione o **tipo de item** de escopo.       

![Adicionar atribuição de função de item de espaço de trabalho-Selecionar tipo de item](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Selecione o **Item** específico a ser usado como escopo e, em seguida, selecione a **função** a ser atribuída na lista suspensa.  O menu suspenso lista somente as funções que são válidas para o tipo de item selecionado. [Saiba mais](./synapse-workspace-synapse-rbac.md).  

![Adicionar atribuição de função de item de espaço de trabalho-selecionar função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Em seguida, **Selecione a (s) entidade (is)** à qual a função deve ser atribuída.  Você pode selecionar várias entidades iterativamente.  Selecione **aplicar** para criar a (s) atribuição (ões) de função.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Remover uma atribuição de função RBAC Synapse

Para revogar o acesso RBAC Synapse, remova as atribuições de função apropriadas.  Na tela controle de acesso, use os filtros para localizar as atribuições de função a serem removidas.  Verifique as atribuições de função e selecione **Remover acesso**.   

![Excluir uma atribuição de função para remover o acesso](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Lembre-se de que as alterações nas atribuições de função levarão de 2-5 minutos para entrar em vigor.   

## <a name="next-steps"></a>Próximas etapas

[Entender as funções RBAC Synapse necessárias para executar tarefas comuns](./synapse-workspace-understand-what-role-you-need.md)