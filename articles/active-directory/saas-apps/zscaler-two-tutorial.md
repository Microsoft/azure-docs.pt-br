---
title: 'Tutorial: integração do Azure Active Directory com o Zscaler Two | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Two.
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
ms.openlocfilehash: 8c3d76f24cce860538dc3bd5dcf29b59708bdb5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726184"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: integração do Azure Active Directory com o Zscaler Two

Neste tutorial, você aprenderá como integrar o Zscaler Two ao Azure AD (Azure Active Directory). Ao integrar o Zscaler Two ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zscaler Two.
* Permitir que os usuários sejam conectados automaticamente ao Zscaler Two com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Two, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zscaler Two habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler Two é compatível com SSO iniciado por **SP**

* O Zscaler Two é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-zscaler-two-from-the-gallery"></a>Adicionar o Zscaler Two da galeria

Para configurar a integração do Zscaler Two ao Azure AD, é necessário adicionar o Zscaler Two da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Zscaler Two** na caixa de pesquisa.
1. Selecione **Zscaler Two** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Configurar e testar o SSO do Azure AD para o Zscaler Two

Configure e teste o SSO do Azure AD com o Zscaler Two usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler Two.

Para configurar e testar o SSO do Azure AD com o Zscaler Two, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zscaler Two](#configure-zscaler-two-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zscaler Two](#create-zscaler-two-test-user)** – para ter um equivalente de B. Fernandes no Zscaler Two que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zscaler Three**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Entrada**, insira a URL usada pelos usuários para entrar no aplicativo ZScaler Two.

    > [!NOTE]
    > Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Zscaler Two](https://www.zscaler.com/company/contact) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Zscaler Two espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo Zscaler Two espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | Nome | Atributo de Origem |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra Declarações de usuário com a opção de Adicionar declaração.](common/new-save-attribute.png)

    ![Captura de tela que mostra a caixa de diálogo Gerenciar declarações do usuário, na qual você pode inserir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.
    
    f. Clique em **Save** (Salvar).

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar a Função no Azure AD.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Zscaler Two**, copie a URL apropriada de acordo com seus requisitos.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao ZScaler Two.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zscaler Two**.
2. Na lista de aplicativos, selecione **ZScaler Two**.
3. No menu à esquerda, selecione **Usuários e grupos**.
4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
5. Na caixa de diálogo **Usuários e grupos**, selecione o usuário como **Brenda Fernandes** na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Usuários e grupos na qual você pode selecionar um usuário.](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. Na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Selecionar Função na qual você pode escolher uma função de usuário.](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

    ![A captura de tela mostra a caixa de diálogo Adicionar Atribuição na qual você pode selecionar Atribuir.](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

## <a name="configure-zscaler-two-sso"></a>Configurar o SSO do Zscaler Two

1. Para automatizar a configuração no Zscaler Two, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o Zscaler Two** direcionará você ao aplicativo Zscaler Two. Nele, forneça as credenciais de administrador para entrar no Zscaler Two. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Sso de instalação](common/setup-sso.png)

3. Se desejar configurar o Zscaler Two manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Zscaler Two como administrador e execute as seguintes etapas:

4. Vá até **Administração > Autenticação > Configurações de Autenticação** e execute as seguintes etapas:
   
    ![A captura de tela mostra o site do Zscaler One com etapas, conforme descrito.](./media/zscaler-two-tutorial/ic800206.png "Administração")

    a. Em Tipo de Autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML**, execute as seguintes etapas: e clique em Salvar.  
            
    ![Gerenciar Usuários e Autenticação](./media/zscaler-two-tutorial/ic800208.png "Gerenciar Usuários e Autenticação")
    
    a. Na caixa de texto **URL do Portal SAML**, cole o a **URL de Logon** copiada do portal do Azure.

    b. Na caixa de texto **Atributo de Nome de Logon**, insira **NameID**.

    c. Clique em **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure para o **Certificado SSL Público**.

    d. Acione **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa de texto **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa de texto **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Em **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Save** (Salvar).

6. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![A captura de tela mostra a caixa de diálogo Configurar Autenticação de Usuário com Ativar selecionado.](./media/zscaler-two-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.   
    
     ![Opções da Internet](./media/zscaler-two-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **Conexões** .   
  
     ![Conexões](./media/zscaler-two-tutorial/ic769493.png "conexões")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-two-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto Endereço, digite **gateway.Zscaler.Two.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)** .

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.


### <a name="create-zscaler-two-test-user"></a>Criar usuário de teste do Zscaler Two

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Zscaler Two. O Zscaler Two é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Zscaler Two, um será criado após a autenticação.

>[!Note]
>Caso precise criar um usuário manualmente, contate a [equipe de suporte do Zscaler Two](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zscaler Two, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Zscaler Two e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Zscaler Two em Meus Aplicativos, isso redirecionará você à URL de Logon do Zscaler Two. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zscaler Two, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
