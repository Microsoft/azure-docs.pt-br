---
title: 'Tutorial: Integração do Azure Active Directory com o Workspot Control | Microsoft Docs'
description: Saiba como configurar o logon único para o Azure Active Directory e o Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086674"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração do Azure Active Directory com o Workspot Control

Neste tutorial, você aprenderá a integrar o Controle do Workspot ao Azure AD (Azure Active Directory). Ao integrar o Workspot Control ao Azure AD, você pode:

* Use o Azure AD para controlar quem tem acesso ao Workspot Control.
* Permita que os usuários entrem automaticamente no Workspot Control (SSO [logon único]) usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para obter mais informações sobre a integração de aplicativo SaaS ao Azure AD, confira [Logon único em aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Workspot Control, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

* Assinatura habilitada para um controle Workspot o logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

> [!Note]
> O Workspot Control é compatível com SSO iniciado por SP e IdP.


## <a name="adding-workspot-control-from-the-gallery"></a>Adicionando o controle do workspot da galeria

Para configurar a integração do Workspot Control ao Azure AD, você deve adicionar o Workspot Control da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workspot Control da galeria, siga estas etapas:**

1. No painel esquerdo do [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos Empresariais** e selecione **Todos os Aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

3. Selecione **Novo aplicativo** na parte superior da janela.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Workspot Control**, selecione **Workspot Control** no painel de resultados e selecione **Adicionar**.

     ![Janela "Adicionar da galeria"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Workspot Control para um usuário de teste chamado "Brenda Fernandes".
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workspot Control.

Para configurar e testar o logon único do Azure AD com o Workspot Control, você deve concluir as seguintes tarefas:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) – para habilitar os usuários a usar o recurso.
2. [Configurar o logon único do Workspot Control](#configure-workspot-control-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD para Brenda Fernandes.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Crie um usuário de teste do Workspot Control](#create-a-workspot-control-test-user) para estabelecer um equivalente de Brenda Fernandes no Workspot Control que esteja vinculado à representação de usuário do Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Workspot Control, siga estas etapas:

1. Na página de integração de aplicativo do **Workspot Control** no [portal do Azure](https://portal.azure.com/), selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na janela **Selecionar um método de logon único**, selecione o modo **SAML** para habilitar o logon único.

    ![Selecione uma janela de método de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com o SAML**, selecione o ícone **Editar** (lápis) para acessar **Configuração Básica do SAML**.

    ![Ícone de editar realçado em "Configuração Básica de SAML"](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP execute estas etapas:

    ![Informações de logon único em Domínio e URLs do Workspot Control](common/idp-intiated.png)

    1. Na caixa de texto **Identificador**, insira uma URL com o seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Na caixa de texto **URL de resposta**, insira uma URL com o seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais**.

    ![Informações de logon único em Domínio e URLs do Workspot Control](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, insira uma URL com o seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Esses valores não são reais. Substitua esses valores pelo Identificador, a URL de resposta e a URL de logon reais. Entre em contato com a [equipe de suporte ao cliente do Workspot Control](mailto:support@workspot.com) para obter esses valores. Ou veja também os padrões na seção **Configuração Básica do SAML** do portal do Azure.

6. Na página **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **Certificado (Base64)** usando as opções disponíveis de acordo com suas necessidades. Salve-o em seu computador.

    ![O link de download do Certificado (Base64)](common/certificatebase64.png)

7. Na seção **Configurar o Workspot Control**, copie a URL apropriado de acordo com seus requisitos:

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    - **URL de logon**

    - **Identificador do Azure AD**

    - **URL de logoff**

### <a name="configure-workspot-control-single-sign-on"></a>Configurar o logon único do Workspot Control

1. Em uma janela diferente do navegador da Web, entre no Workspot Control como um administrador de segurança.

2. Na barra de ferramentas na parte superior da página, selecione **Instalação** e, em seguida **SAML**.

    ![Opções de configuração](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Na janela **Configuração Security Assertion Markup Language**, siga estas etapas:
 
    ![Janela de Configuração de Security Assertion Markup Language](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Na caixa **ID de Entidade**, cole o **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa **URL do Serviço de Logon**, cole a **URL de Logon** copiada do portal do Azure.

    1. Na caixa **URL do Serviço de Logoff**, cole a **URL de Logoff** copiada do portal do Azure.

    1. Selecione **Arquivo de Atualização** para fazer upload no certificado X.509, o certificado codificado em base-64 que você baixou do portal do Azure.

    1. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da janela.

    ![O botão "Novo usuário"](common/new-user.png)

3. Nas propriedades do usuário, siga estas etapas:

    ![A janela Propriedades do usuário](common/user-properties.png)

    1. No campo **Nome**, insira **BrendaFernandes**.
  
    1. No **Nome de usuário**, insira **brendafernandes@* domíniodasuaempresa.extensão***. Por exemplo, insira **BrittaSimon@contoso.<i></i>com**.

    1. Selecione a caixa de seleção **Mostrar Senha**. Em seguida, anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a Brenda Fernandes acesso ao Workspot Control para permitir que ela use o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e então **Workspot Control**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workspot controle**.

    ![O link Workspot Control na lista Aplicativos](common/all-applications.png)

3. No menu do lado esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário**. Em seguida, selecione **Usuários e Grupos** na janela **Adicionar atribuição**.

    ![A janela "Adicionar Atribuição"](common/add-assign-user.png)

5. Na janela **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários**. Em seguida, clique em **Selecionar**.

6. Se você esperar qualquer valor de função na declaração SAML, selecione a função adequada para o usuário na lista na janela **Selecionar Função**. Então clique em **Selecionar** na parte inferior.

7. Na janela **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-workspot-control-test-user"></a>Criar um usuário de teste do controle Workspot

Para permitir que os usuários do Azure AD entrem no Workspot Control, eles devem ser provisionados no Workspot Control. O provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, siga estas etapas:**

1. Entre no Workspot Control como um administrador de segurança.

2. Na barra de ferramentas na parte superior da página, selecione **Usuários** e, em seguida **Adicionar Usuário**.

    ![Opções de "Usuários"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na janela **Adicionar um Novo Usuário**, siga estas etapas:

    ![Janela "Adicionar um Novo Usuário"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Na caixa **Nome**, insira o nome de um usuário, como **Brenda**.

    1. Na caixa de texto **Sobrenome**, insira o sobrenome do usuário, como **Fernandes**.

    1. Na caixa **Email**, insira o endereço de email do usuário, como **Brittasimon@contoso.<i></i>com**.

    1. Selecione a função de usuário apropriada na lista suspensa **Função**.

    1. Selecione o grupo de usuários apropriado na lista suspensa **Grupo**.

    1. Selecione **Adicionar usuário**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, podemos testar nossa configuração de logon único do Azure AD por meio do *Painel de Acesso*.

Ao clicar no bloco **Workspot Control** no Painel de Acesso, você deverá ser conectado automaticamente ao Workspot Control para o qual você configurou o SSO. Para saber mais, confira [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
