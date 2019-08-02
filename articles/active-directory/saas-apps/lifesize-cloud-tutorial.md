---
title: 'Tutorial: Integração do Azure Active Directory ao Lifesize Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Lifesize Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f751eb9dfb8ef65bea80993ddbd3a682b1d47111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098052"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Azure Active Directory ao Lifesize Cloud

Neste tutorial, você aprenderá a integrar o Lifesize Cloud ao Azure AD (Azure Active Directory).
A integração do Lifesize Cloud ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Lifesize Cloud.
* Você pode permitir que os usuários sejam conectados automaticamente ao Lifesize Cloud (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Lifesize Cloud, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Lifesize Cloud

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Lifesize Cloud dá suporte ao SSO iniciado por **SP**

* O Lifesize Cloud dá suporte ao provisionamento de usuário **Automatizado**

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adição do Lifesize Cloud da galeria

Para configurar a integração do Lifesize Cloud ao Azure AD, você precisará adicionar o Lifesize Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Lifesize Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Lifesize Cloud**, selecione **Lifesize Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Lifesize Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Lifesize Cloud, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lifesize Cloud.

Para configurar e testar o logon único do Azure AD com o Lifesize Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Lifesize Cloud](#configure-lifesize-cloud-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Lifesize Cloud](#create-lifesize-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no Lifesize Cloud que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Lifesize Cloud, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Lifesize Cloud**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Lifesize Cloud](common/sp-identifier-relay.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/ls/?acs`

    b. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/<companyname>`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon real, o Identificador e o Estado de Retransmissão. Contate a [equipe de suporte ao Cliente do Lifesize Cloud](https://www.lifesize.com/en/support) para obter a URL de Logon e os valores de Identificador. Obtenha o valor do Estado de Retransmissão na Configuração de SSO explicada mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Lifesize Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-lifesize-cloud-single-sign-on"></a>Configurar o Logon Único do Lifesize Cloud

1. Para configurar o SSO para seu aplicativo, faça logon no aplicativo Lifesize Cloud com privilégios de administrador.

2. No canto superior direito, clique em seu nome e, em seguida, clique em **Configurações Avançadas**.

    ![Configurar o logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Nas configurações de adiantamento agora, clique em de **configuração de SSO** link. Isso abrirá a página de configuração de SSO para sua instância.

    ![Configurar o logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Agora, configure os seguintes valores na interface do usuário de configuração de SSO.

    ![Configurar o logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure.

    b.  Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra seu certificado codificado em base 64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**.
  
    d. Nos mapeamentos de Atributo do SAML para a caixa de texto Nome, insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Nos mapeamentos de Atributo do SAML para a caixa de texto **Sobrenome**, insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Nos mapeamentos de Atributo do SAML para a caixa de texto **Email**, insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Para verificar a configuração, você pode clicar no botão **Testar**.

    >[!NOTE]
    >Para realizar um teste bem-sucedido, você precisa concluir o assistente de configuração no Azure AD e também fornecer acesso a usuários ou grupos que possam executar o teste.

6. Habilite o SSO ao marcar o botão **Habilitar SSO**.

7. Agora, clique no botão **Atualizar** para que todas as configurações sejam salvas. Isso irá gerar o valor de RelayState. Copie o valor de RelayState, que é gerado na caixa de texto, cole-o na caixa de texto **Estado de Retransmissão** na seção **URLs e Domínio do Lifesize Cloud**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Lifesize Cloud.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Lifesize Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Lifesize Cloud**.

    ![O link do Lifesize Cloud na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-lifesize-cloud-test-user"></a>Criar um usuário de teste do Lifesize Cloud

Nesta seção, você criará um usuário chamado Brenda Fernandes no Lifesize Cloud. O Lifesize Cloud oferece suporte ao provisionamento automático de usuário. Após a autenticação bem-sucedida no Azure AD, o usuário será provisionado no aplicativo automaticamente.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Lifesize Cloud no Painel de Acesso, você deverá ver a página de logon do aplicativo Lifesize Cloud. Aqui, você precisa inserir seu nome de usuário e, depois disso, você será redirecionado para a home page do aplicativo.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
