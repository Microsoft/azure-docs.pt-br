---
title: 'Tutorial: Integração do Azure Active Directory ao AnswerHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf3894daf71be0defe4271a08ec5e8d963e0d34
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227490"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Azure Active Directory ao AnswerHub

Neste tutorial, você aprende a integrar o AnswerHub ao Azure AD (Azure Active Directory).
A integração do AnswerHub ao Azure AD oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao AnswerHub.
* Você pode permitir que os usuários entrem automaticamente no AnswerHub com suas contas do Azure AD (logon único).
* Você pode gerenciar suas contas em uma localização central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AnswerHub, você precisará do seguinte:

* Uma assinatura do Azure AD. Caso não tenha um ambiente do Azure AD, inicie uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para logon único do AnswerHub que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O AnswerHub dá suporte ao SSO iniciado por SP.

## <a name="add-answerhub-from-the-gallery"></a>Adicionar o AnswerHub por meio da galeria

Para configurar a integração do AnswerHub ao Azure AD, você precisará adicionar o AnswerHub por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o AnswerHub por meio da galeria:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![Botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

    ![Folha de Aplicativos Empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela.

    ![Botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **AnswerHub**. Selecione **AnswerHub** na lista de resultados e, em seguida, selecione **Adicionar**.

     ![AnswerHub na lista de resultados](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o AnswerHub usando um usuário de teste chamado Brenda Fernandes.
Para o logon único, você precisará estabelecer um vínculo entre um usuário do Azure AD e o usuário correspondente no AnswerHub.

Para configurar e testar o logon único do Azure AD com o AnswerHub, você precisará concluir estas tarefas:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) – para habilitar os usuários a usar o recurso.
2. [Configurar o logon único do AnswerHub](#configure-answerhub-single-sign-on) – para definir as configurações de logon único no lado do aplicativo.
3. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) chamado Brenda Fernandes.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. Criar um usuário de teste do AnswerHub que corresponda ao usuário de teste do Azure AD e que esteja vinculado a ele.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configura o logon único do Azure AD no portal do Azure.

**Para configurar o logon único do Azure AD com o AnswerHub:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **AnswerHub**, clique em **Logon único**.

    ![Botão Logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de Logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Caixa de diálogo Selecionar um método de Logon único](common/select-saml-option.png)

3. Na página **Configurar o Logon Único com o SAML**, selecione o ícone de edição para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Página Configurar o Logon Único com o SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, conclua as seguintes etapas:

    ![Seção Configuração Básica do SAML](common/sp-identifier.png)

    a. Na caixa **URL de Logon**, insira uma URL que tenha este padrão: `https://<company>.answerhub.com`

    b. Na caixa **Identificador (ID da Entidade)** , insira uma URL que tenha este padrão: `https://<company>.answerhub.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do AnswerHub](mailto:success@answerhub.com) para obter os valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **Certificado (Base64)** , de acordo com suas necessidades e salve o certificado no computador.

    ![Link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o AnswerHub**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

   Você pode copiar estas URLs:
    - URL de logon

    - Identificador do Azure AD

    - URL de logoff

### <a name="configure-answerhub-single-sign-on"></a>Configurar o logon único do AnswerHub

Nesta seção, você configurará o logon único do AnswerHub.  

**Para configurar o logon único do AnswerHub:**

1. Em outra janela do navegador da Web, entre em seu site de empresa do AnswerHub como administrador.

    > [!NOTE]
    > Caso precise de ajuda para configurar o AnswerHub, contate a [equipe de suporte do AnswerHub](mailto:success@answerhub.com.).

2. Vá para **Administração**.

3. Na guia **Usuários e Grupos**, no painel esquerdo, na seção **Configurações Sociais**, selecione **Configuração do SAML**.

4. Na guia **Configuração do IdP**, conclua estas etapas:

    ![Guia Usuários e Grupos](./media/answerhub-tutorial/ic785172.png "Configuração do SAML")  
  
    a. Na caixa **URL de Logon do IdP**, cole a **URL de Logon** copiada do portal do Azure.
  
    b. Na caixa **URL de Logoff do IdP**, cole a **URL de Logoff** copiada do portal do Azure.

    c. Na caixa **Formato do Identificador de Nome do IdP**, insira o valor do **Identificador** selecionado na seção **Atributos de Usuário** no portal do Azure.
  
    d. Selecione **Chaves e Certificados**.

5. Na seção **Chaves e Certificados**, conclua estas etapas:

    ![Seção Chaves e Certificados](./media/answerhub-tutorial/ic785173.png "Chaves e Certificados")  

    a. Abra o certificado codificado em Base64 baixado no portal do Azure no Bloco de notas, copie o conteúdo e, em seguida, cole o conteúdo na caixa **Chave Pública do IdP (Formato x509)** .
  
    b. Clique em **Salvar**.

6. Na guia **Configuração do IdP**, selecione **Salvar** novamente.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

**Para criar um usuário de teste do Azure AD:**

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Selecione Azure Active Directory, Usuários, Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, conclua estas etapas.

    ![Propriedades do usuário](common/user-properties.png)

    a. Na caixa **Nome**, insira **BrendaFernandes**.
  
    b. Na caixa **Nome de usuário**, insira **brendafernandes\@<domíniodaempresa.extensão>** .  
    Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você configurará o usuário Brenda Fernandes para que use o logon único do Azure AD, concedendo a esse usuário acesso ao AnswerHub.

**Para atribuir o usuário de teste do Azure AD:**

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **AnswerHub**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **AnswerHub**.

    ![Lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e, em seguida, escolha o botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. 

7. Escolha o botão **Selecionar** na parte inferior da tela.

8. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-an-answerhub-test-user"></a>Criar um usuário de teste do AnswerHub

Para permitir que os usuários do Azure AD entrem no AnswerHub, você precisará adicioná-los ao AnswerHub. No AnswerHub, essa tarefa é feita manualmente.

**Para configurar uma conta de usuário:**

1. Entre em seu site de empresa do **AnswerHub** como administrador.

2. Vá para **Administração**.

3. Selecione a guia **Usuários e Grupos**.

4. No painel esquerdo, na seção **Gerenciar Usuários**, selecione **Criar ou importar usuários** e, em seguida, **Usuários e Grupos**.

   ![Guia Usuários e Grupos](./media/answerhub-tutorial/ic785175.png "Usuários e Grupos")

5. Nas caixas apropriadas, insira o **Endereço de email**, o **Nome de usuário** e a **Senha** de uma conta válida do Azure AD que deseja adicionar e, em seguida, selecione **Salvar**.

> [!NOTE]
> Você pode usar qualquer outra API ou ferramenta de criação de conta de usuário fornecida pelo AnswerHub para configurar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco do AnswerHub no Painel de Acesso, você deverá ser conectado automaticamente ao AnswerHub, para o qual você configurou o SSO. Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

