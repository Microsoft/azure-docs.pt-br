---
title: 'Tutorial: Integração do Azure Active Directory com o ProMaster (por Inlogik) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e ProMaster (por Inlogik).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 37d47c36ebbd7c274bac2a9e1b7e940614f2aa6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511649"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Tutorial: Integração do Azure Active Directory com o ProMaster (por Inlogik)

Neste tutorial, você aprenderá a integrar o ProMaster (por Inlogik) com o Microsoft Azure Active Directory.
A integração do ProMaster (por Inlogik) ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao ProMaster (por Inlogik).
* É possível permitir que seus usuários entrem automaticamente no ProMaster (por Inlogik) (Logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o ProMaster (por Inlogik), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do ProMaster (por Inlogik)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ProMaster (por Inlogik) é compatível com o SSO iniciado por **SP** e **IDP**
* Depois de configurar o ProMaster (por Inlogik), você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Adicionando ProMaster (por Inlogik) da galeria

Para configurar a integração do ProMaster (por Inlogik) no Microsoft Azure Active Directory, você precisa adicionar o ProMaster (por Inlogik) da galeria para a sua lista dos aplicativos SaaS geenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **ProMaster (por Inlogik)** na caixa de pesquisa.
1. Selecione **ProMaster (por Inlogik)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ProMaster (por Inlogik) baseado em uma usuária de teste chamada **B.Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ProMaster (por Inlogik).

Para configurar e testar o logon único do Microsoft Azure Active Directory com ProMaster (por Inlogik), é necessário concluir os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar SSO do ProMaster (por Inlogik)](#configure-promaster-by-inlogik-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar usuário de teste do ProMaster (por Inlogik)](#create-promaster-by-inlogik-test-user)** – para ter um equivalente de B.Fernandes no ProMaster (por Inlogik) que esteja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ProMaster (por Inlogik), execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ProMaster (por Inlogik)** , selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>
    https://<CUSTOMDOMAIN>/SAMLBASE
    ```

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>/saml/acs
    https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs
    ```

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>
    https://<CUSTOMDOMAIN>/SAMLBASE
    ```

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a equipe de suporte ao cliente [ProMaster (por Inlogik)](https://www.inlogik.com/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você habilita B.Fernandes para usar o logon único do Azure concedendo acesso ao ProMaster (por Inlogik).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ProMaster (por Inlogik)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-promaster-by-inlogik-sso"></a>Configurar o SSO do ProMaster (por Inlogik)

Para configurar o logon único no lado **ProMaster (por Inlogik)** , é necessário enviar a **URL de metadados de federação do aplicativo** para a [equipe de suporte do ProMaster (por Inlogik)](https://www.inlogik.com/contact). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-promaster-by-inlogik-test-user"></a>Criar usuário de teste do ProMaster (por Inlogik)

Nesta seção, você criará uma usuária chamada B.Fernandes no ProMaster (por Inlogik). Trabalhe com a [equipe de suporte do ProMaster (por Inlogik)](https://www.inlogik.com/contact) para adicionar os usuários na plataforma ProMaster (por Inlogik). Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ProMaster (por Inlogik) no Painel de Acesso, você deverá entrar automaticamente no ProMaster (por Inlogik) para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o ProMaster (por Inlogik) com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o ProMaster (por Inlogik) com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)