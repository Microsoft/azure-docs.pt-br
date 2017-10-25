---
title: "Atribuir licenças para o Azure MFA | Microsoft Docs"
description: "Saiba como atribuir as licenças do usuário para a Microsoft Azure Multi-Factor Authentication"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 45522bf526c4aeab1d6ccc8891a55a0436ff9320
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Atribuindo uma licença do Azure MFA, do Azure AD Premium ou do Enterprise Mobility aos usuários
Se você tiver comprado licenças do Azure MFA, do Azure AD Premium ou do Enterprise Mobility Suite, não precisará criar um provedor Multi-Factor Auth. Depois de atribuir as licenças para seus usuários, você poderá começar a ativá-los para a MFA.

## <a name="to-assign-a-license"></a>Para atribuir uma licença
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do Active Directory, clique duas vezes no diretório que tenha os usuários que você deseja habilitar.
4. Na parte superior da página do diretório, selecione **Licenças**.
   ![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Na página de licenças, selecione **Autenticação Multifator do Azure**, **Active Directory Premium** ou **Enterprise Mobility Suite**.  Se você tiver apenas uma, então, ela deverá ser selecionada automaticamente.
6. Na parte inferior da página, clique em **Atribuir**.
   ![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. Na caixa que aparece, clique ao lado dos usuários ou grupos aos quais você deseja atribuir licenças.  Você deverá ver uma marca de seleção verde aparecer.
8. Clique no ícone da marca de seleção para salvar as alterações.
   ![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. Você deverá ver uma mensagem informando quantas licenças foram atribuídas e quantas podem ter falhado.  Clique em **Ok**.
   ![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais, veja [O que é o licenciamento do Microsoft Azure Active Directory?](../active-directory/active-directory-licensing-what-is.md)
