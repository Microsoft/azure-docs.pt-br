---
title: 'Tutorial: Integração do Azure Active Directory ao Absorb LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646513"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Azure Active Directory ao Absorb LMS

Neste tutorial, você aprenderá a integrar o Absorb LMS ao Azure AD (Azure Active Directory). Quando integrar o Absorb LMS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Absorb LMS.
* Permitir que os usuários sejam conectados automaticamente ao Absorb LMS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Absorb LMS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Absorb LMS com logon único habilitado.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Absorb LMS é compatível com SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-absorb-lms-from-the-gallery"></a>Adicionar o Absorb LMS da galeria

Para configurar a integração do Absorb LMS ao Azure AD, você precisa adicionar o Absorb LMS da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Absorb LMS** na caixa de pesquisa.
1. Selecione **Absorb LMS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Configurar e testar o SSO do Azure AD para o Absorb LMS

Configure e teste o SSO do Azure AD com o Absorb LMS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Absorb LMS.

Para configurar e testar o SSO do Azure AD com o Absorb LMS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Absorb LMS](#configure-absorb-lms-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Absorb LMS](#create-absorb-lms-test-user)** – Para ter um equivalente de B.Fernandes no Absorb LMS que esteja vinculado à declaração de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Absorb LMS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    Se você estiver usando **Absorb 5 – interface do usuário**, use a seguinte configuração:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Se você estiver usando **Absorb 5 – Nova Experiência de Aprendiz**, use a seguinte configuração:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do cliente Absorb LMS](https://support.absorblms.com/hc/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Absorb LMS**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao Absorb LMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Absorb LMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-absorb-lms-sso"></a>Configurar o SSO do Absorb LMS

1. Em uma nova janela do navegador da Web, entre no site da empresa do Absorb LMS como administrador.

2. Selecione o botão **Conta** na parte superior direita.

    ![Botão Conta](./media/absorblms-tutorial/account.png)

3. No painel Conta, selecione **Configurações do Portal**.

    ![Link de Configurações do Portal](./media/absorblms-tutorial/portal.png)

4. Selecione a guia **Gerenciar Configurações de SSO**.

    ![A guia Usuários](./media/absorblms-tutorial/sso.png)

5. Na página **Gerenciar Configurações de Logon Único**, faça o seguinte:

    ![Página de configuração de logon único](./media/absorblms-tutorial/settings.png)

    a. Na caixa de texto **Nome**, insira o nome como o SSO do Azure AD Marketplace.

    b. Selecione **SAML** como um **Método**.

    c. No Bloco de Notas, abra o certificado que você baixou pelo Portal do Azure. Remoa as marcas **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---**. Em seguida, na caixa **Chave**, cole o conteúdo restante.

    d. Na caixa **Modo**, selecione **Provedor de Identidade Iniciado**.

    e. Na caixa **Propriedade de ID**, selecione o atributo que você configurou como o identificador de usuário no Azure AD. Por exemplo, se *nameidentifier* estiver selecionado no Azure AD, selecione **Nome de Usuário**.

    f. Selecione **Sha256** como um **Tipo de Assinatura**.

    g. Na caixa **URL de Logon**, cole a **URL de Acesso do Usuário** a partir da página **Propriedade** do aplicativo do Portal do Azure.

    h. Na **URL de Logoff**, cole o valor da **URL de Saída** copiado da janela **Configurar logon** do Portal do Azure.

    i. Defina a opção **Redirecionar Automaticamente** como **Ativo**.

6. Selecione **Salvar**.

    ![Alternância para Permitir apenas Logon SSO](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Criar um usuário de teste do Absorb LMS

Para que os usuários do Azure AD entrem no Absorb LMS, os usuários deverão ser configurados no Absorb LMS. No caso do Absorb LMS, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre no site da empresa do Absorb LMS como administrador.

2. No painel **Usuários**, selecione **Usuários**.

    ![Link Usuários](./media/absorblms-tutorial/users.png)

3. Selecione a guia **Usuário**.

    ![Lista suspensa Adicionar Novo](./media/absorblms-tutorial/add.png)

4. Na página **Adicionar Usuário**, faça o seguinte:

    ![Página Adicionar Usuário](./media/absorblms-tutorial/user.png)

    a. Na caixa **Nome**, digite o primeiro nome, como **Brenda**.

    b. Na caixa **Sobrenome**, digite o último nome, como **Fernandes**.

    c. Na caixa **Nome de Usuário**, digite o nome completo, como **Brenda Fernandes**.

    d. Na caixa **Senha**, digite a senha do usuário.

    e. Na caixa **Confirmar Senha**, digite a senha novamente.

    f. Defina a alternância **Está Ativo** para **Ativo**.

5. Selecione **Salvar**.

    ![Alternância para Permitir apenas Logon SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por padrão, o provisionamento de usuários não está habilitado no SSO. Se o cliente deseja habilitar esse recurso, ele precisa defini-lo conforme mencionado [nesta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Observe também que o provisionamento de usuários só está disponível em **Absorb 5 – Nova Experiência de Aprendiz** com a URL do ACS – `https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao Absorb LMS para o qual o SSO foi configurado.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Absorb LMS em Meus Aplicativos, você deverá ser conectado automaticamente ao Absorb LMS no qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Absorb LMS, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).