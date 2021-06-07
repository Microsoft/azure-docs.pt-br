---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PurelyHR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PurelyHR.
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
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515178"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PurelyHR

Neste tutorial, você aprenderá a integrar o PurelyHR ao Azure AD (Azure Active Directory). Ao integrar o PurelyHR ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao PurelyHR.
* Permitir que os usuários sejam conectados automaticamente ao PurelyHR com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do PurelyHR.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PurelyHR dá suporte ao SSO iniciado por **SP e IDP**
* O PurelyHR é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-purelyhr-from-the-gallery"></a>Adicionar o PurelyHR da galeria

Para configurar a integração do PurelyHR ao Azure AD, você precisará adicionar o PurelyHR da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **PurelyHR** na caixa de pesquisa.
1. Selecione **PurelyHR** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Configurar e testar o logon único do Azure AD para o PurelyHR

Configure e teste o SSO do Azure AD com o PurelyHR usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PurelyHR.

Para configurar e testar o SSO do Azure AD com o PurelyHR, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PurelyHR](#configure-purelyhr-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do PurelyHR](#create-purelyhr-test-user)** – para ter um equivalente de B.Fernandes no PurelyHR que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PurelyHR**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<companyID>.purelyhr.com/sso-consume`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte do cliente PurelyHR](https://support.purelyhr.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o PurelyHR**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao PurelyHR.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **PurelyHR**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-purelyhr-sso"></a>Configurar o SSO do PurelyHR

1. Para automatizar a configuração no PurelyHR, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o PurelyHR** direcionará você ao aplicativo PurelyHR. Nele, forneça as credenciais de administrador para entrar no PurelyHR. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o PurelyHR manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do PurelyHR como administrador e execute as seguintes etapas:

1. Abra o **Painel** das opções na barra de ferramentas e clique em **Configurações de SSO**.

1. Cole os valores nas caixas conforme descrito abaixo-

    ![Configurar o logon único](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Abra o **Certificate(Bas64)** baixado do Portal do Azure no bloco de notas e copie o valor do certificado. Cole o valor copiado na caixa **Certificado X.509**.

    b. Na caixa **URL do emissor Idp**, cole o **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa **URL de Ponto de Extremidade IdP**, cole a **URL de Logon** copiada do portal do Azure. 

    d. Verifique a caixa de seleção **Criação Automática de Usuários** para habilitar o provisionamento automático de usuários no PurelyHR.

    e. Clique em **Salvar Alterações** para salvar as configurações.

### <a name="create-purelyhr-test-user"></a>Criar um usuário de teste do PurelyHR

Normalmente, esta etapa não é necessária, pois o aplicativo dá suporte ao provisionamento de usuário na hora certa. Se o provisionamento automático de usuário não estiver habilitado, a criação manual do usuário poderá ser feita como descrito abaixo.

Entre no site da empresa do seu Velpic SAML como um administrador e execute as seguintes etapas:

1. Clique na guia Gerenciar, vá para a seção Usuários e clique no botão Novo para adicionar usuários.

    ![adicionar usuário](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página da caixa de diálogo **"Criar Novo Usuário"** , execute as etapas que se seguem.

    ![usuário](./media/velpicsaml-tutorial/velpic_8.png)

    a. Na caixa de texto **Nome**, digite o nome B.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome Fernandes.

    c. Na caixa de texto **Nome de Usuário**, digite o nome de usuário de B. Fernandes.

    d. Na caixa de texto **Email**, digite o endereço de email da conta B.Simon@contoso.com.

    e. O restante das informações é opcional, você pode preenchê-las se necessário.

    f. Clique em **SALVAR**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do PurelyHR no Painel de Acesso, você deverá ser conectado automaticamente ao PurelyHR com SSO configurado. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o PurelyHR com o Azure AD](https://aad.portal.azure.com/)