---
title: 'Tutorial: Integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 1e303485a03edcd9ba3d3e7380aa4c7ae8b1a4b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092613"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com a Área Restrita Salesforce

Neste tutorial, você aprende a integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory).

As áreas restritas oferecem a capacidade de criar várias cópias da sua organização em ambientes separados por uma variedade de finalidades, como desenvolvimento, testes e treinamento, sem comprometer os dados e os aplicativos em sua organização de produção do Salesforce.
Para obter mais detalhes, confira [Visão Geral da Área Restrita](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

A integração do Salesforce Sandbox ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso à Área Restrita Salesforce.
* Você pode permitir que seus usuários entrem automaticamente no Salesforce Sandbox (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce Sandbox, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Salesforce Sandbox habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Salesforce Sandbox dá suporte ao SSO iniciado por **SP e IDP**
* O Salesforce Sandbox é compatível com o provisionamento de usuário **Just In Time**
* O Salesforce Sandbox é compatível com o [provisionamento de usuário **Automatizado**](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria

Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Salesforce Sandbox por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Área Restrita Salesforce**, selecione **Área Restrita Salesforce** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Área Restrita Salesforce na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com a Área Restrita Salesforce, com base em um usuário de teste chamado **Brenda Fernandes**.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce Sandbox é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Para configurar e testar o logon único do Azure AD com o Salesforce Sandbox, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Salesforce Sandbox](#configure-salesforce-sandbox-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Salesforce Sandbox](#create-salesforce-sandbox-test-user)** – para ter um equivalente de Brenda Fernandes no Salesforce Sandbox vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Salesforce Sandbox, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Salesforce Sandbox**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, se você tiver um **Arquivo de metadados do Provedor de Serviço** e quiser configurar o modo iniciado por **IDP**, execute as etapas a seguir:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços no portal de administração do Salesforce Sandbox, explicado posteriormente no tutorial.

    c. Depois que o arquivo de metadados for enviado, o **URL de resposta** será preenchido automaticamente na caixa de texto **URL de resposta**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o valor da **URL de Resposta** não for preenchido automaticamente, preencha-o manualmente de acordo com seus requisitos.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Salesforce Sandbox**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Configurar o logon único do Salesforce Sandbox

1. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

2. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

3. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **Configurações de logon único do SAML**, os campos são preenchidos automaticamente, clique em Salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **Configurações de Logon Único**, clique no botão **Baixar Metadados** para baixar o arquivo de metadados do provedor de serviços. Usar esse arquivo a **básicas de configuração de SAML** seção no portal do Azure para configurar as URLs necessárias, conforme explicado acima.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se você desejar configurar o aplicativo no modo iniciado **SP**, os pré-requisitos disso estão a seguir:

    a. Você deve ter um domínio verificado.

    b. Você precisa configurar e habilitar seu domínio na Área Restrita do Salesforce, as etapas para isso serão explicadas posteriormente neste tutorial.

    c. No portal do Azure, sobre o **básicas de configuração de SAML** seção, clique em **definir URLs adicionais** e execute a seguinte etapa:
  
    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](common/both-signonurl.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Esse valor deve ser copiado do portal da Área Restrita Salesforce depois que você tiver habilitado o domínio.

11. Sobre o **certificado de autenticação SAML** seção, clique em **XML de metadados de Federação** e, em seguida, salve o arquivo xml em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

12. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

13. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

14. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique em **Escolher arquivo** para carregar o arquivo XML de metadados e clique em **criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página **Configurações do SAML Single Sign-On**, em que os campos são preenchidos automaticamente, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*), na caixa de texto **Nome** e clique em salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para habilitar seu domínio na Área Restrita Salesforce, execute as etapas a seguir:

    > [!NOTE]
    > Antes de habilitar o domínio, você precisará criar o mesmo na Área Restrita Salesforce. Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois que o domínio for criado, certifique-se de que ele esteja configurado corretamente.

21. No painel de navegação à esquerda na Área Restrita Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na seção **Configuração de Autenticação**, clique em **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na seção **Configuração de Autenticação**, como **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único do SAML que você definiu durante a configuração de SSO na Área Restrita Salesforce e clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Salesforce Sandbox.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Salesforce Sandbox**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Salesforce Sandbox**.

    ![O link da Área Restrita Salesforce na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-salesforce-sandbox-test-user"></a>Criar um usuário de teste do Salesforce Sandbox

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox. A área restrita do Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-sandbox-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático de usuário.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Salesforce Sandbox no Painel de Acesso, você deverá ser conectado automaticamente ao Salesforce Sandbox no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Configurar Provisionamento de Usuário](salesforce-sandbox-provisioning-tutorial.md)
