---
title: Aprovar ou negar solicitações de acesso no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como usar o portal meu acesso para aprovar ou negar solicitações a um pacote do Access no gerenciamento de direitos Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78243f02cbbe3d06b94ee52d6193865dbfa27121
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488996"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Aprovar ou negar solicitações de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com o gerenciamento de direitos do Azure AD, você pode configurar políticas para exigir aprovação para pacotes de acesso e escolher um ou mais aprovadores. Este artigo descreve como os aprovadores designados podem aprovar ou negar solicitações de pacotes do Access.

## <a name="open-request"></a>Abrir solicitação

A primeira etapa para aprovar ou negar solicitações de acesso é localizar e abrir a solicitação de acesso com aprovação pendente. Há duas maneiras de abrir a solicitação de acesso.

**Função de pré-requisito:** Aprovador

1. Procure um email de Microsoft Azure que solicita que você aprove ou negue uma solicitação. Aqui está um exemplo de email:

    ![Aprovar solicitação para acessar email de pacote](./media/entitlement-management-shared/email-approve-request.png)

1. Clique no link **aprovar ou negar solicitação** para abrir a solicitação de acesso.

1. Entre no portal meu acesso.

Se você não tiver o email, poderá encontrar as solicitações de acesso que aguardam sua aprovação seguindo estas etapas.

1. Entre no portal meu acesso em [https://myaccess.microsoft.com](https://myaccess.microsoft.com).

1. No menu à esquerda, clique  em aprovações para ver uma lista de solicitações de acesso com aprovação pendente.

1. Na guia **pendente** , localize a solicitação.

## <a name="approve-or-deny-request"></a>Aprovar ou negar solicitação

Depois de abrir uma solicitação de acesso com aprovação pendente, você poderá ver detalhes que o ajudarão a tomar uma decisão de aprovar ou negar.

**Função de pré-requisito:** Aprovador

1. Clique no link **Exibir** para abrir o painel Solicitação de acesso.

1. Clique em **detalhes** para ver detalhes sobre a solicitação de acesso.

    Os detalhes incluem o nome do usuário, organização, data de início e de término do acesso, se fornecido, justificativa de negócios, quando a solicitação foi enviada e quando a solicitação expirará.

1. Clique em **aprovar** ou **negar**.

1. Se necessário, insira um motivo.

    ![Portal Meus Acessos – Solicitação de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **Enviar** para enviar sua decisão.

    Se uma política estiver configurada com vários Aprovadores, apenas um aprovador precisa tomar uma decisão sobre a aprovação pendente. Depois que um Aprovador tiver enviado sua decisão para a solicitação de acesso, a solicitação será concluída e não estará mais disponível para os outros Aprovadores aprovarem ou negarem a solicitação. Os outros Aprovadores podem ver a decisão de solicitação e o tomador de decisão em seu portal meu acesso. Neste momento, há suporte apenas para aprovação de estágio único.

    Se nenhum dos aprovadores configurados for capaz de aprovar ou negar a solicitação de acesso, a solicitação expirará após a duração da solicitação configurada. O usuário é notificado de que sua solicitação de acesso expirou e que precisa enviar novamente a solicitação de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Solicitar notificações de processo e email](entitlement-management-process.md)
