---
title: 'Tutorial: Integração do Azure Active Directory ao TOPdesk – Secure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TOPdesk – Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eded8eb446d36a321acf46231eee3e764ba41504
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088443"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Azure Active Directory ao TOPdesk – Secure

Neste tutorial, você aprenderá a integrar o TOPdesk – Secure ao Azure AD (Azure Active Directory).
A integração do TOPdesk – Secure ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TOPdesk – Secure.
* Você pode permitir que os usuários sejam conectados automaticamente ao TOPdesk – Secure (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TOPdesk – Secure, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do TOPdesk – Secure

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TOPdesk – Secure dá suporte ao SSO iniciado por **SP**

## <a name="adding-topdesk---secure-from-the-gallery"></a>Adicionar o TOPdesk – Secure da galeria

Para configurar a integração do TOPdesk – Secure ao Azure AD, é necessário adicionar o TOPdesk – Secure da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o TOPdesk – Secure da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TOPdesk – Secure**, selecione **TOPdesk – Secure** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![TOPdesk – Secure na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TOPdesk – Secure, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TOPdesk – Secure.

Para configurar e testar o logon único do Azure AD com o TOPdesk – Secure, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do TOPdesk – Secure](#configure-topdesk---secure-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do TOPdesk – Secure](#create-topdesk---secure-test-user)** – para ter um equivalente de Brenda Fernandes no TOPdesk – Secure que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TOPdesk – Secure, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TOPdesk – Secure**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do TOPdesk – Secure](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do TOPdesk – Secure](https://www.topdesk.com/us/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o TOPdesk – Secure**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-topdesk---secure-single-sign-on"></a>Configurar o Logon Único do TOPdesk – Secure

1. Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.

2. No menu **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/topdesk-secure-tutorial/ic790598.png "Configurações")

3. Clique em **Configurações de Logon**.

    ![Configurações de logon](./media/topdesk-secure-tutorial/ic790599.png "Configurações de logon")

4. Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na seção **Seguro** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:

    ![Configurações técnicas](./media/topdesk-secure-tutorial/ic790855.png "Configurações técnicas")

    a. Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.

    b. Abra o arquivo de metadados e localize o nó **AssertionConsumerService** .

    ![Serviço de Declaração do Consumidor](./media/topdesk-secure-tutorial/ic790856.png "Serviço de Declaração do Consumidor")

    c. Copie o valor **AssertionConsumerService**, cole-o na caixa de texto URL de resposta na seção **Domínio e URLs do TOPdesk – Secure**.

6. Execute as seguintes etapas para criar um arquivo de certificado:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o arquivo de metadados baixado do Portal do Azure.

    b. Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificate** .

    d. Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.

7. Na seção **Público**, clique em **Adicionar**.

    ![Adicionar](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página do diálogo **Assistente de configuração do SAML** , realize as seguintes etapas:

    ![Assistente de configuração SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de configuração SAML")

    a. Para carregar o arquivo de metadados baixado do Portal do Azure, em **Metadados de Federação**, clique em **Procurar**.

    b. Para carregar o arquivo de certificado, em **Certificado (RSA)** , clique em **Procurar**.

    c. Para **chave privada (RSA, PKCS8, DER)** , você pode carregar sua própria chave privada ou contatar [TOPdesk - equipe de suporte do Secure Client](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.

    e. Na caixa de texto **Atributo de nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na caixa de texto **Nome de exibição** , digite um nome para a sua configuração.

    g. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo-lhe acesso ao TOPdesk – Secure.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **TOPdesk – Secure**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **TOPdesk – Secure**.

    ![O link do TOPdesk – Secure na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-topdesk---secure-test-user"></a>Criar um usuário de teste do TOPdesk – Secure

Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Secure, eles deverão ser provisionados no TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> Novo \> Arquivos de Suporte \> Operador**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. No diálogo **Novo Operador** , realize as seguintes etapas:

    ![Novo operador](./media/topdesk-secure-tutorial/ic790611.png "Novo operador")

    a. Clique na guia **Geral**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Simon**.

    c. Selecione um **Site** para a conta na seção **Local**.

    d. Na caixa de texto **Nome de Logon** da seção **Logon no TOPdesk**, digite um nome de logon para o usuário.

    e. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk - Secure ou APIs fornecidas pelo TOPdesk - Secure para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TOPdesk – Secure no Painel de Acesso, você deverá ser conectado automaticamente ao TOPdesk – Secure, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

