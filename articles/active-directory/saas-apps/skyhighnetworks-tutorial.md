---
title: 'Tutorial: Integração do Azure Active Directory à Configuração de SSO do Azure AD do MVISION Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Configuração de SSO do Azure AD do MVISION Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 456348f5492af44274a37a6400fb8d95e9bd55a4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015138"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Tutorial: Integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure Active Directory

Neste tutorial, você aprenderá a integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD (Azure Active Directory). Ao integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso à Configuração de SSO do Azure AD do MVISION Cloud.
* Permitir que os usuários sejam conectados automaticamente à Configuração de SSO do Azure AD do MVISION Cloud com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura da Configuração de SSO do Azure AD do MVISION Cloud habilitada para SSO (logon único).


## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A Configuração de SSO do Azure AD do MVISION Cloud é compatível com SSO iniciado por **SP e IDP**
* Depois de configurar o Dropbox, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Como adicionar a Configuração de SSO do Azure AD do MVISION Cloud por meio da galeria

Para configurar a integração da Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD, você precisará adicionar a Configuração de SSO do Azure AD do MVISION Cloud por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Configuração de SSO do Azure AD do MVISION Cloud** na caixa de pesquisa.
1. Selecione **Configuração de SSO do Azure AD do MVISION Cloud** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com a Configuração de SSO do Azure AD do MVISION Cloud usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na Configuração de SSO do Azure AD do MVISION Cloud.

Para configurar e testar o SSO do Azure AD com a Configuração de SSO do Azure AD do MVISION Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO da Configuração de SSO do Azure AD do MVISION Cloud](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da Configuração de SSO do Azure AD do MVISION Cloud](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** – para ter um equivalente de Brenda Fernandes na Configuração de SSO do Azure AD do MVISION Cloud que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Datadog**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)


4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de domínio e URLs da Configuração de SSO do Azure AD do MVISION Cloud](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Definir a Configuração de SSO do Azure AD do MVISION Cloud**, copie as URLs apropriadas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure AD permitindo a ela acesso à Configuração de SSO do Azure AD do MVISION Cloud.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e **Configuração de SSO do Azure AD do MVISION Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Configuração de SSO do Azure AD do MVISION Cloud**.

    ![O link da Configuração de SSO do Azure AD do MVISION Cloud na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Configurar o SSO na Configuração de SSO do Azure AD do MVISION Cloud

Para configurar o logon único no lado da **Configuração de SSO do Azure AD do MVISION Cloud**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Criar um usuário de teste da Configuração de SSO do Azure AD do MVISION Cloud

Nesta seção, você criará um usuário chamado B.Fernandes na Configuração de SSO do Azure AD do MVISION Cloud. Trabalhe com a [equipe de suporte da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com) para adicionar os usuários na plataforma da Configuração de SSO do Azure AD do MVISION Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco da Configuração de SSO do Azure AD do MVISION Cloud no Painel de Acesso, você deverá ser conectado automaticamente à Configuração de SSO do Azure AD do MVISION Cloud para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a Configuração de SSO do Azure AD do MVISION Cloud com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)