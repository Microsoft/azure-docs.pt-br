---
title: 'Tutorial: Integração do Azure Active Directory ao BC in the Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BC in the Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 5b8d9cedcc2ca3085f342db0bbf5fe3704ac7242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674082"
---
# <a name="tutorial-azure-active-directory-integration-with-bc-in-the-cloud"></a>Tutorial: Integração do Azure Active Directory ao BC in the Cloud

Neste tutorial, você aprende a integrar o BC in the Cloud ao Azure AD (Azure Active Directory).
A integração do BC in the Cloud ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao BC in the Cloud.
* Você pode permitir que os usuários sejam conectados automaticamente ao BC in the Cloud (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao BC in the Cloud, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do BC in the Cloud

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O BC in the Cloud dá suporte ao SSO iniciado por **SP**

## <a name="adding-bc-in-the-cloud-from-the-gallery"></a>Adicionando o BC in the Cloud por meio da galeria

Para configurar a integração do BC in the Cloud ao Azure AD, você precisa adicionar o BC in the Cloud à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BC in the Cloud por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **BC in the Cloud**, selecione **BC in the Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BC in the Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o BC in the Cloud, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BC in the Cloud.

Para configurar e testar o logon único do Azure AD com o BC in the Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do BC in the Cloud](#configure-bc-in-the-cloud-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do BC in the Cloud](#create-bc-in-the-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no BC in the Cloud que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o BC in the Cloud, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BC in the Cloud**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do BC in the Cloud](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.bcinthecloud.com/router/loginSaml/<customerid>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://app.bcinthecloud.com`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do BC in the Cloud](https://www.bcinthecloud.com/supportcenter/) para obter esse valor.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o BC in the Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-bc-in-the-cloud-single-sign-on"></a>Configurar o logon único do BC in the Cloud

Para configurar o logon único no lado do **BC in the Cloud**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do BC in the Cloud](https://www.bcinthecloud.com/supportcenter/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.

    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BC in the Cloud.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **BC in the Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BC in the Cloud**.

    ![O link do BC in the Cloud na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-bc-in-the-cloud-test-user"></a>Criar um usuário de teste do BC in the Cloud

Nesta seção, você cria um usuário chamado Brenda Fernandes no BC in the Cloud. Trabalhe com a [equipe de suporte do BC in the Cloud](https://www.bcinthecloud.com/supportcenter/) para adicionar os usuários na plataforma do BC in the Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BC in the Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao BC in the Cloud, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)