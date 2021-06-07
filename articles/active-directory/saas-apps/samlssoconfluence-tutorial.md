---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do SAML para o Confluence da resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Confluence da Resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651273"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory ao SSO do SAML para o Confluence da resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Confluence da resolution GmbH ao Azure AD (Azure Active Directory). Ao integrar o SSO do SAML para Confluence da resolution GmbH ao Azure AD, você poderá:

* Controlar no Azure AD quem terá acesso ao SSO do SAML para o Confluence da resolution GmbH.
* Permitir que os usuários sejam conectados automaticamente ao SSO do SAML para Confluence da resolution GmbH com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SSO do SAML para o Confluence da resolution GmbH.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do SAML para o Confluence da resolution GmbH dá suporte ao SSO iniciado por **SP e IDP**

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adicionar o SSO do SAML para Confluence da resolution GmbH por meio da galeria

Para configurar a integração do SSO do SAML para Confluence da Resolution GmbH no Azure AD, adicione o SSO do SAML para Confluence da Resolution GmbH da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSO do SAML para Confluence da resolution GmbH** na caixa de pesquisa.
1. Selecione **SSO do SAML para Confluence da resolution GmbH** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Configurar e testar o SSO do Azure AD para o SSO do SAML para Confluence da resolution GmbH

Configure e teste o SSO do Azure AD com o SSO do SAML para Confluence da resolution GmbH usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado no SSO do SAML para Confluence da resolution GmbH.

Para configurar e testar o SSO do Azure AD com o SSO do SAML para Confluence da resolution GmbH, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do SAML para Confluence da resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SSO do SAML para o Confluence da resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do SAML para o Confluence da resolution GmbH que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SSO do SAML para Confluence da resolution GmbH**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do SSO do SAML para Confluence da Resolution GmbH](https://www.resolution.de/go/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo acesso ao SSO do SAML para Confluence da resolution GmbH.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para Confluence da Resolution GmbH**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Configurar o SSO do SAML para o Confluence da resolution GmbH

1. Em uma janela diferente do navegador da Web, faça logon no **portal do administrador do SSO do SAML para Confluence da Resolution GmbH** como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.
    
    ![Captura de tela que mostra o ícone de "Engrenagem" selecionado e a opção "Complementos" selecionada na lista suspensa.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Você é redirecionado à página de Acesso de Administrador. Insira a Senha e clique no botão **Confirmar**.

    ![Captura de tela que mostra a página "Acesso de Administrador" com o botão "Confirmar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Na guia **ATLASSIAN MARKETPLACE**, clique em **Localizar novos complementos**. 

    ![Captura de tela que mostra a guia "Attlassian Marketplace" com a opção "Encontrar novos complementos" selecionada.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Pesquise **SSO (Logon Único) do SAML para Confluence** e clique no botão **Instalar** para instalar o novo plug-in do SAML.

    ![Captura de tela que mostra a página "Encontrar novos complementos" com "SSO (Logon Único) via SAML para Confluence" na caixa de pesquisa e o botão "Instalar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. A instalação do plug-in será iniciada. Clique em **fechar**

    ![Captura de tela que mostra a caixa de diálogo "Instalando".](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Captura de tela que mostra "Instalado e pronto para começar!" caixa de diálogo com a ação "Fechar" selecionada.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Clique em **Gerenciar**.

    ![Captura de tela que mostra a página de aplicativo "SSO (Logon Único) via SAML para Confluence" com o botão "Gerenciar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Clique em **Configurar** para configurar o novo plug-in.

    ![Captura de tela que mostra a página "Gerenciar" com o botão "Configurar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Este novo plug-in também pode ser encontrado na guia **USUÁRIOS E SEGURANÇA**.

    ![Captura de tela que mostra a guia "Usuários e Segurança" com "Logon Único via SAML" selecionado.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Em **Configuração de Plug-in de Logon Único do SAML**, clique no botão **Adicionar novo IdP** para definir as configurações do Provedor de Identidade.

    ![Captura de tela que mostra a página "Configuração de Plug-in de Logon Único via SAML", com o botão "Adicionar novo IdP" selecionado.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Na página **Escolher seu Provedor de Identidade SAML**, execute as seguintes etapas:

    ![Captura de tela que mostra a página "Escolha o seu Provedor de Identidade SAML" com as caixas de texto "Tipo de IdP", "Nome" e "Descrição" realçadas.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Definir o **Azure Active Directory** como o tipo de IdP.
    
    b. Adicionar **Nome** do Provedor de Identidade (por exemplo, Azure AD).
    
    c. Adicionar **Descrição** do Provedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Próximo**.
    
12. Na página **Configuração do provedor de identidade**, clique no botão **Avançar**.

    ![Captura de tela que mostra a página "Configuração do provedor de identidade" com o botão "Avançar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Na página **Importar metadados de IdP do SAML**, execute as seguintes etapas:

    ![Captura de tela que mostra a página "Importar Metadados IdP de SAML" com os botões "Importar", "Carregar Arquivo" e "Avançar" selecionados.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Clique no botão **Carregar arquivo** e selecione o arquivo XML de metadados que você baixou na etapa 5.

    b. Clique no botão **Importar**.
    
    c. Espere um pouco até que a importação seja bem-sucedida.
    
    d. Clique no botão **Avançar**.
    
14. Na página de **Transformação e atributo de ID de usuário**, clique no botão **Avançar**.

    ![Captura de tela que mostra a página "Atributo e transformação de ID de usuário" com o botão "Avançar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Na página **Criação e atualização de usuário**, clique em **Salvar e avançar** para salvar as configurações.   
    
    ![Captura de tela que mostra a página "Atualização e criação do usuário" com o botão "Salvar e Avançar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Na página **Testar suas configurações**, clique em **Ignorar teste e configurar manualmente** para ignorar momentaneamente o teste do usuário. Isso será executado na próxima seção e requer algumas configurações no portal do Azure. 
    
    ![Captura de tela que mostra a página "Testar as suas configurações" com o botão "Ignorar teste e configurar manualmente" selecionado.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Na caixa de diálogo que aparece com a mensagem **Ignorar o teste significa...**, clique em **OK**.
    
    ![Configurar o logon único](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Criar um usuário de teste do SSO do SAML para o Confluence da resolution GmbH

Para habilitar usuários do Azure AD a fazerem logon no SSO do SAML para Confluence da Resolution GmbH, eles devem ser provisionados no SSO do SAML para Confluence da Resolution GmbH.  
No SSO do SAML para Confluence da Resolution GmbH, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa do SAML SSO para Confluence da Resolution GmbH como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Captura de tela que mostra o ícone de "Engrenagem" selecionado e a opção "Gerenciamento de usuário" selecionada no menu.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Na seção usuários, clique na guia **Adicionar usuários**. Na página do diálogo **“Adicionar um usuário”** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brenda Fernandes.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha de Brenda Fernandes.

    e. Clique em **Confirmar Senha** e redigite a senha.
    
    f. Clique no botão **Adicionar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SSO do SAML para Confluence da resolution GmbH, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SSO do SAML para Confluence da resolution GmbH e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao SSO do SAML para Confluence da resolution GmbH para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do SSO do SAML para Confluence da resolution GmbH em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao SSO do SAML para Confluence da resolution GmbH para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSO do SAML para Confluence da resolution GmbH, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).