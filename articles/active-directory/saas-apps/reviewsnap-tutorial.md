---
title: 'Tutorial: Integração do Azure Active Directory ao Reviewsnap | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Reviewsnap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b00fb373-2b31-4dcf-84ce-abc29e4c639c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f4983f2ba1567241a6dfc6231a4925805947cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60526612"
---
# <a name="tutorial-azure-active-directory-integration-with-reviewsnap"></a>Tutorial: Integração do Azure Active Directory ao Reviewsnap

Neste tutorial, você aprenderá a integrar o Reviewsnap ao Azure AD (Azure Active Directory).

A integração do Reviewsnap ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Reviewsnap.
- Você pode permitir que os usuários façam logon automaticamente no Reviewsnap (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Reviewsnap, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Reviewsnap

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Reviewsnap da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-reviewsnap-from-the-gallery"></a>Adicionando o Reviewsnap da galeria
Para configurar a integração do Reviewsnap no Azure AD, você precisa adicionar o Reviewsnap da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Reviewsnap da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Reviewsnap**, selecione **Reviewsnap** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Reviewsnap na lista de resultados](./media/reviewsnap-tutorial/tutorial_reviewsnap_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Reviewsnap com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Reviewsnap é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Reviewsnap.

Para configurar e testar o logon único do Azure AD com o Reviewsnap, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Reviewsnap](#create-a-reviewsnap-test-user)** – para ter um equivalente de Brenda Fernandes no Reviewsnap que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Reviewsnap.

**Para configurar o logon único do Azure AD com o Reviewsnap, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Reviewsnap**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/reviewsnap-tutorial/tutorial_reviewsnap_samlbase.png)

1. Na seção **Domínio e URLs do Reviewsnap**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do Reviewsnap](./media/reviewsnap-tutorial/tutorial_reviewsnap_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL: `https://app.reviewsnap.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.reviewsnap.com/auth/saml/callback?namespace=<CUSTOMER_NAMESPACE>`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Reviewsnap](./media/reviewsnap-tutorial/tutorial_reviewsnap_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.reviewsnap.com/login`
     
    > [!NOTE] 
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Entre em contato com a [equipe de suporte ao Cliente do Reviewsnap](mailto:support@reviewsnap.com) para obter o valor. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/reviewsnap-tutorial/tutorial_reviewsnap_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/reviewsnap-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do Reviewsnap**, clique em **Configurar Reviewsnap** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Reviewsnap](./media/reviewsnap-tutorial/tutorial_reviewsnap_configure.png) 

1. Para configurar o logon único no lado do **Reviewsnap**, é necessário enviar o **Certificado (Base64) baixado, a URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** para a [equipe de suporte do Reviewsnap](mailto:support@reviewsnap.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/reviewsnap-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/reviewsnap-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/reviewsnap-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/reviewsnap-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-reviewsnap-test-user"></a>Criar um usuário de teste do Reviewsnap

Nesta seção, você criará uma usuária chamada Britta simon no Reviewsnap. Trabalhe com a  [equipe de suporte do Reviewsnap](mailto:support@reviewsnap.com) para adicionar os usuários à plataforma Reviewsnap. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao Reviewsnap.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao Reviewsnap, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Reviewsnap**.

    ![O link do Reviewsnap na lista Aplicativos](./media/reviewsnap-tutorial/tutorial_reviewsnap_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Reviewsnap no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Reviewsnap.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/reviewsnap-tutorial/tutorial_general_01.png
[2]: ./media/reviewsnap-tutorial/tutorial_general_02.png
[3]: ./media/reviewsnap-tutorial/tutorial_general_03.png
[4]: ./media/reviewsnap-tutorial/tutorial_general_04.png

[100]: ./media/reviewsnap-tutorial/tutorial_general_100.png

[200]: ./media/reviewsnap-tutorial/tutorial_general_200.png
[201]: ./media/reviewsnap-tutorial/tutorial_general_201.png
[202]: ./media/reviewsnap-tutorial/tutorial_general_202.png
[203]: ./media/reviewsnap-tutorial/tutorial_general_203.png

