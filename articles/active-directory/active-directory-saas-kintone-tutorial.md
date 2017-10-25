---
title: "Tutorial: Integração do Azure Active Directory com o Kintone | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Kintone."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: e5e847c12cba3611ce7ea2c3e956dbd55b1e0cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: integração do Azure Active Directory com o Kintone

Neste tutorial, você aprenderá a integrar o Kintone ao Azure AD (Azure Active Directory).

A integração do Kintone ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Kintone
- Você pode permitir que usuários façam logon automaticamente no Kintone (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Kintone, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Kintone com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Kintone da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-kintone-from-the-gallery"></a>Adição do Kintone da galeria
Para configurar a integração do Kintone ao Azure AD, você precisará adicionar o Kintone da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kintone por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Kintone**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_search.png)

5. No painel de resultados, selecione **Kintone** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Kintone, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Kintone é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kintone.

No Kintone, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Kintone, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Kintone](#creating-a-kintone-test-user)** – para ter um equivalente de Brenda Fernandes no Kintone que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Kintone.

**Para configurar o logon único do Azure AD com o Kintone, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Kintone**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_samlbase.png)

3. Na seção **URLs e Domínio do Kintone**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.kintone.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Kintone](https://www.kintone.com/contact/) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Kintone**, clique em **Configurar Kintone** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_configure.png) 

7. Em outra janela do navegador da Web, faça logon no site da sua empresa do **Kintone** como administrador.

8. Clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-kintone-tutorial/ic785879.png "Configurações")

9. Clique em **Usuários e administração do sistema**.
   
    ![Administração do Sistema e de Usuários](./media/active-directory-saas-kintone-tutorial/ic785880.png "Administração do Sistema e de Usuários")

10. Em **Administração do Sistema \> Segurança**, clique em **Logon**.
   
    ![Logon](./media/active-directory-saas-kintone-tutorial/ic785881.png "Logon")

11. Selecione **Habilitar a autenticação do SAML**.
   
    ![Autenticação SAML](./media/active-directory-saas-kintone-tutorial/ic785882.png "Autenticação SAML")

12. Na seção Autenticação do SAML, execute as seguintes etapas:
    
    ![Autenticação SAML](./media/active-directory-saas-kintone-tutorial/ic785883.png "Autenticação SAML")
    
    a. Na caixa de texto **URL de Logon**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.
   
    b. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.
    
    c. Clique em **Pesquisar** para carregar o certificado que você baixou.
    
    d. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kintone-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kintone-test-user"></a>Criação de um usuário de teste do Kintone

Para permitir que os usuários do Azure AD façam logon no Kintone, eles devem ser provisionados no Kintone.  
No caso do Kintone, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **Kintone** como administrador.

2. Clique em **Configuração**.
   
    ![Configurações](./media/active-directory-saas-kintone-tutorial/ic785879.png "Configurações")

3. Clique em **Usuários e administração do sistema**.
   
    ![Administração do Sistema e de Usuários](./media/active-directory-saas-kintone-tutorial/ic785880.png "Administração do Sistema e de Usuários")

4. Em **Administração de Usuários**, clique em **Departamentos e Usuários**.
   
    ![Departamento e Usuários](./media/active-directory-saas-kintone-tutorial/ic785888.png "Departamento e Usuários")

5. Clique em **Novo Usuário**.
   
    ![Novos Usuários](./media/active-directory-saas-kintone-tutorial/ic785889.png "novos Usuários")

6. Na seção **Novo Usuário** , realize as seguintes etapas:
   
    ![Novos Usuários](./media/active-directory-saas-kintone-tutorial/ic785890.png "novos Usuários")
   
    a. Digite um **Nome de Exibição**, **Nome de Logon**, **Nova Senha**, **Confirmar Senha**, **Endereço de Email** e outros detalhes de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
 
    b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Kintone ou APIs fornecidas pelo Kintone para provisionar as contas de usuário do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Kintone.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Kintone, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Kintone**.

    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Kintone no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo do Kintone.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_203.png

