---
title: Criar uma revisão de acesso de um pacote de acesso no gerenciamento de direitos do Azure AD
description: Saiba como criar uma política de revisão de acesso para pacotes de acesso de gerenciamento de direitos em Azure Active Directory revisões de acesso (versão prévia).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798504"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Criar uma revisão de acesso de um pacote de acesso no gerenciamento de direitos do Azure AD

Para reduzir o risco de acesso obsoleto, você deve habilitar revisões periódicas de usuários que têm atribuições ativas para um pacote de acesso no gerenciamento de direitos do Azure AD. Você pode habilitar revisões ao criar um novo pacote do Access ou editar um pacote existente do Access. Este artigo descreve como habilitar as revisões de acesso de pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar revisões de pacotes de acesso, você deve atender aos pré-requisitos para criar um pacote de acesso:
- Azure AD Premium P2
- Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

Para obter mais informações, veja [Requisitos de licença](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Criar uma revisão de acesso de um pacote de acesso

Você pode habilitar as revisões de acesso ao [criar um novo pacote de acesso](entitlement-management-access-package-create.md) ou [Editar uma política de pacote do Access existente](entitlement-management-access-package-lifecycle-policy.md) . Siga estas etapas para habilitar as revisões de acesso de um pacote de acesso:

1. Abra a guia **ciclo de vida** de um pacote do Access e role para baixo até revisões de **acesso**.

1. Mova a alternância **exigir revisões de acesso** para **Sim**.

    ![Adicionar a revisão de acesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Especifique a data em que as revisões serão iniciadas ao lado de **Iniciar em**.

1. Em seguida, defina a **frequência de revisão** como **anual**, **bi-anual**, **trimestral** ou **mensal**.
Essa configuração determina a frequência com que as revisões de acesso ocorrerão.

1. Defina a **duração** para definir quantos dias cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, você pode agendar uma revisão anual que começa em 1º de Janeiro e está aberta para revisão por 30 dias para que os revisores tenham até o fim do mês para responder.

1. Ao lado dos **revisores**, selecione **autorevisão** se desejar que os usuários executem sua própria revisão de acesso ou selecione **revisores específicos** se quiser designar um revisor.

    ![Selecione Adicionar revisores](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se você selecionou **revisores específicos**, especifique quais usuários farão a revisão de acesso:
    1. Selecione **Adicionar revisores**.
    1. No painel **selecionar revisores** , pesquise e selecione os usuários que você deseja que sejam revisores.
    1. Depois de selecionar os revisores, clique no botão **selecionar** .

    ![Especificar os revisores](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Clique em **examinar + criar** se você estiver criando um novo pacote de acesso ou **Atualizar** se estiver editando um pacote do Access, na parte inferior da página.

## <a name="view-the-status-of-the-access-review"></a>Exibir o status da revisão de acesso

Após a data de início, uma revisão de acesso será listada na seção **revisões de acesso** . Siga estas etapas para exibir o status de uma revisão de acesso:

1. Em **governança de identidade**, clique em **pacotes de acesso** e selecione o pacote de acesso com o status de revisão de acesso que você deseja verificar.   

1. Quando estiver na visão geral do pacote de acesso, clique em **revisões de acesso** no menu à esquerda.
    
    ![Selecionar revisões de acesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Será exibida uma lista que contém todas as políticas que têm revisões de acesso associadas a elas. Clique na revisão para ver seu relatório.

    ![Lista de revisões de acesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando você exibe o relatório, ele mostra o número de usuários revisados e as ações tomadas pelo revisor neles.

    ![Exibir status da revisão](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Notificações de acesso de revisões de email
Você pode designar revisores ou os usuários podem revisar seus próprios acessos. Por padrão, o Azure AD enviará um email aos revisores ou aos revisores logo após o início da revisão.

O email incluirá instruções sobre como examinar o acesso aos pacotes do Access. Se a revisão for para os usuários revisarem seu acesso, mostre-lhes as instruções sobre como executar uma autorevisão de seus pacotes de acesso.
  
Se você tiver atribuído usuários convidados como revisores e eles não tiverem aceitado seu convite de convidado do Azure AD, eles não receberão emails das revisões de acesso do Azure AD. Eles devem primeiro aceitar o convite e criar uma conta com o Azure AD antes que possam receber os emails. 

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso de pacotes de acesso](entitlement-management-access-reviews-review-access.md)
- [Revisão automática de pacotes do Access](entitlement-management-access-reviews-self-review.md)
