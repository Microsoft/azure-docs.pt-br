---
title: Aprovar ou negar solicitações de acesso-gerenciamento de direitos do Azure AD
description: Saiba como usar o portal meu acesso para aprovar ou negar solicitações a um pacote do Access no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddb3b171e5a26273cb2e0045f11e3a4dbb48c5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655094"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Aprovar ou negar solicitações de acesso no gerenciamento de direitos do Azure AD

Com o gerenciamento de direitos do Azure AD, você pode configurar políticas para exigir aprovação para pacotes de acesso e escolher um ou mais aprovadores. Este artigo descreve como os aprovadores designados podem aprovar ou negar solicitações de pacotes do Access.

## <a name="open-request"></a>Abrir solicitação

A primeira etapa para aprovar ou negar solicitações de acesso é localizar e abrir a solicitação de acesso com aprovação pendente. Há duas maneiras de abrir a solicitação de acesso.

**Função de pré-requisito:** Aprovador

1. Procure um email de Microsoft Azure que solicita que você aprove ou negue uma solicitação. Aqui está um exemplo de email:

    ![Aprovar solicitação para acessar email de pacote](./media/entitlement-management-shared/approver-request-email.png)

1. Clique no link **aprovar ou negar solicitação** para abrir a solicitação de acesso.

1. Entre no portal meu acesso.

Se você não tiver o email, poderá encontrar as solicitações de acesso que aguardam sua aprovação seguindo estas etapas.

1. Entre no portal meu acesso em [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (Para o governo dos EUA, o domínio no link meu portal de acesso será `myaccess.microsoft.us` .)

1. No menu à esquerda, clique em **aprovações** para ver uma lista de solicitações de acesso com aprovação pendente.

1. Na guia **pendente** , localize a solicitação.

## <a name="view-requestors-answers-to-questions-preview"></a>Exibir respostas do solicitante para perguntas (versão prévia)

1. Navegue até a guia **aprovações** em meu acesso.

1. Vá para a solicitação que você gostaria de aprovar e clique em **detalhes**. Você também pode clicar em **aprovar** ou **negar** se estiver pronto para tomar uma decisão.

1. Clique em **detalhes da solicitação**.

    ![Meu Portal de acesso-solicitação de acesso – clique em detalhes da solicitação](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. As informações fornecidas pelo solicitante estarão na parte inferior do painel.

    ![Captura de tela mostra os detalhes da solicitação](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Com base nas informações fornecidas pelo solicitante, você pode aprovar ou negar a solicitação. Consulte as etapas em aprovar ou negar solicitação para obter diretrizes.

## <a name="approve-or-deny-request"></a>Aprovar ou negar solicitação

Depois de abrir uma solicitação de acesso com aprovação pendente, você poderá ver detalhes que o ajudarão a tomar uma decisão de aprovar ou negar.

**Função de pré-requisito:** Aprovador

1. Clique no link **Exibir** para abrir o painel Solicitação de acesso.

1. Clique em **detalhes** para ver detalhes sobre a solicitação de acesso.

    Os detalhes incluem o nome do usuário, organização, data de início e de término do acesso, se fornecido, justificativa de negócios, quando a solicitação foi enviada e quando a solicitação expirará.

1. Clique em **aprovar** ou **negar**.

1. Se necessário, insira um motivo.

    ![Captura de tela mostra a página onde você aceita ou nega a solicitação.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Clique em **Enviar** para enviar sua decisão.

    Se uma política estiver configurada com vários Aprovadores, apenas um aprovador precisa tomar uma decisão sobre a aprovação pendente. Depois que um Aprovador tiver enviado sua decisão para a solicitação de acesso, a solicitação será concluída e não estará mais disponível para os outros Aprovadores aprovarem ou negarem a solicitação. Os outros Aprovadores podem ver a decisão de solicitação e o tomador de decisão em seu portal meu acesso. Neste momento, há suporte apenas para aprovação de estágio único.

    Se nenhum dos aprovadores configurados for capaz de aprovar ou negar a solicitação de acesso, a solicitação expirará após a duração da solicitação configurada. O usuário é notificado de que sua solicitação de acesso expirou e que precisa enviar novamente a solicitação de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Solicitar notificações de processo e email](entitlement-management-process.md)
