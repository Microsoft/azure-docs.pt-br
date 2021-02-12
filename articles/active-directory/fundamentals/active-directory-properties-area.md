---
title: Adicionar informações de privacidade de sua organização – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar informações de privacidade de sua organização à área de Propriedades do Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996739"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Adicionar informações de privacidade de sua organização usando o Azure Active Directory
Este artigo explica como um administrador de locatário pode adicionar informações relacionadas à privacidade ao locatário do Azure Active Directory (Azure AD) de uma organização, por meio do portal do Azure.

É altamente recomendável que você adicione o contato de privacidade global e a política de privacidade de sua organização para que seus funcionários internos e convidados externos possam examinar suas políticas. Como as declarações de privacidade são exclusivamente criadas e personalizadas para cada empresa, é altamente recomendável que você entre em contato com um advogado para obter assistência.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adicionar suas informações de privacidade no Azure AD
Você adiciona informações de privacidade da sua organização na área **Propriedades** do Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Para acessar a área de Propriedades e adicionar suas informações de privacidade

1. Entre no portal do Azure como um administrador de locatário.

2. Na barra de navegação à esquerda, selecione **Azure Active Directory** e, em seguida, selecione **Propriedades**.

    A área **Propriedades** é exibida.

    ![A área de Propriedades do Azure AD realçando a área de informações de privacidade](media/active-directory-properties-area/properties-area.png)

3. Adicione as informações de privacidade de seus funcionários:

    - **Contato técnico.** Digite o endereço de email da pessoa de contato para suporte técnico dentro de sua organização.
    
    - **Contato de privacidade global.** Digite o endereço de email da pessoa de contato para consultas sobre a privacidade dos dados pessoais. Essa pessoa é também quem a Microsoft contatará se houver uma violação de segurança. Se não houver nenhuma pessoa listada aqui, a Microsoft entrará em contato com seus administradores globais.

    - **URL da política de privacidade.** Digite o link para o documento da sua organização que descreve como sua organização lida com a privacidade dos dados do convidado interno e externo.

        >[!Important]
        >Se você não incluir sua própria política de privacidade ou seu contato de privacidade, seus convidados externos verão o texto na caixa de **permissões de revisão** que diz, **< _o nome da sua organização_> não forneceu links para seus termos para você examinar**. Por exemplo, um usuário convidado verá essa mensagem quando receber um convite para acessar uma organização por meio de colaboração B2B.

        ![Caixa Permissões de Revisão de Colaboração B2B com mensagem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
- [Resgate do convite de colaboração do Azure Active Directory B2B](../external-identities/redemption-experience.md)
- [Adicionar ou alterar as informações de perfil de um usuário no Azure Active Directory](active-directory-users-profile-azure-portal.md)