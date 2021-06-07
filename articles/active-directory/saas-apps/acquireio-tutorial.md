---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AcquireIO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a AcquireIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 5fe070bc1abe0592b3082c597c1812781335448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673172"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AcquireIO

Neste tutorial, você aprenderá a integrar a AcquireIO ao Azure AD (Azure Active Directory). Ao integrar a AcquireIO ao Azure AD, é possível:

* Controlar quem tem acesso à AcquireIO no Azure AD.
* Permitir que seus usuários entrem automaticamente na AcquireIO com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da AcquireIO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AcquireIO dá suporte ao SSO iniciado por **IDP**

## <a name="adding-acquireio-from-the-gallery"></a>Adicionando a AcquireIO da galeria

Para configurar a integração da AcquireIO ao Azure AD, você precisará adicionar a AcquireIO por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AcquireIO** na caixa de pesquisa.
1. Selecione **AcquireIO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Configurar e testar o logon único do Azure AD para o AcquireIO

Configure e teste o SSO do Azure AD na AcquireIO com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na AcquireIO.

Para configurar e testar o SSO do Azure AD na AcquireIO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AcquireIO](#configure-acquireio-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do AcquireIO](#create-acquireio-test-user)** – para ter um equivalente de B.Fernandes no AcquireIO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AcquireIO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Você receberá a URL de resposta real, que será explicada posteriormente na seção **Configurar a AcquireIO** do tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar a AcquireIO**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso à AcquireIO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AcquireIO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-acquireio-sso"></a>Configurar o SSO do AcquireIO

1. Para automatizar a configuração no AcquireIO, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, clique em **Configurar o AcquireIO**, que direcionará você ao aplicativo AcquireIO. Nele, forneça as credenciais de administrador para entrar no AcquireIO. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o AcquireIO manualmente, em outra janela do navegador da Web, entre no AcquireIO como administrador.

1. Do lado esquerdo do menu, clique em **App Store**.

    ![Captura de tela que realça App Store.](./media/acquireio-tutorial/config01.png)

1. Role a página para baixo até **Active Directory** e clique em **Instalar**.

    ![Captura de tela que realça a seção Active Directory e o botão Instalar.](./media/acquireio-tutorial/config02.png)

1. No pop-up do Active Directory, execute as seguintes etapas:

    ![Captura de tela que mostra a tela Active Directory.](./media/acquireio-tutorial/config03.png)

    a. Clique para **Copiar** a URL de resposta para sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o certificado codificado em Base64 no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado X.509**.

    d. Clique em **Conectar Agora**.

### <a name="create-acquireio-test-user"></a>Criar usuário de teste da AcquireIO

Para permitir que os usuários do Microsoft Azure AD façam logon na AcquireIO, eles devem estar provisionados na AcquireIO. Na AcquireIO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela diferente do navegador da Web, entre na AcquireIO como administrador.

1. No lado esquerdo do menu, clique em **Perfis** e navegue até **Adicionar Perfil**.

    ![Captura de tela que realça os Perfis no menu no lado esquerdo da tela, bem como a opção Adicionar Perfil.](./media/acquireio-tutorial/config04.png)

1. No pop-up **Adicionar cliente**, execute as seguintes etapas:

    ![Configuração da AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário, por exemplo, **B.Fernandes**.

    b. Na caixa de texto **E-mail**, insira o e-mail do usuário como **B.simon@contoso.com** .

    c. Clique em **Enviar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do AcquireIO no Painel de Acesso, você deverá ser conectado automaticamente ao AcquireIO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o AcquireIO com o Azure AD](https://aad.portal.azure.com/)