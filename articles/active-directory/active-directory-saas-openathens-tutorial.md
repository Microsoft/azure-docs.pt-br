---
title: "Tutorial: Integração do Azure Active Directory com o OpenAthens | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 2819e300d94a0bc3b0900419218561fc846effcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: integração do Azure Active Directory com o OpenAthens

Neste tutorial, você aprende a integrar o OpenAthens ao Azure AD (Azure Active Directory).

A integração do OpenAthens ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao OpenAthens.
- Você pode permitir que os usuários façam logon automaticamente no OpenAthens (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o OpenAthens, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do OpenAthens

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o OpenAthens da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-openathens-from-the-gallery"></a>Adicionar o OpenAthens da galeria
Para configurar a integração do OpenAthens ao Azure AD, você precisará adicionar o OpenAthens da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpenAthens a partir da galeria**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Vá para **Aplicativos empresariais** e, em seguida, vá para **Todos os aplicativos**.

    ![O painel “Aplicativos empresariais”][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **OpenAthens**, selecione **OpenAthens** no painel de resultados e, em seguida, selecione o botão **Adicionar**.

    ![OpenAthens na lista de resultados](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OpenAthens, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do OpenAthens é equivalente ao usuário do Azure AD. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OpenAthens.

No OpenAthens, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o OpenAthens, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on), para habilitar os seus usuários a usarem esse recurso.
2. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do OpenAthens](#create-a-openathens-test-user), para ter um equivalente de Brenda Fernandes no OpenAthens vinculado à representação do usuário no Azure AD.
4. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on), para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo OpenAthens.

**Para configurar o logon único do AD do Azure com OpenAthens**

1. No portal do Azure, na página de integração de aplicativos do **OpenAthens**, selecione **Logon único**.

    ![Configurar o link do logon único][4]

2. Para habilitar o logon único, na caixa de diálogo **Logon único**, selecione **Logon único baseado em SAML** como o **Modo**.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. Na seção **Domínio e URLs do OpenAthens**, insira o valor `https://login.openathens.net/saml/2/metadata-sp` na caixa de texto **Identificador**.

    ![Informações de logon único de Domínio e URLs do OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. Na seção **Certificado de autenticação SAML**, selecione **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado de autenticação SAML](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Selecione o botão **Salvar**.

    ![O botão de Salvar logon único](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. Em outra janela do navegador da Web, faça logon em seu site de empresa do OpenAthens como administrador.

7. Selecione **Conexões** na lista na guia **Gerenciamento**. 

    ![Configurar o logon único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Selecione **SAML 1.1/2.0**e, em seguida, selecione o botão **Configurar**.

    ![Configurar o logon único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Para adicionar a configuração, selecione o botão **Procurar** para carregar o arquivo .xml de metadados que você baixou do portal do Azure e, em seguida, selecione **Adicionar**.

    ![Configurar o logon único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Execute as seguintes etapas na guia **Detalhes**.

    ![Configurar o logon único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. Em **Mapeamento do nome de exibição**, selecione **Usar atributo**.

    b. Na caixa de texto **Exibir atributo do nome**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Em **Mapeamento de usuário exclusivo**, selecione **Usar atributo**.

    d. Na caixa de texto **Atributo de usuário exclusivo**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Em **Status**, marque todas as três caixas de seleção.

    f. Em **Criar contas locais**, selecione **automaticamente**.

    g. Selecione **Salvar alterações**.

> [!TIP]
> Agora, é possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo. Depois de adicionar esse aplicativo a partir da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração**, na parte inferior. Para saber mais informações sobre o recurso de documentação inserida, consulte [Documentação inserida do Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado “Brenda Fernandes”.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD**

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. Na caixa de texto **Nome**, digite **BrendaFernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o endereço de email de Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa de texto **Senha**.

    d. Selecione **Criar**.
  
### <a name="create-an-openathens-test-user"></a>Criar um usuário de teste do OpenAthens

O OpenAthens oferece suporte ao provisionamento Just in Time, e os usuários são criados automaticamente após a autenticação bem-sucedida. Você não precisa realizar qualquer ação nesta seção.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao OpenAthens.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao OpenAthens**

1. No portal do Azure, abra o modo de exibição de aplicativos, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de **Aplicativos**, escolha **OpenAthens**.

    ![O link do OpenAthens na lista de Aplicativos](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e Grupos** no painel **Adicionar atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na lista **Usuários e grupos**, selecione **Brenda Fernandes**.

6. Selecione o botão **Selecionar** na lista **Usuários e grupos**.

7. Selecione o botão **Atribuir** no painel **Adicionar atribuição**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco **OpenAthens** no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo OpenAthens.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* Para obter uma lista de tutoriais como integrar aplicativos SaaS com o Azure Active Directory, consulte os [tutoriais de integração de aplicativos SaaS para uso com o Azure AD](active-directory-saas-tutorial-list.md).
* Para obter mais informações sobre o acesso de aplicativos e logon único com o Azure Active Directory, consulte [O que é o acesso de aplicativo e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

