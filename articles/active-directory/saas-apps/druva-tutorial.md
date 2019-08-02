---
title: 'Tutorial: Integração do Azure Active Directory com o Druva | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f034a206ca114b484bd29c72d8e53f9ae5aa498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103697"
---
# <a name="tutorial-integrate-druva-with-azure-active-directory"></a>Tutorial: Integrar o Druva ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Druva ao Azure AD (Azure Active Directory). Quando integrar o Druva ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Druva.
* Permitir que os usuários sejam conectados automaticamente ao Druva com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Druva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Druva dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-druva-from-the-gallery"></a>Adicionar o Druva da galeria

Para configurar a integração do Druva com o Azure AD, você precisará adicionar o Druva da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Druva** na caixa de pesquisa.
1. Escolha **Druva** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Druva usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Druva.

Para configurar e testar o SSO do Azure AD com o Druva, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Druva](#configure-druva-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Druva](#create-druva-test-user)** – para ter um equivalente de Brenda Fernandes no Druva vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Druva**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso você deseje configurar o aplicativo no modo iniciado pelo **IdP**, o usuário não precisará executar nenhuma etapa, pois o aplicativo já estará pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://login.druva.com/login`

1. O aplicativo Druva espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME | Atributo de Origem|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Insira o valor gerado pelo token |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Druva**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-druva-sso"></a>Configurar o SSO do Druva

1. Em outra janela do navegador da Web, entre em seu site de empresa do Druva como administrador.

1. Vá para **Gerenciar \> Configurações**.

    ![Configurações](./media/druva-tutorial/ic795091.png "Configurações")

1. Na caixa de diálogo Configurações de Logon Único, execute as seguintes etapas:

    ![Configurações de Logon Único](./media/druva-tutorial/ic795092.png "Configurações de Logon Único")

    a. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **URL de Logout do Provedor de Identidade**, cole o valor de **URL de Logout** que você copiou do portal do Azure

    c. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de ID**

    d. Para abrir a página **Configurações**, clique em **Salvar**.

1. Na página **Configurações**, clique em **Gerar Token de SSO**.

    ![Configurações](./media/druva-tutorial/ic795093.png "Configurações")

1. No diálogo **Token de Autenticação de Logon Único** , realize as seguintes etapas:

    ![Token SSO](./media/druva-tutorial/ic795094.png "Token SSO")

    a. Clique em **Copiar**, cole o valor copiado na caixa de texto **Valor** na seção **Adicionar Atributo** no Portal do Azure.

    b. Clique em **fechar**

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Druva.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Druva**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-druva-test-user"></a>Criar um usuário de teste do Druva

Para permitir que os usuários do Azure AD entrem no Druva, eles precisam ser provisionados no Druva. No caso do Druva, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre em seu site de empresa do **Druva** como administrador.

1. Vá para **Gerenciar \> Usuários**.

    ![Gerenciar Usuários](./media/druva-tutorial/ic795097.png "Gerenciar Usuários")

1. Clique em **Criar Novo**.

    ![Gerenciar Usuários](./media/druva-tutorial/ic795098.png "Gerenciar Usuários")

1. Na caixa de diálogo Criar Novo Usuário, execute as seguintes etapas:

    ![Criar Novo Usuário](./media/druva-tutorial/ic795099.png "Criar Novo Usuário")

    a. Na caixa de texto **Endereço de email**, insira o email do usuário como **brendafernandes\@contoso.com**.

    b. Na caixa de texto **Nome**, insira o nome de usuário, como **BrendaFernandes**.

    c. Clique em **Criar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Druva ou as APIs fornecidas pelo Druva para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Druva no Painel de Acesso, você deverá ser conectado automaticamente ao Druva, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
