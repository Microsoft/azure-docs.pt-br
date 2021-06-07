---
title: Grupos dinâmicos e colaboração B2B – Azure Active Directory | Microsoft Docs
description: Mostra como usar os grupos dinâmicos do Azure AD com a colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441548"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e Colaboração do Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da associação de grupo de segurança para o Azure AD (Azure Active Directory) está disponível [no portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher os grupos criados no Azure AD com base nos atributos do usuário (como UserType, departamento ou país/região). Os membros podem ser adicionados ou removidos automaticamente de um grupo de segurança com base nas alterações de seus atributos. Esses grupos podem fornecer acesso a aplicativos ou a recursos de nuvem (como sites e documentos do SharePoint) e para atribuir licenças a membros. Leia mais sobre grupos dinâmicos em [Grupos dedicados no Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

O [licenciamento Azure ad Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) apropriado é necessário para criar e usar grupos dinâmicos. Saiba mais no artigo [Criar regras baseadas em atributo para associação dinâmica de grupo no Azure Active Directory](../enterprise-users/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Criando um grupo dinâmico "todos os usuários"
Você pode criar um grupo contendo todos os usuários dentro de um locatário usando uma regra de associação. Quando os usuários são adicionados ou removidos do locatário no futuro, a associação do grupo é ajustada automaticamente.

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que é atribuída à função de administrador da empresa ou de administrador de usuários no locatário.
1. Selecione **Azure Active Directory**.
2. Em **Gerenciar**, selecione **Grupos** e, em seguida, selecione **Novo grupo**.
1. Na página **novo grupo** , em **tipo de grupo**, selecione **segurança**. Insira o **Nome do grupo** e a **Descrição do grupo** para o novo grupo. 
2. Em **tipo de associação**, selecione **usuário dinâmico** e, em seguida, selecione **Adicionar consulta dinâmica**. 
4. Acima da caixa de texto **sintaxe da regra** , selecione **Editar**. Na página **sintaxe de editar regra** , digite a seguinte expressão na caixa de texto:

   ```
   user.objectId -ne null
   ```
1. Selecione **OK**. A regra aparece na caixa sintaxe de regra:

   ![Sintaxe de regra para todos os usuários grupo dinâmico](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Clique em **Salvar**. O novo grupo dinâmico agora incluirá usuários convidados B2B, assim como usuários membros.


1. Selecione **criar** na página **novo grupo** para criar o grupo.

## <a name="creating-a-group-of-members-only"></a>Criando apenas um grupo de membros

Se você quiser que o grupo exclua os usuários convidados e inclua somente os membros do seu locatário, crie um grupo dinâmico conforme descrito acima, mas na caixa **sintaxe de regra** , insira a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

A imagem a seguir mostra a sintaxe de regra para um grupo dinâmico modificado para incluir somente membros e excluir convidados.

![Mostra a regra em que o tipo de usuário é igual ao membro](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Criando apenas um grupo de convidados

Também pode ser útil criar um novo grupo dinâmico que contenha apenas os usuários convidados, para que você possa aplicar políticas (como as políticas de acesso condicional do Azure AD) a eles. Crie um grupo dinâmico conforme descrito acima, mas na caixa **sintaxe de regra** , insira a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

A imagem a seguir mostra a sintaxe de regra para um grupo dinâmico modificado para incluir somente convidados e excluir usuários membros.

![Mostra a regra onde o tipo de usuário é igual a convidado](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
- [Acesso condicional para usuários de colaboração B2B](conditional-access.md)