---
title: 'Tutorial: Integração do Azure Active Directory ao Chargebee | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Chargebee.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 22753b80931956af6ce448cfee974ae746fff6e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456204"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Tutorial: Integrar o Chargebee ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Chargebee ao Azure AD (Azure Active Directory). Ao integrar o Chargebee ao Azure AD, você poderá:

* Controle no Azure AD quem tem acesso ao Chargebee.
* Permitir que os usuários sejam conectados automaticamente ao Chargebee com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Chargebee com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Chargebee dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-chargebee-from-the-gallery"></a>Como adicionar o Chargebee da Galeria

Para configurar a integração do Chargebee ao Azure AD, você precisa adicionar o Chargebee à sua lista de aplicativos SaaS gerenciados da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Chargebee** na caixa de pesquisa.
1. Selecione **Chargebee** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Configurar e testar o logon único do Azure AD para o Chargebee

Configure e teste o SSO do Azure AD com o Chargebee usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Chargebee.

Para configurar e testar o SSO do Azure AD com o Chargebee, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Chargebee](#configure-chargebee-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Chargebee](#create-chargebee-test-user)** – para ter um equivalente de B.Fernandes no Chargebee que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Chargebee**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<domainname>.chargebee.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.chargebee.com/saml/<domainname>/acs`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` é o nome do domínio que o usuário cria depois de reivindicar a conta. No caso de qualquer outra informação, entre em contato com a [equipe de suporte ao cliente do Chargebee](mailto:support@chargebee.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Chargebee**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Chargebee.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Chargebee**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-chargebee-sso"></a>Configurar o SSO do Chargebee

1. Abra uma nova janela do navegador da Web e entre no site de empresa do Chargebee como administrador.

4. No lado esquerdo do menu, clique em **Configurações** > **Segurança** > **Gerenciar**.

    ![Captura de tela que mostra o site da empresa Chargebee com Configurações, Segurança e Gerenciamento selecionados.](./media/chargebee-tutorial/config01.png)

5. No pop-up **Logon Único**, execute as seguintes etapas:

    ![Captura de tela que mostra a caixa de diálogo Logon Único com SAML selecionado e a opção para confirmar.](./media/chargebee-tutorial/config02.png)

    a. Selecione **SAML**.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o certificado codificado em Base64 no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado do SAML**.

    d. Clique em **Confirmar**.

### <a name="create-chargebee-test-user"></a>Criar usuário de teste do Chargebee

Para habilitar o Azure AD, os usuários entram no Chargebee, eles devem ser provisionados no Chargebee. No Chargebee, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela diferente do navegador da Web, entre no Chargebee como Administrador de Segurança.

2. No lado esquerdo do menu, clique em **Clientes** e, em seguida, navegue até **Criar um Novo Cliente**.

    ![A captura de tela que mostra o site do Chargebee com Clientes e Criar Cliente selecionados.](./media/chargebee-tutorial/config03.png)

3. Na página **Novo Cliente**, preencha os respectivos campos mostrados abaixo e clique em **Criar Cliente** para criação de usuário.

    ![Captura de tela que mostra a página Novo Cliente na qual você pode inserir informações do cliente.](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Chargebee no Painel de Acesso, você deverá ser conectado automaticamente ao Chargebee no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)