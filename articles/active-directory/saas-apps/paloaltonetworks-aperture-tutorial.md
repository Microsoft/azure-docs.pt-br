---
title: 'Tutorial: integração do Microsoft Azure Active Directory com o Palo Alto Networks – Aperture | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Aperture.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b4c8ab55a9b2afc9c1a215236fa1276a630a5cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963614"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: integração do Microsoft Azure Active Directory com o Palo Alto Networks – Aperture

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – Aperture ao Microsoft Azure AD.
A integração do Palo Alto Networks – Aperture ao Microsoft Azure AD oferece os seguintes benefícios:

* No Microsoft Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – Aperture.
* É possível permitir que os usuários entrem automaticamente no Palo Alto Networks – Aperture (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o Palo Alto Networks – Aperture, os seguintes itens serão necessários:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Palo Alto Networks – Aperture habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Palo Alto Networks – Aperture é compatível com o SSO iniciado por **SP** e **IDP**

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Adicionar o Palo Alto Networks – Aperture da galeria

Para configurar a integração do Palo Alto Networks – Aperture com o Azure Active Directory, você precisará adicionar o Palo Alto Networks – Aperture da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Palo Alto Networks – Aperture** na caixa de pesquisa.
1. Selecione **Palo Alto Networks – Aperture** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Palo Alto Networks – Aperture, com base em uma usuária de teste chamada **B. Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks – Aperture.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks – Aperture, siga estas etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Palo Alto Networks – Aperture](#configure-palo-alto-networks---aperture-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Palo Alto Networks – Aperture](#create-palo-alto-networks---aperture-test-user)** – para ter um equivalente de Brenda Fernandes no Palo Alto Networks – Aperture que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Palo Alto Networks – Aperture**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Uma captura de tela que mostra a "Configuração Básica de SAML" com as caixas de texto "Identificador" e "URL de Resposta" realçadas, bem como a ação "Salvar" selecionada.](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Palo Alto Networks – Aperture – SP](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Palo Alto Networks – Aperture**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Palo Alto Networks – Aperture.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Palo Alto Networks – Aperture**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-palo-alto-networks---aperture-sso"></a>Configurar o SSO do Palo Alto Networks – Aperture

1. Em outra janela do navegador da Web, entre no Palo Alto Networks - Aperture como Administrador.

2. Na barra de menu superior, clique em **CONFIGURAÇÕES**.

    ![Guia de configurações](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navegue para a seção **APLICAÇÃO** e clique em **Autenticação** no lado esquerdo do menu.

    ![Guia de Autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Na página **Autenticação**, execute as seguintes etapas:
    
    ![Guia de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Marque **Habilitar Logon Único (os provedores SSP com suporte são Okta, One login)** do campo **Logon Único**.

    b. Na caixa de texto **ID do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Clique em **Escolher Arquivo** para fazer upload do Certificado baixado do Azure Active Directory no campo **Certificado do Provedor de Identidade**.

    d. Na caixa de texto **URL de SSO do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    e. Analise as informações IdP na seção **Aperture Info** e baixe o certificado do campo **Aperture Key**.

    f. Clique em **Save** (Salvar).


### <a name="create-palo-alto-networks---aperture-test-user"></a>Criar usuário de teste do Palo Alto Networks – Aperture

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Palo Alto Networks - Aperture. Trabalhe com a [equipe de suporte ao cliente do Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para adicionar os usuários na plataforma Palo Alto Networks - Aperture. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Palo Alto Networks – Aperture, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Palo Alto Networks – Aperture diretamente e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Palo Alto Networks – Aperture, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Palo Alto Networks – Aperture em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Palo Alto Networks – Aperture, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Palo Alto Networks – Aperture, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).