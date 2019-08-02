---
title: Concluir uma revisão de acesso de grupos ou aplicativos-Azure Active Directory | Microsoft Docs
description: Saiba como concluir uma revisão de acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c405032368ffd06f5808bc4518302d2f6d66b9
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489154"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Concluir uma revisão de acesso de grupos ou aplicativos nas revisões de acesso do Azure AD

Como administrador, você [cria uma revisão de acesso de grupos ou aplicativos](create-access-review.md) e revisores [executam a revisão de acesso](perform-access-review.md). Este artigo descreve como Ver os resultados da revisão de acesso e aplicar os resultados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global, administrador de usuários, administrador de segurança ou leitor de segurança

Para obter mais informações, consulte [quais usuários devem ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Exibir uma revisão de acesso

Você pode acompanhar o progresso à medida que os revisores concluírem suas revisões.

1. Entre no portal do Azure e abra a [página governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **revisões de acesso**.

1. Na lista, clique em uma revisão de acesso.

    Para exibir uma série de revisões de acesso, navegue até a revisão de acesso e você encontrará ocorrências futuras nas revisões agendadas.

    Na página **visão geral** , você pode ver o progresso. Nenhum direito de acesso é alterado no diretório até que a revisão seja concluída.

    ![Progresso das revisões de acesso](./media/complete-access-review/overview-progress.png)

1. Se você quiser interromper uma revisão de acesso antes de atingir a data de término agendada, clique no botão **parar** .

    Ao interromper uma revisão, os revisores não poderão mais fornecer respostas. Não é possível reiniciar uma análise depois de ter sido interrompida.

1. Se você não estiver mais interessado na revisão de acesso, poderá excluí-la clicando no botão **excluir** .

## <a name="apply-the-changes"></a>Aplicar as alterações

Se a **aplicação automática de resultados ao recurso** tiver sido habilitada e baseada em suas seleções durante **as configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando você interromper manualmente a revisão.

Se a **aplicação automática de resultados no recurso** não foi habilitada para a revisão, clique em **aplicar** para aplicar manualmente as alterações. Se o acesso de um usuário foi negado na revisão, quando você clica em **aplicar**, o Azure ad remove sua associação ou atribuição de aplicativo.

![Aplicar alterações de revisão de acesso](./media/complete-access-review/apply-changes.png)

O status da revisão será alterado de **concluído** por meio de Estados intermediários  , como aplicando e finalmente ao resultado de estado **aplicado**. É necessário esperar que os usuários negados, se houver algum, sejam removidos da associação do grupo ou da atribuição do aplicativo em alguns minutos.

Uma revisão de aplicação automática configurada ou selecionar **Aplicar** não afeta um grupo originado em um diretório local ou em um grupo dinâmico. Se você quiser alterar um grupo que se origina localmente, baixe os resultados e aplique essas alterações para a representação do grupo neste diretório.

## <a name="retrieve-the-results"></a>Recuperar os resultados

Para exibir os resultados de uma revisão de acesso única, clique na página **resultados** . Para exibir apenas o acesso de um usuário, na caixa de pesquisa, digite o nome de exibição ou o nome principal de usuário de um usuário cujo acesso foi revisado.

![Recuperar resultados para uma revisão de acesso](./media/complete-access-review/retrieve-results.png)

Para exibir o progresso de uma revisão de acesso ativa que é recorrente, clique na página **resultados** .

Para exibir os resultados de uma instância concluída de uma revisão de acesso recorrente, clique em **examinar histórico**e selecione a instância específica na lista de instâncias de revisão de acesso concluídas, com base nas datas de início e de término da instância. Os resultados dessa instância podem ser obtidos na página **resultados** .

Para recuperar todos os resultados de uma revisão de acesso, clique no botão **baixar** . O arquivo CSV resultante pode ser visualizado no Excel ou em outros programas que abrem arquivos CSV codificados em UTF-8.

## <a name="remove-users-from-an-access-review"></a>Remover usuários de uma revisão de acesso

 Por padrão, um usuário excluído permanecerá excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador se necessário.  Após 30 dias, esse usuário será excluído permanentemente.  Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo.  Após um usuário ter sido excluído permanentemente, os dados posteriores sobre o usuário serão removidos das revisões de acesso ativas.  Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso do usuário com revisões de acesso ao Azure AD](manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso de convidado com revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
