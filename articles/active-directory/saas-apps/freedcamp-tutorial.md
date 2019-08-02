---
title: 'Tutorial: Integração do Azure Active Directory ao Freedcamp | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101907"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutorial: Integração do Freedcamp com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Freedcamp ao Azure AD (Azure Active Directory). Ao integrar o Freedcamp ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Freedcamp.
* Permitir que os usuários sejam conectados automaticamente ao Freedcamp com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Freedcamp com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Freedcamp dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="adding-freedcamp-from-the-gallery"></a>Adicionar Freedcamp da galeria

Para configurar a integração do Freedcamp com o Azure AD, você precisa adicionar o Freedcamp à sua lista de aplicativos SaaS gerenciados a partir da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Freedcamp** na caixa de pesquisa.
1. Selecione **Freedcamp** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Freedcamp usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Freedcamp.

Para configurar e testar o SSO do Azure AD com o Freedcamp, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Freedcamp](#configure-freedcamp)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Freedcamp](#create-freedcamp-test-user)** – para ter um equivalente de Brenda Fernandes no Freedcamp que esteja vinculado à representação de usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Freedcamp**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    1. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Os usuários também podem inserir os valores da URL de seus próprios domínios de cliente, e não precisam seguir necessariamente o padrão `freedcamp.com`, eles podem inserir qualquer valor específico de domínio do cliente, específico à própria instância do aplicativo. Também é possível contatar a [equipe de suporte do cliente Freedcamp](mailto:devops@freedcamp.com) para obter mais informações sobre padrões de URL.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Freedcamp**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Configurar o Freedcamp

1. Para automatizar a configuração no Freedcamp, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o Freedcamp** direcionará você ao aplicativo Freedcamp. Nele, forneça as credenciais de administrador para entrar no Freedcamp. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Freedcamp manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Freedcamp como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique em **perfil** e navegue até **Minha Conta**.

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config01.png)

5. Do lado esquerdo da barra de menus, clique em **SSO** e, na página **Suas conexões SSO** execute as seguintes etapas:

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. Na caixa de texto **Título**, digite o título.

    b. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o certificado codificado em Base64 no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    e. Clique em **Enviar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes para usar o logon único do Azure permitindo a ela acesso ao Freedcamp.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Freedcamp**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-freedcamp-test-user"></a>Criar usuário de teste do Freedcamp

Para permitir que os usuários do Azure AD entrem no Freedcamp, eles precisam ser provisionados no Freedcamp. No Freedcamp, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela diferente do navegador da Web, faça logon no Freedcamp como administrador de segurança.

2. No canto superior direito da página, clique em **perfil** e navegue até **Gerenciar Sistema**.

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config03.png)

3. No lado direito da página Gerenciar Sistema, execute as seguintes etapas:

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Clique em **Adicionar ou convidar Usuários**.

    b. Na caixa de texto **E-mail**, insira o e-mail do usuário como `Brittasimon@contoso.com`.

    c. Clique em **Adicionar Usuário**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Freedcamp no Painel de Acesso, você deverá ser conectado automaticamente ao Freedcamp, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)