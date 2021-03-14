---
title: Conceder e solicitar permissões em todo o locatário na central de segurança do Azure
description: Saiba como gerenciar permissões em todo o locatário na central de segurança do Azure
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617481"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Conceder e solicitar visibilidade em todo o locatário

Um usuário com a função de Azure Active Directory (AD) do **administrador global** pode ter responsabilidades em todo o locatário, mas não tem as permissões do Azure para exibir as informações de toda a organização na central de segurança do Azure. A elevação de permissões é necessária porque as atribuições de função do Azure AD não concedem acesso aos recursos do Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Conceder permissões em todo o locatário para você mesmo

Para atribuir permissões em nível de locatário:

1. Se sua organização gerencia o acesso a recursos com [Azure ad Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)ou qualquer outra ferramenta PIM, a função de administrador global deve estar ativa para o usuário seguindo o procedimento abaixo.

1. Como um usuário administrador global sem uma atribuição no grupo de gerenciamento raiz do locatário, abra a página **visão geral** da central de segurança e selecione o link **visibilidade em todo o locatário** na faixa. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Habilitar permissões em nível de locatário na central de segurança do Azure":::

1. Selecione a nova função do Azure a ser atribuída. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulário para definir as permissões de nível de locatário a serem atribuídas ao usuário":::

    > [!TIP]
    > Geralmente, a função Admin de Segurança é necessária para aplicar políticas no nível raiz, enquanto Leitor de Segurança será suficiente para fornecer visibilidade no nível dos locatários. Para obter mais informações sobre as permissões concedidas por essas funções, consulte a [descrição da função interna Admin de Segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a [descrição da função interna Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Para obter as diferenças entre essas funções específicas à central de segurança, consulte a tabela em [funções e ações permitidas](security-center-permissions.md#roles-and-allowed-actions).

    A exibição de toda a organização é obtida com a concessão de funções no nível do grupo de gerenciamento raiz do locatário.  

1. Faça logoff do portal do Azure e, em seguida, faça logon novamente.

1. Após elevar o acesso, abra ou atualize a Central de Segurança do Azure para verificar se você tem visibilidade de todas as assinaturas do locatário do Azure AD. 

O processo simples acima executa várias operações automaticamente para você:

1. As permissões do usuário são temporariamente elevadas.
1. Usando as novas permissões, o usuário é atribuído à função de RBAC do Azure desejada no grupo de gerenciamento raiz.
1. As permissões elevadas são removidas.

Para obter mais detalhes sobre o processo de elevação do Azure AD, consulte [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Solicitar permissões em todo o locatário quando as suas não forem suficientes

Se você fizer logon na central de segurança e vir uma faixa informando que sua exibição é limitada, você pode clicar para enviar uma solicitação ao administrador global da sua organização. Na solicitação, você pode incluir a função que deseja atribuir e o administrador global tomará uma decisão sobre qual função conceder. 

É a decisão do administrador global se você deve aceitar ou rejeitar essas solicitações. 

> [!IMPORTANT]
> Você só pode enviar uma solicitação a cada sete dias.

Para solicitar permissões elevadas de seu administrador global:

1. No portal do Azure, abra a central de segurança do Azure.

1. Se você vir a faixa "você está vendo informações limitadas". Selecione-o.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Faixa informando a um usuário que ele pode solicitar permissões em todo o locatário.":::

1. No formulário de solicitação detalhado, selecione a função desejada e a justificativa de por que você precisa dessas permissões.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Página de detalhes para solicitar permissões em todo o locatário do administrador global do Azure":::

1. Selecione **Solicitar acesso**.

    Um email é enviado para o administrador global. O email contém um link para a central de segurança onde eles podem aprovar ou rejeitar a solicitação.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Email para o administrador global para obter novas permissões":::

    Depois que o administrador global selecionar **examinar a solicitação** e concluir o processo, a decisão será enviada por email para o usuário solicitante. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as permissões da central de segurança na seguinte página relacionada:

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)