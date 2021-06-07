---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ScaleX Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ScaleX Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: fecb7381786c36b077d329f68ca48ab10f65a984
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ScaleX Enterprise

Neste tutorial, você aprenderá a integrar o ScaleX Enterprise ao Azure AD (Azure Active Directory). Ao integrar o ScaleX Enterprise ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao ScaleX Enterprise.
* Permitir que os usuários sejam conectados automaticamente no ScaleX Enterprise com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ScaleX Enterprise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ScaleX Enterprise dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionando o ScaleX Enterprise por meio da galeria

Para configurar a integração do ScaleX Enterprise ao Azure AD, você precisará adicionar o ScaleX Enterprise por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ScaleX Enterprise** na caixa de pesquisa.
1. Selecione **ScaleX Enterprise** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Configurar e testar o logon único do Azure AD para o ScaleX Enterprise

Configure e teste o SSO do Azure AD com o ScaleX Enterprise usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ScaleX Enterprise.

Para configurar e testar o SSO do Azure AD com o ScaleX Enterprise, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ScaleX Enterprise](#configure-scalex-enterprise-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do ScaleX Enterprise](#create-scalex-enterprise-test-user)** – para ter um equivalente de B.Fernandes no ScaleX Enterprise que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ScaleX Enterprise**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do ScaleX Enterprise](https://about.rescale.com/contactus.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo ScaleX Enterprise espera as declarações do SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado para a configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **emailaddress** é mapeado com **user.mail**. O aplicativo ScaleX Enterprise espera que **emailaddress** seja mapeado com **user.userprincipalname**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterando-o.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o ScaleX Enterprise**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao ScaleX Enterprise.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ScaleX Enterprise**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-scalex-enterprise-sso"></a>Configurar o SSO do ScaleX Enterprise

1. Para automatizar a configuração no ScaleX Enterprise, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o ScaleX Enterprise** direcionará você ao aplicativo ScaleX Enterprise. Nele, forneça as credenciais de administrador para entrar no ScaleX Enterprise. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o ScaleX Enterprise manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do ScaleX Enterprise como administrador e execute as seguintes etapas:

1. Clique no menu na parte superior direita e selecione **Administração do Contoso**.

    > [!NOTE]
    > Contoso é apenas um exemplo. Esse deve ser o Nome real de sua Empresa.

    ![Captura de tela que mostra um exemplo de nome de empresa selecionado no menu no canto superior direito.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecione **Integrações** no menu superior e, em seguida, **Logon único**.

    ![Captura de tela que mostra a opção "Integrações" selecionada e a opção "Logon Único" selecionada no menu suspenso.](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Preencha o formulário da seguinte maneira:

    ![Configurar o logon único](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecione **Criar qualquer usuário que possa se autenticar com o SSO**

    b. **SAML do Provedor de Serviços**: cole o valor **_urn:oasis:names:tc:SAML:2.0:nameid-format:persistent_**

    c. **Nome do campo de email do Provedor de Identidade na resposta do ACS**: cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID da entidade do EntityDescriptor do provedor de identidade:** Cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    e. **URL Identity Provider SingleSignOnService:** Cole a **URL de Logon** da portal do Azure.

    f. **Certificado X509 público do Provedor de Identidade:** Abra o certificado X509 baixado do Azure no bloco de notas e cole o conteúdo nessa caixa. Verifique se não há quebras de linha no meio do conteúdo do certificado.

    g. Marque as caixas de seleção a seguir: **Habilitado, Criptografar NameID e Assinar AuthnRequests.**

    h. Clique em **Atualizar Configurações de SSO** para salvar as configurações.

### <a name="create-scalex-enterprise-test-user"></a>Criar um usuário de teste do ScaleX Enterprise

Para permitir que os usuários do Azure AD entrem no ScaleX Enterprise, eles precisam ser provisionados no ScaleX Enterprise. No caso do ScaleX Enterprise, o provisionamento é uma tarefa automática e nenhuma etapa manual é necessária. Qualquer usuário que pode se autenticar com êxito com as credenciais de SSO será provisionado automaticamente no ScaleX.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ScaleX Enterprise no Painel de Acesso, você deverá ser conectado automaticamente ao ScaleX Enterprise, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o ScaleX Enterprise com o Azure AD](https://aad.portal.azure.com/)
