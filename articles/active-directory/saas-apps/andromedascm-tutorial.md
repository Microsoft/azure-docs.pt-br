---
title: 'Tutorial: Integração do Azure Active Directory ao Andromeda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Andromeda.
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
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736031"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integração do Azure Active Directory ao Andromeda

Neste tutorial, você aprende a integrar o Andromeda ao Azure AD (Azure Active Directory).
A integração do Andromeda ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Andromeda.
* Você pode permitir que seus usuários entrem automaticamente no Andromeda (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Andromeda, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Andromeda

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Andromeda dá suporte ao SSO iniciado por **SP e IDP**
* O Andromeda é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-andromeda-from-the-gallery"></a>Adicionando o Andromeda por meio da galeria

Para configurar a integração do Andromeda ao Azure AD, você precisa adicionar o Andromeda por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Andromeda** na caixa de pesquisa.
1. Selecione **Andromeda** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Configurar e testar o SSO do Azure AD para o Andromeda

Configure e teste o SSO do Azure AD com o Andromeda usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Andromeda.

Para configurar e testar o SSO do Azure AD com o Andromeda, execute as seguintes etapas:


1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Andromeda](#configure-andromeda-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Andromeda](#create-andromeda-test-user)** – para ter um equivalente de Brenda Fernandes no Andromeda que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Andromeda**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Captura de tela que mostra Definir URLs adicionais, em que você pode inserir uma URL de Logon.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Esses valores não são reais. Você atualizará o valor com o identificador real, URL de resposta, e URL de logon que é explicado no tutorial posteriormente.

6. O aplicativo Andromeda espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Captura de tela que mostra Atributos do usuário, como givenname user.givenname e emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Desmarque out as definições do NameSpace ao configurá-los.

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | Nome | Atributo de Origem|
    | ------ | -----------|
    | função        | Função específica do aplicativo |
    | type        | Tipo de Aplicativo |
    | company       | CompanyName |

    > [!NOTE]
    > O Andromeda espera funções para usuários atribuídos ao aplicativo. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra Declarações do usuário com opções para Adicionar nova declaração e Salvar.](common/new-save-attribute.png)

    ![Captura de tela que mostra Gerenciar declarações do usuário, em que pode inserir os valores descritos nesta etapa.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o Andromeda**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao Andromeda.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Andromeda**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-andromeda-sso"></a>Configurar o SSO do Andromeda

1. Faça logon no site de empresa do Andromeda como administrador.

2. Na parte superior da barra de menus, clique em **Admin** e navegue até **Administração**.

    ![Administrador do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. No lado esquerdo da barra de ferramentas na seção **Interfaces**, clique em **Configuração do SAML**.

    ![SAML do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na página **Configuração do SAML**, realize as seguintes etapas:

    ![Configuração do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Marque **Habilitar o SSO com SAML**.

    b. Na seção **Informações sobre o Andromeda**, copie o valor **Identidade do SP** e cole-o na caixa de texto **Identificador** da seção **Configuração básica do SAML**.

    c. Copie o valor **URL do Consumidor** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração básica do SAML**.

    d. Copie o valor **URL de Logon** e cole-o na caixa de texto **URL de Logon** da seção **Configuração básica do SAML**.

    e. Na seção **provedor de identidade SAML**, digite seu nome IDP.

    f. Na caixa de texto **Ponto de extremidade do logon único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    g. Abra o **certificado codificado com Base64** baixado do portal do Azure no bloco de notas, cole-o na caixa de texto **Certificado X 509**.
    
    h. Mapeie os seguintes atributos com o respectivo valor para facilitar o logon SSO do Azure AD. O atributo **ID de usuário** é necessário para fazer login. Para o provisionamento, **Email**, **Company**, **UserType** e **Role** são obrigatórios. Nesta seção, definimos o mapeamento de atributos (nome e valores) correlacionados àqueles definidos no portal do Azure

    ![attbmap do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Clique em **Save** (Salvar).


### <a name="create-andromeda-test-user"></a>Criar um usuário de teste do Andromeda

Nesta seção, um usuário chamado Brenda Fernandes será criado no Andromeda. O Andromeda dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Andromeda, um novo usuário será criado após a autenticação. Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Andromeda](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Andromeda, na qual será possível iniciar o fluxo de logon.  

* Acesse a URL de Logon do Andromeda diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Andromeda para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Andromeda, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Andromeda para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Andromeda, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
