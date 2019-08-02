---
title: 'Tutorial: Integração do Azure Active Directory ao Coralogix | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 721e0c40ec2e02dabee0681e01fea4182b906183
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104656"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Tutorial: Integração do Azure Active Directory ao Coralogix

Neste tutorial, você aprenderá a integrar o Coralogix ao Azure AD (Azure Active Directory).
A integração do Coralogix ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Coralogix.
* Você pode permitir que os usuários sejam conectados automaticamente ao Coralogix (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Coralogix, você precisará dos seguintes itens:

- Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, obtenha uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Uma assinatura habilitada para logon único do Coralogix. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Coralogix dá suporte ao SSO iniciado por SP.

## <a name="add-coralogix-from-the-gallery"></a>Adicionar o Coralogix por meio da galeria

Para configurar a integração do Coralogix ao Azure AD, primeiro adicione o Coralogix por meio da galeria à lista de aplicativos SaaS gerenciados.

Para adicionar o Coralogix por meio da galeria, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Coralogix**. Selecione **Coralogix** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

     ![Coralogix na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Coralogix, com base em um usuário de teste chamado Brenda Fernandes.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Coralogix.

Para configurar e testar o logon único do Azure AD com o Coralogix, primeiro conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Configurar o logon único do Coralogix](#configure-coralogix-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Criar um usuário de teste do Coralogix](#create-a-coralogix-test-user) para ter um equivalente de Brenda Fernandes no Coralogix que esteja vinculado à representação de usuário do Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Coralogix, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coralogix**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de Logon único**, selecione **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Coralogix](common/sp-identifier.png)

    a. Na caixa **URL de Logon**, insira uma URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , insira uma URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou o

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Coralogix](mailto:info@coralogix.com) para obter o valor. Veja também os padrões na seção **Configuração Básica do SAML** no portal do Azure.

5. O aplicativo Coralogix espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. Na página **Configurar Logon Único com o SAML**, selecione o botão **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário**, na caixa de diálogo **Atributos de Usuário**, edite as declarações usando o ícone **Editar**. Adicione também as declarações usando **Adicionar nova declaração** para configurar o atributo do token SAML, conforme mostrado na imagem anterior. Em seguida, execute as etapas a seguir:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![imagem](./media/coralogix-tutorial/tutorial_usermail.png) ![imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Na lista **Escolher formato do identificador de nome**, selecione **Endereço de email**.

    c. Na lista **Atributo de origem**, selecione **user.mail**.

    d. Clique em **Salvar**.

7. Na página **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com suas necessidades. Em seguida, salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Coralogix**, copie as URLs apropriadas.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-coralogix-single-sign-on"></a>Configurar o logon único do Coralogix

Para configurar o logon único no lado do **Coralogix**, envie o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Coralogix](mailto:info@coralogix.com). Eles garantem que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Na parte superior da tela, selecione **Novo usuário**.

    ![Botão Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, insira "brittasimon@yourcompanydomain.extension". Por exemplo, nesse caso, você poderá inserir "brittasimon@contoso.com".

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo o acesso ao Coralogix.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Coralogix**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Coralogix**.

    ![O link do Coralogix na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

### <a name="create-a-coralogix-test-user"></a>Criar um usuário de teste do Coralogix

Nesta seção, você criará um usuário chamado Brenda Fernandes no Coralogix. Trabalhe com a [equipe de suporte do Coralogix](mailto:info@coralogix.com) para adicionar os usuários à plataforma Coralogix. Você precisa criar e ativar os usuários antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o bloco do Coralogix no portal Meus Aplicativos, você deverá ser conectado automaticamente ao Coralogix. Para obter mais informações sobre o portal Meus Aplicativos, confira [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

