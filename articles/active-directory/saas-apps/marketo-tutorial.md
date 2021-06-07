---
title: 'Tutorial: Integração do Azure Active Directory ao Marketo | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686947"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Azure Active Directory com o Marketo

Neste tutorial, você aprenderá a integrar o Marketo ao Azure AD (Azure Active Directory).
A integração do Marketo ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Marketo.
* Você pode permitir que os usuários sejam conectados automaticamente ao Marketo (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Marketo, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Marketo

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Marketo dá suporte ao SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-marketo-from-the-gallery"></a>Adicionar o Marketo da galeria

Para configurar a integração do Marketo ao Azure AD, você precisará adicionar o Marketo da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Marketo** na caixa de pesquisa.
1. Selecione **Marketo** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Configurar e testar o SSO do Azure AD para o Marketo

Nesta seção, você configurará e testará o logon único do Azure AD com o Marketo, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Marketo.

Para configurar e testar o logon único do Azure AD com o Marketo, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o SSO do Azure AD com a usuária Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o SSO do Azure AD.
2. **[Configurar o SSO do Marketo](#configure-marketo-sso)** – para definir as configurações de SSO no lado do aplicativo.
    1. **[Criar um usuário de teste do Marketo](#create-marketo-test-user)** – para ter um equivalente de Brenda Fernandes no Marketo que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Marketo**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite a URL `https://saml.marketo.com/sp`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e o estado de Retransmissão reais. Contate a [equipe de suporte ao cliente do Marketo](https://investors.marketo.com/contactus.cfm) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Marketo**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Marketo.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Marketo**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-marketo-sso"></a>Configurar o SSO do Marketo

1. Para automatizar a configuração no Marketo, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Marketo** direcionará você ao aplicativo Marketo. De lá, forneça as credenciais de administrador para entrar no Marketo. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Marketo manualmente, em outra janela do navegador da Web, entre no site da empresa do Marketo como administrador.

1. Para obter a ID do Munchkin do seu aplicativo, execute as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar o logon único1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique no link do **Munchkin**.
   
    ![Configurar o logon único2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie a ID do Munchkin mostrada na tela e preencha sua URL de resposta no assistente de configuração do Azure AD.
   
    ![Configurar o logon único3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Para configurar o SSO no aplicativo, siga estas etapas:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar o logon único4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique em **Logon Único**.
   
    ![Configurar o logon único5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar as configurações de SAML, clique no botão **Editar**.
   
    ![Configurar Logon Único6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Configurações de logon único **habilitadas**.
   
    f. Cole o **Identificador do Azure AD** na caixa de texto **ID do Emissor**.
   
    g. Na caixa de texto **ID da Entidade**, insira a URL como `http://saml.marketo.com/sp`.
   
    h. Selecione o local da ID de usuário como **elemento do Identificador de Nome**.
   
    ![Configurar o logon único7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o identificador de usuário não for um valor de UPN, altere o valor na guia Atributo.
   
    i. Carregue o certificado que você baixou do assistente de configuração do Azure AD. **Salve** as configurações.
   
    j. Edite as configurações de redirecionamento de páginas.
   
    k. Cole a **URL de Logon** na caixa de texto **URL de Logon**.
   
    l. Cole a **URL de Logoff** na caixa de texto **URL de Logoff**.
   
    m. Na **URL de erro**, copie a **URL da instância do Marketo** e clique no botão **Salvar** para salvar as configurações.
   
    ![Configurar o logon único8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Para habilitar o SSO para usuários, conclua as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar o logon único9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu **Segurança** e clique em **Configurações de Logon**.
   
    ![Configurar o logon único10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Marque a opção **Exigir SSO** e **salve** as configurações.
   
    ![Configurar o logon único11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Criar um usuário de teste do Marketo

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Marketo. siga estas etapas para criar um usuário na plataforma do Marketo.

1. Faça logon usando credenciais de administrador de aplicativo do Marketo.

2. Clique no botão **Admin** no painel de navegação superior.
   
    ![usuário de teste1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue até o menu **Segurança** e clique em **Usuários e Funções**
   
    ![usuário de teste2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Clique no link **Convidar Novo Usuário** na guia Usuários
   
    ![usuário de teste3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. No assistente para Convidar Novo Usuário, preencha as informações a seguir
   
    a. Insira o endereço de **Email** do usuário na caixa de texto
   
    ![usuário de teste4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Insira o **Nome** na caixa de texto
   
    c. Insira o **Sobrenome** na caixa de texto
   
    d. Clique em **Avançar**.

6. Na guia **Permissões**, selecione **userRoles** e clique em **Avançar**
   
    ![usuário de teste5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Clique no botão **Enviar** para enviar o convite de usuário
   
    ![usuário de teste6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Os usuários receberão a notificação de email e precisarão clicar no link e alterar a senha para ativar a conta. 

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo, no portal do Azure e entre automaticamente no Marketo para o qual você configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Marketo em Meus Aplicativos, será conectado automaticamente ao Marketo para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Marketo, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).