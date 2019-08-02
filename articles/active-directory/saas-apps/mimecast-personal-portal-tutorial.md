---
title: 'Tutorial: Integração do Azure Active Directory com Mimecast Personal Portal | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfafa1157619e151f97fcf9c8a410a0644354b80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097392"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integração do Azure Active Directory com Mimecast Personal Portal

Neste tutorial, você aprende a integrar o Mimecast Personal Portal ao Azure AD (Azure Active Directory).
A integração do Mimecast Personal Portal ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Mimecast Personal Portal.
* É possível permitir que os usuários se conectem automaticamente ao Mimecast Personal Portal (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mimecast Personal Portal, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Mimecast Personal Portal com logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mimecast Personal Portal é compatível com SSO iniciado por **SP**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adicionando o Mimecast Personal Portal por meio da galeria

Para configurar a integração do Mimecast Personal Portal ao Azure AD, é necessário adicionar o Mimecast Personal Portal à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Mimecast Personal Portal por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Mimecast Personal Portal**, selecione **Mimecast Personal Portal** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Mimecast Personal Portal na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Mimecast Personal Portal com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mimecast Personal Portal.

Para configurar e testar o logon único do Azure AD com o Mimecast Personal Portal, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** – para ter um equivalente de Brenda Fernandes no Mimecast Personal Portal que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Mimecast Personal Portal, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Mimecast Personal Portal**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Mimecast Personal Portal](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Exterior        | `https://jer-api.mimecast.com/login/saml`|

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Exterior        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Na caixa de texto **URL de Resposta**, digite uma URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Exterior        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contate a [equipe de suporte ao Cliente do Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Mimecast Personal Portal**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configurar o logon único do Mimecast Personal Portal

1. Em outra janela do navegador da Web, faça logon em seu Mimecast Personal Portal como um administrador.

2. Acesse **Serviços \> Aplicativos**.
   
    ![Aplicativos](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplicativos")

3. Clique em **Perfis de Autenticação**.
   
    ![Perfis de Autenticação](./media/mimecast-personal-portal-tutorial/ic794999.png "Perfis de Autenticação")

4. Clique em **Novo Perfil de Autenticação**.
   
    ![Novo Perfil de Autenticação](./media/mimecast-personal-portal-tutorial/ic795000.png "Novo Perfil de Autenticação")

5. Na seção **Perfil de Autenticação** , realize as seguintes etapas:
   
    ![Perfil de Autenticação](./media/mimecast-personal-portal-tutorial/ic795001.png "Perfil de Autenticação")
   
    a. Na caixa de texto **Descrição** , digite um nome para a sua configuração.
   
    b. Selecione **Impor Autenticação SAML para o Mimecast Personal Portal**.
   
    c. Como **Provedor**, selecione **Azure Active Directory**.
   
    d. Na caixa de texto **URL do Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.
   
    e. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.
   
    f. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    g. Abra o certificado codificado em **Base64** baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado do Provedor de Identidade (Metadados)** .

    h. Selecione **Permitir Logon Único**.
   
    i. Clique em **Save** (Salvar).

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Mimecast Personal Portal.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Mimecast Personal Portal**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Mimecast Personal Portal**.

    ![Link do Mimecast Personal Portal na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-mimecast-personal-portal-test-user"></a>Criar um usuário de teste do Mimecast Personal Portal

Para permitir que os usuários do AD do Azure façam logon no Mimecast Personal Portal, eles devem ser provisionados no Mimecast Personal Portal. No caso do Mimecast Personal Portal, o provisionamento é uma tarefa manual.

Você precisa registrar um domínio antes de criar usuários.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon no **Mimecast Personal Portal** como administrador.

2. Vá para **Diretórios \> Interno**.
   
    ![Diretórios](./media/mimecast-personal-portal-tutorial/ic795003.png "Diretórios")

3. Clique em **Registrar Novo Domínio**.
   
    ![Registrar Novo Domínio](./media/mimecast-personal-portal-tutorial/ic795004.png "Registrar Novo Domínio")

4. Depois de criar o novo domínio, clique em **Novo Endereço**.
   
    ![Novo Endereço](./media/mimecast-personal-portal-tutorial/ic795005.png "Novo Endereço")

5. Na caixa de diálogo Novo endereço, realize as seguintes etapas de uma conta válida do Azure AD que você deseja provisionar:
   
    ![Salvar](./media/mimecast-personal-portal-tutorial/ic795006.png "Salvar")
   
    a. Na caixa de texto **Endereço de Email**, digite o **Endereço de Email** do usuário como **BrendaFernandes\@contoso.com**.
    
    b. Na caixa de texto **Nome Global**, digite o **nome de usuário** como **BrendaFernandes**.

    c. Nas caixas de texto **Senha** e **Confirmar Senha**, digite a **Senha** do usuário.
   
    b. Clique em **Save** (Salvar).

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Mimecast Personal Portal ou as APIs fornecidas pelo Mimecast Personal Portal para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Mimecast Personal Portal no Painel de Acesso, você deverá ser conectado automaticamente ao Mimecast Personal Portal para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

