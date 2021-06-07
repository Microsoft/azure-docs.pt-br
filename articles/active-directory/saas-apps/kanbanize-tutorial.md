---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Kanbanize | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kanbanize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 05ce0d3d3d5c66514edc07446aaf0a879657c10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Kanbanize

Neste tutorial, você aprenderá a integrar o Kanbanize ao Azure AD (Azure Active Directory). Ao integrar o Kanbanize ao Azure AD, você poderá:

* Controlar no Azure AD quem terá acesso ao Kanbanize.
* Permitir que seus usuários entrem automaticamente no Kanbanize com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Kanbanize habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Kanbanize é compatível com o SSO iniciado por **SP e IDP**
* O Kanbanize é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionar o Kanbanize da Galeria

Para configurar a integração do Kanbanize com o Azure AD, você precisará adicionar o Kanbanize da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Kanbanize** na caixa de pesquisa.
1. Selecione **Kanbanize** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Configurar e testar logon único do Azure AD para o Kanbanize

Configure e teste o SSO do Azure AD com o Kanbanize usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kanbanize.

Para configurar e testar o SSO do Azure AD com o Kanbanize, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Kanbanize](#configure-kanbanize-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Kanbanize](#create-kanbanize-test-user)** – para ter um equivalente de B.Fernandes no Kanbanize que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Kanbanize**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `/ctrl_login/saml_login`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [Equipe de suporte ao cliente do Kanbanize](mailto:support@ms.kanbanize.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo Kanbanize espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML . A captura de tela a seguir mostra a lista de atributos padrão, em que nameidentifier é mapeado com **user.userprincipalname**. O aplicativo Kanbanize espera que nameidentifier seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone Editar e alterando-o.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Kanbanize**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Kanbanize.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kanbanize**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-kanbanize-sso"></a>Configurar o SSO do Kanbanize

1. Para automatizar a configuração no Kanbanize, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Kanbanize** direcionará você ao aplicativo Kanbanize. Nele, forneça as credenciais de administrador para entrar no Kanbanize. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Kanbanize manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Kanbanize como administrador e execute as seguintes etapas:

4. No lado superior direito da página, clique no logotipo de **Configurações**.

    ![Configurações do Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na página do painel de Administração do lado esquerdo do menu, clique em **Integrações** e, em seguida, habilite o **Logon Único**.

    ![A captura de tela mostra o painel Administração com a opção Integração selecionada.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Na seção Integrações, clique em **CONFIGURAR** para abrir a página **Integração de Logon Único**.

    ![Configuração do Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na página **Integração de Logon Único**, em **Configurações**, execute as seguintes etapas:

    ![A captura de tela mostra a página Integração de Logon Único em que você insere os valores nesta etapa.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Na caixa de texto **ID da Entidade de IdP**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.

    b. Na caixa de texto **Ponto de Extremidade de Logon de IDP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **Ponto de Extremidade de Logoff de IDP**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Na caixa de texto **Nome de atributo de Email**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Na caixa de texto **Nome de atributo de Primeiro Nome**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Na caixa de texto **Nome de atributo de Sobrenome**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Você pode obter esses valores combinando os valores de namespace e nome do respectivo atributo da seção de Atributos de usuário do portal do Azure.

    g. No Bloco de notas, abra o certificado codificado em Base 64 baixado no portal do Azure, copie o conteúdo (sem os marcadores de início e fim) e cole-o na caixa **Certificado X.509**.

    h. Marque **Habilitar logon com SSO e Kanbanize**.

    i. Clique em **Salvar Configurações**.

### <a name="create-kanbanize-test-user"></a>Criar usuário de teste do Kanbanize

Nesta seção, um usuário chamado B.Fernandes será criado no Kanbanize. O Kanbanize é compatível com o provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Kanbanize, um novo será criado após a autenticação. Se precisar criar um usuário manualmente, contate a [Equipe de suporte ao cliente do Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Kanbanize no Painel de Acesso, você deverá ser conectado automaticamente ao Kanbanize no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Kanbanize com o Azure AD](https://aad.portal.azure.com/)