---
title: "Tutorial: Integração do Azure Active Directory ao ScaleX Enterprise | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ScaleX Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 0ebed0c2605862426384c0e219e52c9d626b6246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutorial: Integração do Azure Active Directory ao ScaleX Enterprise

Neste tutorial, você aprende a integrar o ScaleX Enterprise ao Azure AD (Azure Active Directory).

A integração do ScaleX Enterprise ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ScaleX Enterprise
- É possível permitir que os usuários sejam automaticamente conectados ao ScaleX Enterprise (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte. O que é o acesso a aplicativos e logon único com o [Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ScaleX Enterprise, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do ScaleX Enterprise habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ScaleX Enterprise por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionando o ScaleX Enterprise por meio da galeria
Para configurar a integração do ScaleX Enterprise ao Azure AD, você precisa adicionar o ScaleX Enterprise à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ScaleX Enterprise por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **ScaleX Enterprise**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. No painel de resultados, selecione **ScaleX Enterprise** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o ScaleX Enterprise, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ScaleX Enterprise é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ScaleX Enterprise.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no ScaleX Enterprise.

Para configurar e testar o logon único do Azure AD com o ScaleX Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)** – para ter um equivalente de Brenda Fernandes no ScaleX Enterprise que está vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo ScaleX Enterprise.

**Para configurar o logon único do Azure AD com o ScaleX Enterprise, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **ScaleX Enterprise**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Na seção **Domínio e URLs do ScaleX Enterprise**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IdP**:

    ![Configurar o logon único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Marque **Mostrar configurações avançadas de URL**, se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar o logon único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com o Identificador, a URL de Resposta ou a URL de Logon real. Contate a [equipe de suporte ao Cliente do ScaleX Enterprise](http://info.rescale.com/contact_sales) para obter esses valores. 

5. O aplicativo ScaleX espera as declarações SAML em um formato específico, o que exige a modificação dos mapeamentos de atributo personalizado para sua configuração de atributos do token SAML. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** para abrir as configurações de atributo personalizado.

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Clique com o botão direito do mouse no atributo **name** e clique em Excluir.

    ![Configurar o logon único](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    b. Clique no atributo **emailaddress** para abrir a janela Editar Atributo. Altere o valor de **user.mail** para **user.userprincipalname** e clique em OK.

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)   
    
5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do Certificado no computador.

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do ScaleX Enterprise**, clique em **Configurar ScaleX Enterprise** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Para configurar o logon único no **ScaleX Enterprise**, faça logon no site da empresa ScaleX Enterprise como administrador.

9. Clique no menu na parte superior direita e selecione **Administração do Contoso**.

    > [!NOTE] 
    > Contoso é apenas um exemplo. Esse deve ser o Nome real de sua Empresa. 

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. Selecione **Integrações** no menu superior e, em seguida, **Logon Único**.

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. Preencha o formulário da seguinte maneira:

    ![Configurar Logon Único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Selecione **“Criar qualquer usuário que pode se autenticar com o SSO”.**

    b. **SAML do Provedor de Serviços**: cole o valor ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***

    c. **Nome do campo de email do Provedor de Identidade na resposta do ACS**: cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID de Entidade do EntityDescriptor do Provedor de Identidade:** cole o valor de **ID de Entidade SAML** copiado do portal do Azure.

    e. **URL do SingleSignOnService do Provedor de Identidade:** cole a **URL de Serviço de Logon Único do SAML** do portal do Azure.

    f. **Certificado X509 público do Provedor de Identidade:** abra o certificado X509 baixado do Azure no bloco de notas e cole o conteúdo nesta caixa. Verifique se não há quebras de linha no meio do conteúdo do certificado.
    
    g. Marque as seguintes caixas de seleção: **Habilitado, Criptografar NameID e Assinar AuthnRequests.**

    h. Clique em **Atualizar Configurações de SSO** para salvar as configurações.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Criando um usuário de teste do ScaleX Enterprise

Para permitir que os usuários do Azure AD façam logon no ScaleX Enterprise, eles devem ser provisionados no ScaleX Enterprise. No caso do ScaleX Enterprise, o provisionamento é uma tarefa automática e nenhuma etapa manual é necessária. Qualquer usuário que pode se autenticar com êxito com as credenciais de SSO será provisionado automaticamente no ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso do usuário ao ScaleX Enterprise.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ScaleX Enterprise, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ScaleX Enterprise**.

    ![Configurar o logon único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Clique no bloco ScaleX Enterprise no Painel de Acesso e você será automaticamente conectado ao aplicativo ScaleX Enterprise. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png

