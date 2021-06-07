---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HRworks Single Sign-On | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HRworks Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442500"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HRworks Single Sign-On

Neste tutorial, você aprenderá a integrar o HRworks Single Sign-On ao Azure AD (Azure Active Directory). Ao integrar o HRworks Single Sign-On ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao HRworks Single Sign-On.
* Permitir que os usuários sejam conectados automaticamente ao HRworks Single Sign-On com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do HRworks Single Sign-On.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O HRworks Single Sign-On dá suporte ao SSO **SP** iniciado

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionando o HRworks Single Sign-On por meio da galeria

Para configurar a integração do HRworks Single Sign-On ao Azure AD, você precisará adicionar o HRworks Single Sign-On da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **HRworks Single Sign-On** na caixa de pesquisa.
1. Selecione **HRworks Single Sign-On** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Configurar e testar o logon único do Azure AD para o HRworks Single Sign-On

Configure e teste o SSO do Azure AD com o HRworks Single Sign-On usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no HRworks Single Sign-On.

Para configurar e testar o SSO do Azure AD com o HRworks Single Sign-On, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do HRworks Single Sign-On](#configure-hrworks-single-sign-on-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)** : para ter um equivalente de B.Fernandes no HRworks Single Sign-On que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **HRworks Single Sign-On**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do HRworks Single Sign-On](mailto:nadja.sommerfeld@hrworks.de) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o HRworks Single Sign-On**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao HRworks Single Sign-On.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **HRworks Single Sign-On**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Configurar o SSO do HRworks Single Sign-On

1. Para automatizar a configuração no HRworks Single Sign-On, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, se você clicar em **Configurar o HRworks Single Sign-On**, será direcionado para o aplicativo HRworks Single Sign-On. Nele, forneça as credenciais de administrador para entrar no HRworks Single Sign-On. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 4.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o HRworks Single Sign-On manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do HRworks Single Sign-On como administrador e execute as seguintes etapas:

1. Clique em **Administrator** > **Básico** > **Segurança** > **Single Sign-on** do lado esquerdo da barra de menus e execute as seguintes etapas:

    ![Configurar o logon único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Marque a caixa **Usar o Single Sign-on**.

    b. Selecione **Metadados XML** como **Método de entrada de metadados**.

    c. Selecione **Identificador Individual NameID** como **Valor de NameID**.

    d. No Bloco de Notas, abra o arquivo XML de Metadados baixado do portal do Azure, copie seu conteúdo e, em seguida, cole-o na caixa de texto **Metadados**.

    e. Clique em **Save** (Salvar).

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar um usuário de teste do HRworks Single Sign-On

Para permitir que os usuários do Azure AD entrem no HRworks Single Sign-On, eles devem ser provisionados no HRworks Single Sign-On. No HRworks Single Sign-On, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no HRworks Single Sign-On como Administrador.

1. Clique em **Administrador** > **Pessoas** > **Pessoas** > **Nova pessoa** do lado esquerdo da barra de menus.

     ![Captura de tela que mostra a página de trabalhos de RH com Pessoas e Nova pessoa selecionadas.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. No pop-up, clique em **Próxima**.

    ![Captura de tela que mostra uma lista de países que você deve escolher para a pessoa.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Sobre o pop-up **Criar nova pessoa com país para termos legais**, preencha os respectivos detalhes como **Primeiro nome**, **Sobrenome** e clique em **Criar**.

    ![Captura de tela que mostra caixas de texto em que você pode inserir o nome e o sobrenome da pessoa.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do HRworks Single Sign-On no Painel de Acesso, você deverá entrar automaticamente no HRworks Single Sign-On para o qual tiver configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o HRworks Single Sign-On com o Azure AD](https://aad.portal.azure.com/)