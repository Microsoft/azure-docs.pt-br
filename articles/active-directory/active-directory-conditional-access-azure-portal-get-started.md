---
title: "Introdução ao acesso condicional no Azure Active Directory | Microsoft Docs"
description: "Teste o acesso condicional usando uma condição de local."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cd53e8be32d1e98aaf9f72177895871dba69df86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Introdução ao acesso condicional no Azure Active Directory

O acesso condicional é um recurso do Azure Active Directory que permite que você defina as condições sob as quais os usuários autorizados possam acessar seus aplicativos. 

Este tópico fornece instruções para testar um acesso condicional com base em uma condição de local em seu ambiente.  


## <a name="scenario-description"></a>Descrição do cenário

É um requisito comum em muitas organizações só exigir autenticação multifator para acesso a aplicativos que não é executado desde a intranet corporativa. Com o Azure Active Directory, você pode facilmente atingir essa meta, configurando uma política de acesso condicional com base no local. Este tópico fornece instruções detalhadas sobre como configurar uma política relacionada. A política aproveita os [IPs confiáveis](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) para distinguir entre tentativas de acesso de rede da intranet e todos os outros locais.


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tópico pressupõe que você esteja familiarizado com os conceitos descritos em [Acesso condicional do Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Para testar este cenário, você precisa:

- Criar um usuário de teste 

- Atribuir uma licença do Azure AD Premium ao usuário de teste

- Configurar um aplicativo gerenciado e atribuir o usuário de teste a ele

- Configurar IPs confiáveis

Se você precisar de mais detalhes sobre IPs confiáveis, veja [IPs confiáveis](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Etapas de configuração de política

**Para configurar a política de acesso condicional, execute:**

1. No portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**. 

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. Na folha **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Na folha **Acesso Condicional**, para abrir a folha **Novo**, na barra de ferramentas na parte superior, clique em **Adicionar**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Na folha **Novo**, na caixa de texto **Nome**, digite um nome para a política.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. Na seção **Atribuição**, clique em **Usuários e Grupos**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Na folha **Usuários e grupos**, execute as seguintes etapas:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Clique em **Selecionar usuários e grupos**.

    b. Clique em **Selecionar**.

    c. Na folha **Selecionar**, selecione seu usuário de teste e clique em **Selecionar**.

    d. Na folha **Usuários e grupos**, clique em **Concluído**.

7. Na folha **Novo**, para abrir a folha **Aplicativos de nuvem**, na seção **Atribuição**, clique em **Aplicativos de nuvem**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na folha **Aplicativos de nuvem**, execute as seguintes etapas:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Clique em **Selecionar aplicativos**.

    b. Clique em **Selecionar**.

    c. Na folha **Selecionar**, selecione seu aplicativo em nuvem e, em seguida, clique em **Selecionar**.

    d. Na folha **Aplicativos de nuvem**, clique em **Concluído**.

9. Na folha **Novo**, para abrir a folha **Condições**, na seção **Atribuição**, clique em **Condições**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. Na folha **Condições**, para abrir a folha **Locais**, clique em **Locais**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Na folha **Locais**, execute as seguintes etapas:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. Em **Configurar**, clique em **Sim**.

    b. Em **Incluir**, clique em **Todos os locais**.

    c. Clique em **Excluir** e clique em **Todos os IPs confiáveis**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Clique em **Concluído**.

12. Na folha **Condições**, clique em **Concluído**.

13. Na folha **Novo**, para abrir a folha **Conceder**, na seção **Controles**, clique em **Conceder**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Na folha **Conceder**, execute as seguintes etapas:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selecione **Exigir autenticação multifator**.

    b. Clique em **Selecionar**.

15. Na folha **Novo**, em **Habilitar política**, clique em **Ativar**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Na folha **Novo**, clique em **Criar**.


## <a name="testing-the-policy"></a>Testar a política

Para testar sua política, você deve acessar o aplicativo de um dispositivo que: 

1. Tenha um endereço IP que esteja dentro de seus IPs Confiáveis configurados 

1. Tenha um endereço IP que não esteja dentro de seus IPs Confiáveis configurados

A autenticação multifator só será necessária durante uma tentativa de conexão feita de um dispositivo que não esteja dentro dos seus IPs Confiáveis configurados. 


## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre o acesso condicional, veja [Acesso condicional do Azure Active Directory](active-directory-conditional-access-azure-portal.md).

