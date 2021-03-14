---
title: Conceder acesso baseado em função para usuários
titleSuffix: Azure Maps
description: Usar o controle de acesso baseado em função para conceder aos usuários autorização para mapas do Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102811"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Conceder acesso baseado em função para usuários do Azure Maps

Conceda o *controle de acesso baseado em função do Azure (RBAC do Azure)* atribuindo um grupo do Azure ad ou entidades de segurança a uma ou mais definições de função do Azure Maps. Para exibir as definições de função do Azure que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)**. Selecione **funções** e, em seguida, procure funções que começam com o *Azure Maps*.

* Para gerenciar com eficiência uma grande quantidade de acesso de usuários aos mapas do Azure, consulte [grupos do Azure ad](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Para que os usuários tenham permissão para autenticar no aplicativo, os usuários devem ser criados no Azure AD. Consulte [Adicionar ou excluir usuários usando o Azure ad](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Leia mais sobre o [Azure ad](../../active-directory/fundamentals/index.yml) para gerenciar efetivamente um diretório para os usuários.

1. Vá para sua **conta do Azure Maps**. Selecione atribuição **de função de controle de acesso (iam)**  >  .

    ![Conceder acesso usando o RBAC do Azure](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na guia **atribuições de função** , em **função**, selecione uma definição de função interna do Azure Maps, como **leitor de dados do Azure Maps** ou colaborador de **dados do Azure Maps**. Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. Selecione a entidade de segurança por nome. Depois, selecione **Salvar**.

   * Consulte os detalhes em [atribuir funções do Azure](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> As definições de função internas do Azure Maps fornecem um acesso de autorização muito grande a muitas APIs REST do Azure Maps. Para restringir as APIs para os usuários a um mínimo, consulte [criar uma definição de função personalizada e atribuir usuários](../../role-based-access-control/custom-roles.md) à definição de função personalizada. Isso permitirá que os usuários tenham o privilégio mínimo necessário para o aplicativo.