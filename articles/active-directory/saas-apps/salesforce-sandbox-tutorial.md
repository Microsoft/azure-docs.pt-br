---
title: 'Tutorial: Integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: c82b00b1f107dc147cbef6a0ca406f2054321216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734863"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce Sandbox

Neste tutorial, você aprenderá como integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory). Ao integrar o Salesforce Sandbox ao Azure AD, você poderá:

* Controlar quem tem acesso ao Salesforce Sandbox no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Salesforce Sandbox com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Salesforce Sandbox.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Salesforce Sandbox dá suporte ao SSO iniciado por **SP e IDP**
* O Salesforce Sandbox é compatível com o provisionamento de usuário **Just In Time**
* O Salesforce Sandbox é compatível com o [provisionamento de usuário **Automatizado**](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria

Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Salesforce Sandbox** na caixa de pesquisa.
1. Selecione **Salesforce Sandbox** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Configurar e testar o SSO do Azure AD para o Salesforce Sandbox

Configure e teste o SSO do Azure AD com o Salesforce Sandbox usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Para configurar e testar o SSO do Azure AD com o Salesforce Sandbox, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Sandbox](#configure-salesforce-sandbox-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Salesforce Sandbox](#create-salesforce-sandbox-test-user)** – para ter um equivalente de B.Fernandes no Salesforce Sandbox que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Salesforce Sandbox**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, se você tiver um **Arquivo de metadados do Provedor de Serviço** e quiser configurar o modo iniciado por **IDP**, execute as etapas a seguir:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços no portal de administração do Salesforce Sandbox, explicado posteriormente no tutorial.

    c. Depois que o arquivo de metadados for enviado, o **URL de resposta** será preenchido automaticamente na caixa de texto **URL de resposta**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o valor da **URL de Resposta** não for preenchido automaticamente, preencha-o manualmente de acordo com seus requisitos.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Salesforce Sandbox**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Salesforce Sandbox.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce Sandbox**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-salesforce-sandbox-sso"></a>Configurar o SSO do Salesforce Sandbox

1. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

2. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Captura de tela que mostra o ícone de "Configurações" selecionado no canto superior direito e a opção "Configuração" selecionada na lista suspensa.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Captura de tela que mostra o menu "Configurações" no painel esquerdo, com a opção "Configurações de Logon Único" selecionada no menu "Identidade".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com a caixa de seleção "S A M L Habilitado" selecionada e o botão "Salvar" selecionado.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com o botão "Novo Com Base No Arquivo de Metadados" selecionado.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com os botões "Escolher Arquivo" e "Criar" selecionados.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **Configurações de logon único do SAML**, os campos são preenchidos automaticamente, clique em Salvar.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com os campos populados e o botão "Salvar" selecionado.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **Configurações de Logon Único**, clique no botão **Baixar Metadados** para baixar o arquivo de metadados do provedor de serviços. Usar esse arquivo a **básicas de configuração de SAML** seção no portal do Azure para configurar as URLs necessárias, conforme explicado acima.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com o botão "Baixar Metadados" selecionado.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se você desejar configurar o aplicativo no modo iniciado **SP**, os pré-requisitos disso estão a seguir:

    a. Você deve ter um domínio verificado.

    b. Você precisa configurar e habilitar seu domínio na Área Restrita do Salesforce, as etapas para isso serão explicadas posteriormente neste tutorial.

    c. No portal do Azure, sobre o **básicas de configuração de SAML** seção, clique em **definir URLs adicionais** e execute a seguinte etapa:
  
    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](common/both-signonurl.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Esse valor deve ser copiado do portal da Área Restrita Salesforce depois que você tiver habilitado o domínio.

11. Sobre o **certificado de autenticação SAML** seção, clique em **XML de metadados de Federação** e, em seguida, salve o arquivo xml em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

12. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

13. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Captura de tela que mostra o ícone de "Configurações" selecionado no canto superior direito e a opção "Configuração" selecionada no menu suspenso.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Captura de tela que mostra o menu "Configurações" no painel de navegação, com a opção "Configurações de Logon Único" selecionada no menu "Identidade".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com a caixa "S A M L Habilitado" selecionada e o botão "Salvar" selecionado.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" e o botão "Novo Com Base No Arquivo de Metadados" selecionado.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique em **Escolher arquivo** para carregar o arquivo XML de metadados e clique em **criar**.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com os botões "Escolher Arquivo" e "Criar" selecionados.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página **Configurações do SAML Single Sign-On**, em que os campos são preenchidos automaticamente, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*), na caixa de texto **Nome** e clique em salvar.

    ![Captura de tela que mostra a página "Configurações de Logon Único" com campos populados, um nome de exemplo presente na caixa de texto "Nome" e o botão "Salvar" selecionado.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para habilitar seu domínio na Área Restrita Salesforce, execute as etapas a seguir:

    > [!NOTE]
    > Antes de habilitar o domínio, você precisará criar o mesmo na Área Restrita Salesforce. Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois que o domínio for criado, certifique-se de que ele esteja configurado corretamente.

21. No painel de navegação à esquerda na Área Restrita Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Captura de tela que mostra as opções "Configurações da Empresa" e "Meu Domínio" selecionadas no painel de navegação esquerdo.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na seção **Configuração de Autenticação**, clique em **Editar**.

    ![Captura de tela que mostra a seção "Configuração de Autenticação" com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na seção **Configuração de Autenticação**, como **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único do SAML que você definiu durante a configuração de SSO na Área Restrita Salesforce e clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Criar um usuário de teste do Salesforce Sandbox

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox. A área restrita do Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-sandbox-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático de usuário.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Salesforce Sandbox na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Salesforce Sandbox diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado de modo automático ao Salesforce Sandbox para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Salesforce Sandbox em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Salesforce Sandbox para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Salesforce Sandbox, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)