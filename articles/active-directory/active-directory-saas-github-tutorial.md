---
title: "Tutorial: Integração do Azure Active Directory ao GitHub | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o GitHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Azure Active Directory ao GitHub

Neste tutorial, você aprenderá a integrar o GitHub ao Azure AD (Azure Active Directory).

A integração do GitHub ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao GitHub
- Você pode permitir que usuários façam logon automaticamente no GitHub usando logon único com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do GitHub habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o GitHub da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-github-from-the-gallery"></a>Adicionar o GitHub da galeria
Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GitHub por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **GitHub.com**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. No painel de resultados, selecione **GitHub** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o GitHub, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do GitHub é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GitHub.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no GitHub.

Para configurar e testar o logon único do Azure AD com o GitHub, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do GitHub](#creating-a-GitHub-test-user)** - para ter um equivalente de Brenda Fernandes no GitHub que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal de Gerenciamento do Azure e configura o logon único em seu aplicativo GitHub.

**Para configurar o logon único do Azure AD com o GitHub, execute as seguintes etapas:**

1. No portal de Gerenciamento do Azure, na página de integração do aplicativo **GitHub**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Na seção **URLs e Domínio do GitHub**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. Na caixa de texto **URL de Logon**, digite o valor como: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Logon e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Acesse a seção Administração do GitHub para recuperar esses valores. 

4. Na seção **Atributos do Usuário**, selecione **Identificador de Usuário** como user.mail.

    ![Configurar Logon Único](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. Na caixa de diálogo **Criar um Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Na seção **Certificado de Autenticação SAML**, selecione **Ativar o novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. Na seção **Configuração do GitHub**, clique em **Configurar o GitHub** para abrir a janela **Configurar logon**.

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Configurar o logon único](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. Em outra janela do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

12. Navegue até **Configurações** e clique em **Segurança**

    ![Configurações](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Marque a caixa **Habilitar autenticação SAML**, revelando os campos de configuração de Logon Único. Em seguida, use o valor de URL de logon único para atualizar a URL de Logon Único na configuração do Azure AD.

    ![Configurações](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Configure os seguintes campos:

    a. **URL de Logon**: insira **URL de Serviço de logon único de SAML** na seção **Configurar o GitHub** no Azure AD

    b. **Emissor**: insira **ID da Entidade SAML** na seção **Configurar GitHub** do Azure AD

    c. **Certificado Público**: abra o certificado baixado no Azure AD em um bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE"

    ![Configurações](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Clique em **Testar configuração de SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Configurações](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Clique em **Salvar**

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 


### <a name="creating-a-github-test-user"></a>Criar um usuário de teste do GitHub

Para permitir que os usuários do Azure AD façam logon no GitHub, eles deverão ser provisionados no GitHub.  
No caso do GitHub, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **Pessoas**.

    ![Pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "Pessoas")

3. Clique em **Convidar membro**.

    ![Convidar Usuários](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "Convidar Usuários")

4. Na caixa de diálogo **Convidar membro**, execute as seguintes etapas:

    a. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    ![Convidar Pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "Convidar Pessoas")
    
    b. Clique em **Enviar Convite**.

    ![Convidar Pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "Convidar Pessoas")

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao GitHub.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao GitHub, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **GitHub.com**.

    ![Configurar Logon Único](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GitHub no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo GitHub. Você entrará como uma conta de Organização, mas precisará fazer logon com sua conta pessoal.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
