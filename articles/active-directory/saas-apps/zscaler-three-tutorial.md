---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zscaler Three | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Three.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 75d95ff77b48e7b1102900bc103e6930282e21e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726186"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zscaler Three

Neste tutorial, você aprenderá a integrar o Zscaler Three ao Azure AD (Azure Active Directory). Ao integrar o Zscaler Three ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zscaler Three.
* Permitir que os usuários sejam conectados automaticamente ao Zscaler Three com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Zscaler Three.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zscaler Three dá suporte ao SSO iniciado por **SP**

* O Zscaler Three dá suporte ao provisionamento de usuário **just-in-time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da Galeria

Para configurar a integração do Zscaler Three ao Azure AD, é necessário adicionar o Zscaler Three da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zscaler Three** na caixa de pesquisa.
1. Selecione **Zscaler Three** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-three"></a>Configurar e testar o SSO do Azure AD para o Zscaler Three

Configure e teste o SSO do Azure AD com o Zscaler Three usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler Three.

Para configurar e testar o SSO do Azure AD com o Zscaler Three, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zscaler Three](#configure-zscaler-three-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zscaler Three](#create-zscaler-three-test-user)** – para ter um equivalente de B.Fernandes no Zscaler Three que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zscaler Three**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://login.zscalerthree.net/sfc_sso`

1. O aplicativo Zscaler Three espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo Zscaler Three espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar a Função no Azure AD.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Zscaler Three**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Zscaler Three.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zscaler Three**.
1. Na caixa de diálogo **Usuários e grupos**, selecione o usuário como **Brenda Fernandes** na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Usuários e grupos na qual você pode selecionar um usuário.](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. Na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Selecionar Função na qual você pode escolher uma função de usuário.](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

    ![A captura de tela mostra a caixa de diálogo Adicionar Atribuição na qual você pode selecionar Atribuir.](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Configurar o SSO do Zscaler Three

1. Para automatizar a configuração no Zscaler Three, é necessário instalar a **Extensão do navegador de Conexão Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o Zscaler Three** direcionará você ao aplicativo Zscaler Three. Forneça as credenciais de administrador para entrar no Zscaler Three. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Instalação](common/setup-sso.png)

3. Se desejar configurar o Zscaler Three manualmente, abra uma nova janela do navegador da Web, entre no site empresarial do Zscaler Three como administrador e execute as seguintes etapas:

4. Vá até **Administração > Autenticação > Configurações de Autenticação** e execute as seguintes etapas:

    ![A captura de tela mostra o site do Zscaler One com etapas, conforme descrito.](./media/zscaler-three-tutorial/ic800206.png "Administração")

    a. Em Tipo de Autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML**, execute as seguintes etapas: e clique em Salvar.  

    ![Gerenciar Usuários e Autenticação](./media/zscaler-three-tutorial/ic800208.png "Gerenciar Usuários e Autenticação")

    a. Na caixa de texto **URL do Portal SAML**, cole o a **URL de Logon** copiada do portal do Azure.

    b. Na caixa de texto **Atributo de Nome de Logon**, insira **NameID**.

    c. Clique em **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure para o **Certificado SSL Público**.

    d. Acione **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa de texto **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa de texto **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Em **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Save** (Salvar).

6. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![A captura de tela mostra a caixa de diálogo Configurar Autenticação de Usuário com Ativar selecionado.](./media/zscaler-three-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.   

     ![Opções da Internet](./media/zscaler-three-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **Conexões** .   

     ![Conexões](./media/zscaler-three-tutorial/ic769493.png "conexões")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:   

    ![Servidor proxy](./media/zscaler-three-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto Endereço, digite **gateway.Zscaler Three.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)** .

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

### <a name="create-zscaler-three-test-user"></a>Criar um usuário de teste do Zscaler Three

Nesta seção, um usuário chamado B.Fernandes será criado no Zscaler Three. O Zscaler Three dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Zscaler Three, um novo será criado quando você tentar acessar o Zscaler Three.

>[!Note]
>Caso precise criar um usuário manualmente, contate a [equipe de suporte do Zscaler Three](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zscaler Three, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Zscaler Three e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Zscaler Three em Meus Aplicativos, isso redirecionará você à URL de Logon do Zscaler Three. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zscaler Three, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
