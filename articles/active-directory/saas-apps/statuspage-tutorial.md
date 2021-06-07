---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao StatusPage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 6bc2508220a8c0193efb55eb6cd11f7e151c80be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao StatusPage

Neste tutorial, você aprenderá a integrar o StatusPage ao Azure AD (Azure Active Directory). Ao integrar o StatusPage ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao StatusPage.
* Permitir que os usuários entrem automaticamente no StatusPage com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o StatusPage, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do StatusPage habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O StatusPage dá suporte ao SSO iniciado por **IdP**.

## <a name="add-statuspage-from-the-gallery"></a>Adicionando o StatusPage da galeria

Para configurar a integração do StatusPage ao AD do Azure, você precisará adicionar o StatusPage da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **StatusPage** na caixa de pesquisa.
1. Selecione **StatusPage** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Configurar e testar o SSO do Azure AD para o StatusPage

Nesta seção, você configurará e testará o logon único do Azure AD com o StatusPage, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do StatusPage.

Para configurar o SSO do Azure AD com o StatusPage, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do StatusPage](#configure-statuspage-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do StatusPage](#create-statuspage-test-user)** – para ter um equivalente de Brenda Fernandes no StatusPage que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **AskYourTeam**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:

    | Identificador |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

     | URL de resposta |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Entre em contato com a equipe de suporte do StatusPage em [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)para solicitar os metadados necessários para a configuração do logon único. 
    >
    > a. Nos metadados, copie o valor de Issuer e cole-o na caixa de texto **Identificador** .
    >
    > b. Nos metadados, copie o valor a URL de Resposta e cole-a na caixa de texto **URL de Resposta** .

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o StatusPage**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao StatusPage.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **StatusPage**.

2. Na lista de aplicativos, selecione **StatusPage**.

3. No menu à esquerda, selecione **Usuários e grupos**.

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-statuspage-sso"></a>Configurar o SSO do StatusPage

1. Para automatizar a configuração no StatusPage, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, se você clicar em **Configurar o StatusPage**, será direcionado ao aplicativo StatusPage. Nele, forneça as credenciais de administrador para entrar no StatusPage. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o StatusPage manualmente, em outra janela do navegador da Web, entre em seu site da empresa do StatusPage como administrador.

1. Na barra de ferramentas principal, clique em **Gerenciar Conta**.

    ![Captura de tela que mostra a opção Gerenciar Conta selecionada no site de empresa do StatusPage.](./media/statuspage-tutorial/account.png)

1. Clique na guia **Logon Único** .

    ![Captura de tela que mostra a guia Logon Único.](./media/statuspage-tutorial/tab.png)

1. Na página Instalação do SSO, execute as seguintes etapas:

    ![Captura de tela que mostra a página Configuração do SSO, na qual você pode inserir os valores descritos.](./media/statuspage-tutorial/setup.png)

    ![Captura de tela que mostra o botão Salvar Configuração.](./media/statuspage-tutorial/configuration.png)

    a. Na caixa de texto **URL de Destino de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado** .

    c. Clique em **SALVAR CONFIGURAÇÃO**.

### <a name="create-statuspage-test-user"></a>Criar um usuário de teste do StatusPage

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no StatusPage.

O StatusPage dá suporte ao provisionamento de usuário just-in-time. Você já habilitou isso em [Configurar o logon único do Azure AD](#configure-azure-ad-sso).

**Para criar um usuário chamado Brenda Fernandes no StatusPage, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do StatusPage como administrador.

1. Na parte superior do menu, clique em **Gerenciar Conta**.

    ![Captura de tela que mostra a opção Gerenciar Conta selecionada no site de empresa do StatusPage.](./media/statuspage-tutorial/account.png)

1. Clique na guia **Membros da Equipe**.
  
    ![Captura de tela que mostra a guia Membros da Equipe.](./media/statuspage-tutorial/sandbox.png) 

1. Clique em **ADICIONAR MEMBRO DA EQUIPE**.
  
    ![Captura de tela que mostra o botão Adicionar Membro da Equipe.](./media/statuspage-tutorial/team.png) 

1. Digite o **Endereço de Email**, o **Nome** e o **Sobrenome** de um usuário válido que você deseja provisionar nas caixas de texto relacionadas. 

    ![Captura de tela que mostra a caixa de diálogo Adicionar um Usuário, na qual você pode inserir os valores descritos.](./media/statuspage-tutorial/user.png) 

1. Como **Função**, escolha **Administrador do Cliente**.

1. Clique em **CRIAR CONTA**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao StatusPage, para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do StatusPage em Meus Aplicativos, você deverá ser conectado automaticamente ao StatusPage, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o StatusPage, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).