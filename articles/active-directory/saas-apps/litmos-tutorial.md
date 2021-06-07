---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Litmos | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Litmos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: cfdcef2b9adf4e7ce500a9a89a45678a60afffc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Litmos

Neste tutorial, você aprenderá a integrar o Litmos ao Azure AD (Azure Active Directory). Quando integrar o Litmos ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Litmos.
* Permitir que os usuários sejam conectados automaticamente ao Litmos com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Litmos habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Litmos é compatível com SSO iniciado por **IDP**
* O Litmos é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-litmos-from-the-gallery"></a>Adicionando o Litmos da galeria

Para configurar a integração do Litmos ao AD do Azure, você precisará adicionar o Litmos da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Litmos** na caixa de pesquisa.
1. Escolha **Litmos** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Configurar e testar o logon único do Azure AD para o Litmos

Configure e teste o SSO do Azure AD com o Litmos usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Litmos.

Para configurar e testar o SSO do Azure AD com o Litmos, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Litmos](#configure-litmos-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Litmos](#create-litmos-test-user)** – para ter um equivalente de B. Fernandes no Litmos que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Litmos**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.litmos.com/account/Login`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, que são explicados adiante no tutorial, ou entre em contato com a [equipe de suporte ao cliente do Litmos](https://www.litmos.com/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Litmos**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Litmos.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Litmos**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-litmos-sso"></a>Configurar o SSO do Litmos

1. Em outra janela do navegador, entre em seu site de empresa do Litmos como administrador.

2. Na barra de navegação à esquerda, clique em **Contas**.

    ![Seção Contas no lado do aplicativo][22]

3. Clique na guia **Integrações** .

    ![Guia Integração][23]

4. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.

    ![Seção SAML 2.0][24]

5. Copie o valor em **O ponto de extremidade SAML do Litmos é:** e cole-o na caixa de texto **URL de Resposta** na seção **Domínio e URLs do Litmos** do portal do Azure.

    ![Ponto de extremidade SAML][26]

6. No seu aplicativo **Litmos** , execute as seguintes etapas:

    ![Aplicativo Litmos][25]

    a. Clique em **Habilitar SAML**.

    b. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado SAML X.509** .

    c. Clique em **Salvar Alterações**.

### <a name="create-litmos-test-user"></a>Criar um usuário de teste do Litmos

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Litmos. O aplicativo Litmos oferece suporte ao provisionamento Just-in-Time. Isso significa que, se for necessário, uma conta de usuário será criada automaticamente durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.

**Para criar um usuário chamado Brenda Fernandes no Litmos, execute as seguintes etapas:**

1. Em outra janela do navegador, entre em seu site de empresa do Litmos como administrador.

2. Na barra de navegação à esquerda, clique em **Contas**.

    ![Seção Contas no lado do aplicativo][22]

3. Clique na guia **Integrações** .

    ![Guia Integrações][23]

4. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.

    ![SAML 2.0][24]

5. Selecione **Gerar Usuários Automaticamente**
  
    ![Gerar usuários automaticamente][27]

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Litmos no Painel de Acesso, você deverá ser conectado automaticamente ao Litmos, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Litmos com o Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png