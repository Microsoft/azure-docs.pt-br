---
title: "Tutorial: Integração do Azure Active Directory com o SpringCM | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SpringCM."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: edfd06a06c730597fee4569ca1ce29092b45244a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Azure Active Directory com o SpringCM

Neste tutorial, você aprenderá a integrar o SpringCM ao Azure AD (Azure Active Directory).

A integração do SpringCM ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SpringCM
- Você pode permitir que os usuários façam logon automaticamente no SpringCM (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SpringCM, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SpringCM

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SpringCM da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-springcm-from-the-gallery"></a>Adicionar o SpringCM da galeria
Para configurar a integração do SpringCM ao Azure AD, você precisará adicionar o SpringCM da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SpringCM da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **SpringCM**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_search.png)

5. No painel de resultados, selecione **SpringCM** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SpringCM com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SpringCM é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SpringCM.

No SpringCM, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SpringCM, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar de um usuário de teste do SpringCM](#creating-a-springcm-test-user)** – para ter um equivalente de Brenda Fernandes no SpringCM que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo SpringCM.

**Para configurar o logon único do Azure AD com o SpringCM, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SpringCM**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. Na seção **Domínio e URLs do SpringCM**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do SpringCM](https://knowledge.springcm.com/support) para obter esse valor. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do SpringCM**, clique em **Configurar o SpringCM** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_configure.png)   

7. Em outra janela do navegador da Web, entre em seu site de empresa do **SpringCM** como administrador.

8. No menu na parte superior, clique em **IR PARA**, clique em **Preferências** e, na seção **Preferências da Conta**, clique em **SSO do SAML**.
   
    ![SSO do SAML](./media/active-directory-saas-spring-cm-tutorial/ic797051.png "SSO do SAML")

9. Na seção Configuração do Provedor de Identidade, execute as seguintes etapas:
   
    ![Configuração do Provedor de Identidade](./media/active-directory-saas-spring-cm-tutorial/ic797052.png "Configuração do Provedor de Identidade")
    
    a. Para carregar seu certificado baixado do Azure Active Directory, clique em **Selecionar Certificado do Emissor** ou **Alterar Certificado do Emissor**.
    
    b. Cole o valor de **ID de Entidade do SAML** copiado no Portal do Azure na caixa de texto **Emissor**.
    
    c. Cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure na caixa de texto **Ponto de Extremidade Iniciado do Provedor de Serviço (SP)**.
            
    d. Selecione **SAML Habilitado** como **Habilitar**.

    e. Clique em **Salvar**.
 
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-springcm-test-user"></a>Criar um usuário de teste do SpringCM

Para permitir que os usuários do Azure Active Directory façam logon no SpringCM, eles deverão ser provisionados no SpringCM. No caso do SpringCM, o provisionamento será uma tarefa manual.

>[!NOTE]
>Para obter mais informações, veja [Criar e editar um usuário do SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Para provisionar uma conta de usuário no SpringCM, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **SpringCM** como administrador.

2. Clique em **IR PARA** e depois em **CATÁLOGO DE ENDEREÇOS**.
   
    ![Criar usuário](./media/active-directory-saas-spring-cm-tutorial/ic797054.png "Criar usuário")

3. Clique em **Criar Usuário**.

4. Selecione uma **Função de Usuário**.

5. Selecione **Enviar Email de Ativação**.

6. Digite o nome, o sobrenome e o endereço de email de uma conta de usuário válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

7. Adicione o usuário a um **Grupo de segurança**.

8. Clique em **Salvar**.

  >[!NOTE]
  >É possível usar qualquer outra ferramenta de criação da conta de usuário do SpringCM ou as APIs fornecidas pelo SpringCM para provisionar as contas de usuário do AAD.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SpringCM.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SpringCM, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SpringCM**.

    ![Configurar Logon Único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.
 
Ao clicar no bloco do SpringCM no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SpringCM.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_203.png

