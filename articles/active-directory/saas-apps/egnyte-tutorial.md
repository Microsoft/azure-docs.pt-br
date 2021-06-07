---
title: 'Tutorial: Integração do Azure Active Directory ao Egnyte | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 3d102b492326971ef186ba50a557ad8d1df5b6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454185"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Egnyte

Neste tutorial, você aprenderá a integrar o Egnyte ao Azure AD (Azure Active Directory). Ao integrar o Egnyte ao Azure AD, você poderá:

* Controlar quem tem acesso ao Egnyte no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Egnyte com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Egnyte.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Egnyte dá suporte ao SSO iniciado por **SP**
* Após configurar o Egnyte, você poderá impor o Controle de sessão, que protege contra a exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da galeria

Para configurar a integração do Egnyte ao Azure AD, você precisará adicionar o Egnyte da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Egnyte** na caixa de pesquisa.
1. Selecione **Egnyte** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Egnyte, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Egnyte.

Para configurar e testar o logon único do Azure AD com o Egnyte, você precisa concluir os seguintes blocos de construção:

Para configurar e testar o SSO do Azure AD com o Egnyte, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Egnyte](#configure-egnyte-sso)** – para definir configurar o logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Egnyte](#create-egnyte-test-user)** – para ter um equivalente de B.Fernandes no Egnyte que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Egnyte**, localize a seção **Gerenciar** e escolha **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio único do Egnyte Domain e URLs](common/sp-signonurl.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.egnyte.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize-os com as URLs de Logon e de Resposta. Contate a [equipe de suporte ao cliente do Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

5. Na seção **Configurar o Egnyte**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso ao Egnyte.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Egnyte**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-egnyte-sso"></a>Configurar o SSO do Egnyte

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Egnyte como administrador.

2. Clique em **Configurações**.
   
    ![Configurações 1](./media/egnyte-tutorial/ic787819.png "Configurações")

3. No menu, clique em **Configurações**.

    ![Configurações](./media/egnyte-tutorial/ic787820.png "Configurações")

4. Clique na guia **Configuração** e, depois, em **Segurança**.

    ![Segurança](./media/egnyte-tutorial/ic787821.png "Segurança")

5. Na seção **Autenticação do Logon Único**, faça o seguinte:

    ![Autenticação de Logon Único](./media/egnyte-tutorial/ic787822.png "Autenticação de Logon Único")   
    
    a. Em **Autenticação do logon único**, selecione **SAML 2.0**.
   
    b. Como **Provedor de identidade**, selecione **AzureAD**.
   
    c. Cole o **URL de login** copiado do portal do Azure na caixa de texto **URL de login do provedor de identidade**.
   
    d. Cole o **Identificador do Azure AD** que você copiou do portal do Azure na caixa de texto **Identidade da entidade do provedor de identidade**.
      
    e. Abra seu certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade**.
   
    f. Como **Mapeamento de usuário padrão**, selecione **Endereço de email**.
   
    g. Como **Usar o valor do emissor especificado pelo domínio**, selecione **desabilitado**.
   
    h. Clique em **Save** (Salvar).

### <a name="create-egnyte-test-user"></a>Criar um usuário de teste do Egnyte

Para permitir que os usuários do Azure AD entrem no Egnyte, eles deverão ser provisionados no Egnyte. No caso do Egnyte, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre o site da empresa do **Egnyte** como administrador.

2. Vá para **Configurações \> Usuários e Grupos**.

3. Clique em **Adicionar Novo Usuário** e, em seguida, selecione o tipo de usuário que você deseja adicionar.
   
    ![Usuários](./media/egnyte-tutorial/ic787824.png "Usuários")

4. Na seção **Novo Usuário Avançado**, execute as seguintes etapas:
    
    ![Novo Usuário Padrão](./media/egnyte-tutorial/ic787825.png "Novo Usuário Padrão")   

    a. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    b. Na caixa de texto **Username**, insira o nome de usuário como **Brittasimon**.

    c. Selecione **logon único** como **tipo de autenticação**.
   
    d. Clique em **Save** (Salvar).
    
    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email de notificação.
    >

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Egnyte ou as APIs fornecidas pelo Egnyte para provisionar as contas de usuário do Azure AD.
>

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Egnyte no Painel de Acesso, você deverá ser conectado automaticamente ao Egnyte, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)