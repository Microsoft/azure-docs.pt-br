---
title: Criar uma revisão de acesso das funções de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 12/08/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a618da7c9a66b8f687c1b75914530080ed56bea
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905818"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management

O acesso a funções privilegiadas de recursos do Azure para funcionários muda ao longo do tempo. Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar o acesso regularmente. Você pode usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para criar revisões de acesso para funções de recursos do Azure com privilégios. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais revisões de acesso para funções privilegiadas de recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador de Função com Privilégios](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Abrir revisões de acesso

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função Administrador de Funções com Privilégios.

1. Abra **Azure ad Privileged Identity Management**.

1. No menu à esquerda, selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura.

1. Em gerenciar, selecione **revisões de acesso**.

    ![Recursos do Azure-lista de revisões de acesso mostrando o status de todas as revisões](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso mostrando o status da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar as instruções sobre como [revisar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso à medida que os revisores concluírem suas revisões na página **visão geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-resource-roles-complete-access-review.md).

![Página Visão geral das revisões de acesso mostrando os detalhes da revisão](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso for concluído ou o administrador parar a revisão de acesso, siga as etapas em [concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso e você encontrará ocorrências futuras nas revisões agendadas e edite a data de término ou adicione/remova revisores adequadamente.

Com base em suas seleções nas **configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando você interromper manualmente a revisão. O status da revisão será alterado de **concluído** por meio de Estados intermediários, como **aplicar** e, por fim, o estado **aplicado**. Você deve esperar que os usuários negados, se houver, sejam removidos das funções em alguns minutos.

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md)
- [Concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md)
- [Criar uma revisão de acesso das funções do Azure AD](pim-how-to-start-security-review.md)
