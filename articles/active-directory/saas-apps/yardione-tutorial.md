---
title: 'Tutorial: Integração do Azure Active Directory ao YardiOne | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o YardiOne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 508957f6-caa5-4234-a7f3-90015937e4eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22abdcc004f3b353042d84cf01070fde7650367b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629372"
---
# <a name="tutorial-azure-active-directory-integration-with-yardione"></a>Tutorial: Integração do Azure Active Directory ao YardiOne

Neste tutorial, você aprenderá a integrar o YardiOne ao Microsoft Azure AD (Azure Active Directory).

A integração do YardiOne com o Azure Active Directory fornece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem tem acesso ao YardiOne.
- Você pode permitir que seus usuários façam logon automaticamente no YardiOne (logon único) com as contas do Azure Active Directory deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory ao YardiOne, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do YardiOne

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o YardiOne da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-yardione-from-the-gallery"></a>Adicionando o YardiOne da galeria
Para configurar a integração do YardiOne ao Azure Active Directory, você precisará adicionar o YardiOne à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o YardiOne por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **YardiOne**, selecione **YardiOne** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![YardiOne na lista de resultados](./media/yardione-tutorial/tutorial_yardione_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o YardiOne, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do YardiOne é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no YardiOne.

Para configurar e testar o logon único do Azure Active Directory com o YardiOne, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar de um usuário de teste do YardiOne](#create-a-yardione-test-user)** – para ter um equivalente de Brenda Fernandes no Fuse vinculado à representação do usuário no Azure Active Directory.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo portal YardiOne.

**Para configurar o logon único do Azure Active Directory com o YardiOne, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **YardiOne**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/yardione-tutorial/tutorial_yardione_samlbase.png)

1. Na seção **URLs e Domínio do YardiOne**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do YardiOne](./media/yardione-tutorial/tutorial_yardione_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<y1-subdomain>.yardione.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://<y1-subdomain>.yardione.com/yAuth2/trust`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente YardiOne](https://clientcentral.yardi.com) para obter esses valores.
     
1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas. 

    ![O link de download do Certificado](./media/yardione-tutorial/tutorial_yardione_certificate.png) 
 
1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/yardione-tutorial/tutorial_general_400.png)

1. Para configurar logon único no **YardiOne**, é necessário enviar a **URL de Metadados de Federação de Aplicativos** para a [Equipe de suporte do YardiOne](https://clientcentral.yardi.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/yardione-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/yardione-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/yardione-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/yardione-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-yardione-test-user"></a>Criar um usuário de teste do YardiOne

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no YardiOne. O YardiOne dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o YardiOne, se ele ainda não existir.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a  [equipe de suporte do YardiOne](https://clientcentral.yardi.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao YardiOne.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao YardiOne, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **YardiOne**.

    ![O link do YardiOne na lista de Aplicativos](./media/yardione-tutorial/tutorial_yardione_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do YardiOne no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo YardiOne.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yardione-tutorial/tutorial_general_01.png
[2]: ./media/yardione-tutorial/tutorial_general_02.png
[3]: ./media/yardione-tutorial/tutorial_general_03.png
[4]: ./media/yardione-tutorial/tutorial_general_04.png

[100]: ./media/yardione-tutorial/tutorial_general_100.png

[200]: ./media/yardione-tutorial/tutorial_general_200.png
[201]: ./media/yardione-tutorial/tutorial_general_201.png
[202]: ./media/yardione-tutorial/tutorial_general_202.png
[203]: ./media/yardione-tutorial/tutorial_general_203.png

