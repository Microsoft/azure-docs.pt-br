---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Learning | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Learning.
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
ms.openlocfilehash: 33ac21eb87b57487277e598e899061e8757e82fc
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Learning

Neste tutorial, você aprenderá a integrar o LinkedIn Learning ao Azure AD (Azure Active Directory). Ao integrar o LinkedIn Learning ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao LinkedIn Learning.
* Permitir que os usuários sejam conectados automaticamente ao LinkedIn Learning com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do LinkedIn Learning habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O LinkedIn Learning é compatível com SSO iniciado por **SP e IDP**
* O LinkedIn Learning dá suporte ao provisionamento de usuário **Just-In-Time**


## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionando o LinkedIn Learning da galeria

Para configurar a integração do LinkedIn Learning com o Azure AD, é necessário adicionar o LinkedIn Learning da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LinkedIn Learning** na caixa de pesquisa.
1. Selecione **LinkedIn Learning** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Configurar e testar o SSO do Azure AD para o LinkedIn Learning

Configure e teste o SSO do Azure AD com o LinkedIn Learning usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Learning.

Para configurar e testar o SSO do Azure AD com o LinkedIn Learning, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do LinkedIn Learning](#configure-linkedin-learning-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do LinkedIn Learning](#create-linkedin-learning-test-user)** – para ter um equivalente de B.Fernandes no LinkedIn Learning que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LinkedIn Learning**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

     a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn. 

    b. Na caixa de texto **URL de resposta**, insira o **URL do serviço de consumidor de afirmações (ACS)** copiado do portal do LinkedIn.

    c. Se você quiser configurar o aplicativo no modo **iniciado por SP**, clique na opção **Definir URLs adicionais** na seção **Configuração Básica do SAML** em que você especificará a URL de logon. Para criar sua URL de login, copie o **URL do Serviço de consumidor de afirmações (ACS)** e substitua / saml / por / login /. Depois disso, o URL de conexão deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informações de logon único em Domínio e URLs do LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Esses valores não são reais. Você atualizará esses valores com o Identificador e a URL de Resposta reais, que será explicado mais adiante na seção **Configurar o SSO do LinkedIn Learning** do tutorial.

1. O aplicativo LinkedIn Learning espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo LinkedIn Learning espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o LinkedIn Learning**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao LinkedIn Learning.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LinkedIn Learning**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-linkedin-learning-sso"></a>Configurar o SSO do LinkedIn Learning

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Learning como administrador.

2. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Learning – Padrão** na lista suspensa.

    ![A captura de tela mostra as Configurações Globais em que você pode selecionar Padrão.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique em **OU clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da Entidade** e a **URL do ACS (Serviço do Consumidor de Declaração)** e cole-as na seção **Configuração Básica do SAML** no portal do Azure.

    ![A captura de tela mostra o Logon Único em que você pode inserir os valores descritos.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você baixou do Portal do Azure clicando na opção **Carregar o arquivo XML**.

    ![A captura de tela mostra Definir as configurações de SSO do provedor de serviços do LinkedIn em que você pode carregar um arquivo XML.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique em **Ativar** para habilitar o SSO. O status do SSO é alterado de **Não Conectado** para **Conectado**

    ![A captura de tela mostra o Logon Único em que você pode habilitar Autenticar usuários com SSO.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Criar um usuário de teste do LinkedIn Learning

O aplicativo LinkedIn Learning dá suporte ao provisionamento de usuário just in time e, após a autenticação, os usuários serão criados automaticamente no aplicativo. Na página de configurações de administração no portal do LinkedIn Learning, coloque a opção **Atribuir licenças automaticamente** na posição de ativar o provisionamento just in time, o que também atribuirá uma licença ao usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do LinkedIn Learning, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do LinkedIn Learning diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao LinkedIn Learning, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do LinkedIn Learning em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao LinkedIn Learning, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Após configurar o LinkedIn Learning, você poderá impor controles de sessão, que protegem contra o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
