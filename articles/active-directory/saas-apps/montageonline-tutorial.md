---
title: 'Tutorial: Integração do Azure Active Directory com o Montage Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Montage Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 2caf0384a8e55a44f78b37c4ca8776675b6e971d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504000"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Tutorial: Integração do Azure Active Directory ao Montage Online

Neste tutorial, você aprenderá a integrar o Montage Online ao Azure Active Directory (Azure AD).
A integração do Montage Online ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Montage Online.
* Você pode permitir que seus usuários entrem automaticamente no Montage Online (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Montage Online, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Montage Online

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Montage Online dá suporte ao SSO iniciado por **SP**

## <a name="adding-montage-online-from-the-gallery"></a>Adicionando o Montage Online a partir da galeria

Para configurar a integração do Montage Online ao Azure AD, você precisa adicionar o Montage Online a partir da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Montage Online a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos** .

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Montage Online** , selecione **Montage Online** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Montage Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Montage Online, com base em um usuário de teste chamado **Brenda Fernandes** .
Para que o logon único funcione, precisa ser estabelecida uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Montage Online.

Para configurar e testar o logon único do Azure AD com o Montage Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Montage Online](#configure-montage-online-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Montage Online](#create-montage-online-test-user)** – para ter um equivalente de Brenda Fernandes no Montage Online que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Montage Online, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **Montage Online** , clique em **Logon único** .

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único** , selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML** .

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML** , realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Montage Online](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon** , digite uma URL usando o seguinte padrão:

    Para o ambiente de produção: `https://<subdomain>.montageonline.co.nz/`

    Para o ambiente de teste: `https://build-<subdomain>.montageonline.co.nz/`

    b. Na caixa de texto **Identificador** , digite uma URL:

    Para o ambiente de produção: `MOL_Azure`

    Para o ambiente de teste: `MOL_Azure_Build`

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte do Cliente Montage Online](https://www.montage.co.nz/contact-us/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **Certificado de Autenticação SAML** , clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Montage Online** , copie a URL apropriada de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-montage-online-single-sign-on"></a>Configurar o Logon Único do Montage Online

Para configurar o logon único no lado do **Montage Online** , é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Montagem Online](https://www.montage.co.nz/contact-us/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory** , selecione **Usuários** e, em seguida, **Todos os usuários** .

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome** , insira **BrendaFernandes** .
  
    b. No campo **Nome de usuário** , digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Montage Online.

1. No portal do Azure, selecione **Aplicativos Empresariais** , **Todos os aplicativos** e, em seguida, **Montage Online** .

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Montage Online** .

    ![O link do Montage Online na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos** .

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos** , escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função** , escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

### <a name="create-montage-online-test-user"></a>Criar um usuário de teste do Montage Online

Nesta seção, você criará um usuário chamado Brenda Fernandes no Montage Online. Trabalhe com a [equipe de suporte do Monatge Online](https://www.montage.co.nz/contact-us/) para adicionar os usuários à plataforma Montage Online. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Montage Online no Painel de Acesso, você será conectado automaticamente ao Montage Online para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)