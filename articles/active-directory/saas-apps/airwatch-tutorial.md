---
title: 'Tutorial: Integração do Azure Active Directory com AirWatch | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227694"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar o AirWatch ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o AirWatch ao Azure AD (Azure Active Directory). Quando você integrar o AirWatch ao Azure AD, será possível:

* Controlar no Azure AD quem terá acesso ao AirWatch.
* Permitir que seus usuários entrem automaticamente no AirWatch com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para SSO (logon único) do AirWatch.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O AirWatch dá suporte ao SSO iniciado por **SP**.

## <a name="adding-airwatch-from-the-gallery"></a>Adição do AirWatch da Galeria

Para configurar a integração do AirWatch com o Azure AD, você precisa adicionar o AirWatch à sua lista de aplicativos SaaS gerenciados a partir da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AirWatch** na caixa de pesquisa.
1. Selecione **AirWatch** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o AirWatch usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AirWatch.

Para configurar e testar o SSO do Azure AD com o AirWatch, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do AirWatch](#configure-airwatch-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Criar um usuário de teste do AirWatch](#create-airwatch-test-user)** – para ter um equivalente de Brenda Fernandes no AirWatch que esteja vinculado à representação de usuário no Azure AD.
5. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AirWatch**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite um valor como: `AirWatch`

    > [!NOTE]
    > Esse valor não é o real. Atualize esse valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente AirWatch](https://www.air-watch.com/company/contact-us/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo AirWatch espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME |  Atributo de Origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e selecione **Download** para baixar o XML de metadados e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o AirWatch**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configurar SSO do AirWatch

1. Em outra janela do navegador da Web, entre como um administrador em seu site da empresa AirWatch.

1. Na página de configurações. Selecione **Configurações > Enterprise Integration > Serviços de Diretório**.

   ![Configurações](./media/airwatch-tutorial/ic791921.png "Configurações")

1. Clique na guia **Usuário**, na caixa de texto **DN Base**, digite seu nome de domínio e clique em **Salvar**.

   ![Usuário](./media/airwatch-tutorial/ic791922.png "Usuário")

1. Clique na guia **Servidor** .

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Servidor")

1. Execute as seguintes etapas na seção **LDAP**:

    ![Carregar](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Para **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **Usar SAML para Autenticação**.

1. Na seção **SAML 2.0**, para carregar o certificado baixado, clique em **Carregar**.

    ![Upload](./media/airwatch-tutorial/ic791932.png "Upload")

1. Na seção **Solicitar** , realize as seguintes etapas:

    ![Solicitação](./media/airwatch-tutorial/ic791925.png "Solicitação")  

    a. Para **Tipo de Associação de Solicitação**, selecione **POST**.

    b. No portal do Azure, na página da caixa de diálogo **Configurar logon único no AirWatch**, copie o valor da **URL de Logon** e, em seguida, cole-o na caixa de texto **URL de Logon Único do Provedor de Identidade**.

    c. Para **Formato de NameID**, selecione **Endereço de Email**.

    d. Para **Segurança da Solicitação de Autenticação**, selecione **Nenhuma**.

    e. Clique em **Save** (Salvar).

1. Clique na guia **Usuário** novamente.

    ![Usuário](./media/airwatch-tutorial/ic791926.png "Usuário")

1. Na seção **Atributo** , realize as seguintes etapas:

    ![Atributo](./media/airwatch-tutorial/ic791927.png "Atributo")

    a. Na caixa de texto **Identificador de Objeto**, digite `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Na caixa de texto **Nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Na caixa de texto **Nome de Exibição**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao AirWatch.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **AirWatch**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-airwatch-test-user"></a>Criar um usuário de teste do AirWatch

Para permitir que os usuários do Azure AD se conectem ao AirWatch, eles devem ser provisionados no AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre como administrador em seu site da empresa do **AirWatch**.

2. No painel de navegação à esquerda, clique em **Contas** e em **Usuários**.
  
   ![Usuários](./media/airwatch-tutorial/ic791929.png "Usuários")

3. No menu **Usuários**, clique em **Exibição de Lista** e em **Adicionar > Adicionar Usuário**.
  
   ![Adicionar Usuário](./media/airwatch-tutorial/ic791930.png "Adicionar Usuário")

4. No diálogo **Adicionar/Editar Usuário** , realize as seguintes etapas:

   ![Adicionar Usuário](./media/airwatch-tutorial/ic791931.png "Adicionar Usuário")

   a. Digite **Nome**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome** e **Endereço de Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

   b. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do AirWatch ou as APIs fornecidas pelo AirWatch para provisionar as contas de usuário do AAD.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do AirWatch no Painel de Acesso, você deverá ser conectado automaticamente ao AirWatch para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
