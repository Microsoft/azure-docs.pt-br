---
title: 'Tutorial: Integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Jira da Resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 827a05a8dfbf05b0dacb0bd812fb964567f39b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954200"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Jira da Resolution GmbH com o Azure AD (Azure Active Directory). Ao integrar o SSO do SAML para Jira da Resolution GmbH ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SSO do SAML para Jira da Resolution GmbH.
* Permitir que os usuários sejam conectados automaticamente ao SSO do SAML para Jira da resolution GmbH com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do SSO do SAML para Jira da Resolution GmbH habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO do SAML para Jira da Resolution GmbH é compatível com o SSO iniciado por **SP** e **IDP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Adicionar o SSO do SAML para Jira da Resolution GmbH a partir da galeria

Para configurar a integração do SSO do SAML para Jira da Resolution GmbH no Azure AD, adicione o SSO do SAML para Jira da Resolution GmbH da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSO do SAML para Jira da Resolution GmbH** na caixa de pesquisa.
1. Selecione **SSO do SAML para Jira da resolution GmbH** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Configurar e testar o SSO do Azure AD para o SSO do SAML para Jira da Resolution GmbH

Configure e teste o SSO do Azure AD com o SSO do SAML para Jira da Resolution GmbH usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado no SSO do SAML para Jira da Resolution GmbH.

Para configurar e testar o SSO do Azure AD com o SSO do SAML para Jira da Resolution GmbH, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAML para Jira da Resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste de SSO do SAML para Jira da Resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** – para ter um equivalente de B. Fernandes no SSO do SAML para Jira da Resolution GmbH vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SSO do SAML para Jira da Resolution GmbH**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se desejar configurar o aplicativo no modo iniciado por **IDP**, siga estas etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **Definir URLs adicionais** e siga esta etapa, se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o Identificador, URL de Resposta e URL de entrada, substitua **\<server-base-url>** pela URL base de sua instância do Jira. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure. Caso tenha algum problema, contate-nos em [equipe de suporte ao cliente do SSO do SAML para Jira da resolution GmbH](https://www.resolution.de/go/support).

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, baixe o **XML de metadados de Federação** e salve-o em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você habilitará B. Fernandes a usar o logon único do Azure concedendo acesso ao SSO do SAML para Jira da Resolution GmbH.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para Jira da Resolution GmbH**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Configurar o SSO do SAML para Jira da Resolution GmbH 

1. Em uma janela diferente do navegador da Web, entre na instância do Jira como administrador.

2. Passe o mouse sobre a engrenagem no lado direito e clique em **Gerenciar aplicativos**.
    
    ![Captura de tela que mostra uma seta apontando para o ícone de "Engrenagem" e a opção "Gerenciar aplicativos" selecionada na lista suspensa.](./media/samlssojira-tutorial/add-on-1.png)

3. Se você for redirecionado à página Acesso de Administrador, insira a **Senha** e clique no botão **Confirmar**.

    ![Captura de tela que mostra a página "Acesso de Administrador".](./media/samlssojira-tutorial/add-on-2.png)

4. Normalmente, o Jira redireciona você ao mercado da Atlassian. Caso contrário, clique em **Encontrar novos aplicativos** no painel esquerdo. Pesquise **SSO (Logon Único) do SAML para JIRA** e clique no botão **Instalar** para instalar o plug-in do SAML.

    ![Captura de tela que mostra a página "Atlassian Marketplace for JIRA" com uma seta apontando para o botão "Instalar" do aplicativo "SSO (Logon Único) do SAML para Jira, SAML/SSO".](./media/samlssojira-tutorial/store.png)

5. A instalação do plug-in será iniciada. Quando estiver pronto, clique no botão **Fechar**.

    ![Captura de tela que mostra a caixa de diálogo "Instalando".](./media/samlssojira-tutorial/store-2.png)

    ![Captura de tela que mostra "Instalado e pronto para começar!" caixa de diálogo com o botão "Fechar" selecionado.](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **Gerenciar**.

    ![Captura de tela que mostra o aplicativo "SSO (Logon Único) do SAML para Jira, SAML/SSO" com o botão "Gerenciar" selecionado.](./media/samlssojira-tutorial/store-4.png)
    
7. Em seguida, clique em **Configurar** para configurar o plug-in instalado recentemente.

    ![Captura de tela que mostra a página "Gerenciar aplicativos", com o botão "Configurar" selecionado para o aplicativo "Logon Único de SAML para Jira".](./media/samlssojira-tutorial/store-5.png)

8. No assistente **Configuração do plug-in de Logon Único do SAML**, clique em **Adicionar novo IdP** para configurar o Azure AD como um novo Provedor de Identidade.

    ![Captura de tela mostra a página "Bem-vindo(a)", com o botão "Adicionar novo IdP" selecionado.](./media/samlssojira-tutorial/add-on-4.png) 

9. Na página **Escolha seu Provedor de Identidade SAML**, siga estas etapas:

    ![Captura de tela que mostra a página "Escolha o seu Provedor de Identidade SAML" com as caixas de texto "Tipo de IdP" e "Nome" realçadas e o botão "Avançar" selecionado.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Definir o **Azure Active Directory** como o tipo de IdP.
    
    b. Adicione o **Nome** do Provedor de Identidade (por exemplo, Azure AD).
    
    c. Adicione uma **Descrição** (opcional) do Provedor de Identidade (por ex., Azure AD).
    
    d. Clique em **Próximo**.
    
10. Na página **Configuração do provedor de identidade**, clique em **Avançar**.
 
    ![Captura de tela que mostra a página "Configuração do provedor de identidade".](./media/samlssojira-tutorial/configuration.png)

11. Na página **Importar metadados de IdP do SAML**, execute as seguintes etapas:

    ![Captura de tela que mostra a página "Importar Metadados de IdP do SAML" com a ação "selecionar Arquivo XML de Metadados" selecionada.](./media/samlssojira-tutorial/metadata.png)

    a. Clique no botão **Selecionar Arquivo XML de Metadados** e selecione o arquivo **XML de Metadados de Federação** baixado anteriormente.

    b. Clique no botão **Importar**.
     
    c. Espere um pouco até que a importação seja bem-sucedida.  
     
    d. Clique no botão **Avançar**.
    
12. Na página **Atributo e transformação da ID de usuário**, clique no botão **Avançar**.

    ![Captura de tela que mostra a página "Atributo e transformação de ID de usuário" com o botão "Avançar" selecionado.](./media/samlssojira-tutorial/transformation.png)
    
13. Na página **Criação de usuário e atualização**, clique em **Salvar e Avançar** para salvar as configurações.
    
    ![Captura de tela que mostra a página "Atualização e criação do usuário" com o botão "Salvar e Avançar" selecionado.](./media/samlssojira-tutorial/update.png)
    
14. Na página **Testar suas configurações**, clique em **Ignorar teste e configurar manualmente** para ignorar o teste de usuário por enquanto. Isso será executado na próxima seção e requer algumas configurações no portal do Azure.
    
    ![Captura de tela que mostra a página "Testar as suas configurações" com o botão "Ignorar teste e configurar manualmente" selecionado.](./media/samlssojira-tutorial/test.png)
    
15. Clique em **OK** para ignorar o aviso.
    
    ![Captura de tela que mostra a caixa de diálogo de aviso com o botão "OK" selecionado.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Criar um usuário de teste do SSO do SAML para Jira da Resolution GmbH

Para permitir que usuários do Azure AD entrem no SSO do SAML para Jira da resolution GmbH, eles devem ser provisionados no SSO do SAML para Jira da resolution GmbH. No caso deste tutorial, é necessário realizar o provisionamento manualmente. No entanto, também há outros modelos de provisionamento disponíveis para o plug-in do SSO do SAML da resolution, por exemplo, provisionamento **Just-In-Time**. Veja sua documentação em [SSO do SAML da resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se você tiver uma dúvida sobre isso, contate o suporte no [suporte da resolution](https://www.resolution.de/go/support).

**Para provisionar manualmente uma conta de usuário, siga estas etapas:**

1. Entre na instância do Jira como administrador.

2. Passe o mouse sobre a engrenagem e escolha **Gerenciamento de usuário**.

   ![Captura de tela que mostra uma seta apontando para o ícone de "Engrenagem" e a opção "Gerenciamento de usuários" selecionada na lista suspensa.](./media/samlssojira-tutorial/user-1.png)

3. Se você for redirecionado à página Acesso de Administrador, insira a **Senha** e clique no botão **Confirmar**.

    ![Captura de tela que mostra a página "Acesso de Administrador" com a caixa de texto "Senha" selecionada.](./media/samlssojira-tutorial/user-2.png) 

4. Na seção da guia **Gerenciamento de usuário**, clique em **criar usuário**.

    ![Captura de tela que mostra a guia "Gerenciamento de usuário" com o botão "Criar usuário" selecionado.](./media/samlssojira-tutorial/user-3-new.png) 

5. Na página da caixa de diálogo **"Criar usuário"** , execute as etapas a seguir. É necessário criar o usuário exatamente como no Azure AD:

    ![Adicionar Funcionário](./media/samlssojira-tutorial/user-4-new.png) 

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário: **Brenda Fernandes**.

    c. Na caixa de texto **Nome de usuário**, digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>. 

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário** para concluir a criação do usuário.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SSO do SAML para Jira da Resolution GmbH, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SSO do SAML para Jira da Resolution GmbH e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao SSO do SAML para Jira da Resolution GmbH, para o qual você configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do SSO do SAML para Jira da Resolution GmbH em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao SSO do SAML para Jira da Resolution GmbH, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="enable-sso-redirection-for-jira"></a>Habilitar o redirecionamento de SSO para Jira

Conforme observado na seção anterior, no momento, há duas maneiras de disparar o logon único. Usando o **portal do Azure** ou usando **um link especial para sua instância do Jira**. O plug-in de SSO do SAML da resolution GmbH também permite que você dispare o logon único; basta **acessar qualquer URL que aponta para sua instância do Jira**.

Em essência, todos os usuários que acessam o Jira serão redirecionados para o logon único após a ativação de uma opção no plug-in.

Para ativar o redirecionamento de SSO, faça o seguinte em **sua instância do Jira**:

1. Acesse a página de configuração do plug-in de SSO do SAML no Jira.
1. Clique em **Redirecionamento** no painel esquerdo.

   ![Captura de tela parcial da página Configuração do Plug-In SingleSignOn SAML do Jira realçando o link de redirecionamento no painel de navegação esquerdo.](./media/samlssojira-tutorial/configure-1.png)

1. Marque **Habilitar redirecionamento de SSO**.

   ![Captura de tela parcial da página Configuração do Plug-In SingleSignOn SAML do Jira realçando a caixa de seleção "Habilitar Redirecionamento de SSO" selecionada.](./media/samlssojira-tutorial/configure-2.png) 

1. Pressione o botão **Salvar Configurações** no canto superior direito.

Após ativar a opção, ainda será possível chegar ao prompt de nome de usuário/senha se a opção **Habilitar nosso** estiver marcada navegando até `https://<server-base-url>/login.jsp?nosso`. Como sempre, substitua **\<server-base-url>** pela URL base.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSO do SAML para Jira da Resolution GmbH, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).