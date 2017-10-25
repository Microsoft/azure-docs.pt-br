---
title: "Tutorial: Integração do Azure Active Directory ao IdeaScale | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o IdeaScale."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 88099e942319f16dd721da83e4e69b8fcb836c0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: integração do Active Directory do Azure ao IdeaScale

Neste tutorial, você aprenderá a integrar o IdeaScale ao Azure AD (Azure Active Directory).

A integração do IdeaScale ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao IdeaScale
- Você pode permitir que seus usuários façam logon automaticamente no IdeaScale (Logon Único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao IdeaScale, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do IdeaScale

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando IdeaScale da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-ideascale-from-the-gallery"></a>Adicionando IdeaScale da galeria
Para configurar a integração do IdeaScale ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o IdeaScale da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **IdeaScale**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_search.png)

5. No painel de resultados, selecione **IdeaScale** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o IdeaScale com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do IdeaScale é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do IdeaScale.

No IdeaScale, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o IdeaScale, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Como criar um usuário de teste do IdeaScale](#creating-an-ideascale-test-user)** – para ter um equivalente de Brenda Fernandes no IdeaScale que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo IdeaScale.

**Para configurar o logon único do Azure AD com o IdeaScale, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **IdeaScale**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. Na seção **URLs e Domínio do IdeaScale**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.ideascale.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente do IdeaScale](http://support.ideascale.com/) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-ideascale-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do IdeaScale**, clique em **Configurar IdeaScale** para abrir a janela **Configurar logon**. Copie o **URL de logout e a ID da Entidade do SAML** da **seção de Referência Rápida**.

    ![Configurar Logon Único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_configure.png) 

7. Em outra janela do navegador da Web, faça logon no site da empresa do IdeaScale como administrador.

8. Vá para **Configurações da Comunidade**.
   
    ![Configurações da Comunidade](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Configurações da Comunidade")

9. Vá para **Segurança \> Configurações de Logon Único**.
   
    ![Configurações de Logon Único](./media/active-directory-saas-ideascale-tutorial/ic790848.png "Configurações de Logon Único")

10. Para **Tipo de Logon Único**, selecione **SAML 2.0**.
   
    ![Tipo de Logon Único](./media/active-directory-saas-ideascale-tutorial/ic790849.png "Tipo de Logon Único")

11. No diálogo **Configurações de Logon Único** , realize as seguintes etapas:
   
    ![Configurações de Logon Único](./media/active-directory-saas-ideascale-tutorial/ic790850.png "Configurações de Logon Único")
   
    a. Na caixa de texto **ID da Entidade do IdP do SAML**, cole o valor da **ID da Entidade do SAML** copiado do portal do Azure.

    b. Copie o conteúdo do arquivo de metadados baixado do portal do Azure e cole-o na caixa de texto **Metadados do IdP do SAML**.

    c. Na caixa de texto **URL de Sucesso do Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ideascale-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-ideascale-test-user"></a>Como criar um usuário de teste do IdeaScale

Para permitir que os usuários do Azure AD façam logon no IdeaScale, eles deverão ser provisionados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **IdeaScale** como administrador.

2. Vá para **Configurações da Comunidade**.
   
    ![Configurações da Comunidade](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Configurações da Comunidade")

3. Vá para **Configurações Básicas \> Gerenciamento de Membros**.

4. Clique em **Adicionar Membro**.
   
    ![Gerenciamento de Membros](./media/active-directory-saas-ideascale-tutorial/ic790852.png "Gerenciamento de Membros")

5. Na seção Adicionar Novo Membro, execute as seguintes etapas:
   
    ![Adicionar Novo Membro](./media/active-directory-saas-ideascale-tutorial/ic790853.png "Adicionar Novo Membro")
   
    a. Na caixa de texto **Endereços de Email** , digite o endereço de email de uma conta de AAD válida que você deseja provisionar.
   
    b. Clique em **Salvar Alterações**. 
   
    >[!NOTE]
    >O titular da conta do Azure Active Directory recebe um email com um link para confirmar a conta antes que ela se torne ativa.
      
>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do IdeaScale ou as APIs fornecidas pelo IdeaScale para provisionar as contas de usuário do AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao IdeaScale.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao IdeaScale, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **IdeaScale**.

    ![Configurar Logon Único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único


O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IdeaScale no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo IdeaScale.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_203.png

