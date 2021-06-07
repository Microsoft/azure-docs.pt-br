---
title: 'Tutorial: integração do Azure Active Directory com o Zscaler ZSCloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler ZSCloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 605f033ed48dd79fd164aabd95e326a6467d0ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726174"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: integração do Azure Active Directory com o Zscaler ZSCloud

Neste tutorial, você aprenderá a integrar o Zscaler ZSCloud com o Azure AD (Azure Active Directory). Ao integrar o Zscaler ZSCloud ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zscaler ZSCloud.
* Permitir que os usuários sejam conectados automaticamente ao Zscaler ZSCloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler ZSCloud, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zscaler ZSCloud habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler ZSCloud é compatível com SSO iniciado por **SP**

* O Zscaler ZSCloud é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Adicionando o Zscaler ZSCloud por meio da galeria

Para configurar a integração do Zscaler ZSCloud ao Azure AD, você precisa adicionar o Zscaler ZSCloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Zscaler ZSCloud** na caixa de pesquisa.
1. Selecione **Zscaler ZSCloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Configurar e testar o SSO do Azure AD para o Zscaler ZSCloud

Configure e teste o SSO do Azure AD com o Zscaler ZSCloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler ZSCloud.

Para configurar e testar o SSO do Azure AD com o Zscaler ZSCloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zscaler ZSCloud](#configure-zscaler-zscloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zscaler ZSCloud](#create-zscaler-zscloud-test-user)** – para ter um equivalente de B. Fernandes no Zscaler ZSCloud que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zscaler ZSCloud**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para se conectar ao aplicativo ZScaler ZSCloud.

    > [!NOTE]
    > É necessário atualizar o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Zscaler ZSCloud](https://help.zscaler.com/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Zscaler ZSCloud espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo Zscaler ZSCloud espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
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

8. Na seção **Configurar o Zscaler ZSCloud**, copie a URL apropriada de acordo com seus requisitos.

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

Nesta seção, você habilitará a Brenda Fernandes a usar o logon único do Azure através da concessão de acesso ao Zscaler ZSCloud.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zscaler ZSCloud**.
2. Na lista de aplicativos, selecione **Zscaler ZSCloud**.
3. No menu à esquerda, selecione **Usuários e grupos**.
4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
5. Na caixa de diálogo **Usuários e grupos**, selecione o usuário como **Brenda Fernandes** na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Usuários e grupos na qual você pode selecionar um usuário.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. Na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

    ![A captura de tela mostra a caixa de diálogo Selecionar Função na qual você pode escolher uma função de usuário.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

    ![A captura de tela mostra a caixa de diálogo Adicionar Atribuição na qual você pode selecionar Atribuir.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >A função de acesso padrão não é compatível, pois isso interromperá o provisionamento; portanto, a função de usuário não pode ser selecionada ao atribuir um usuário.

## <a name="configure-zscaler-zscloud-sso"></a>Configurar o SSO do Zscaler ZSCloud

1. Para automatizar a configuração no Zscaler ZSCloud, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o Zscaler ZSCloud** direcionará você ao aplicativo Zscaler ZSCloud. Nele, forneça as credenciais de administrador para entrar no Zscaler ZSCloud. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Sso de instalação](common/setup-sso.png)

3. Se desejar configurar o Zscaler ZSCloud manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Zscaler ZSCloud como administrador e execute as seguintes etapas:

4. Vá até **Administração > Autenticação > Configurações de Autenticação** e execute as seguintes etapas:
   
    ![A captura de tela mostra o site do Zscaler com etapas, conforme descrito.](./media/zscaler-zscloud-tutorial/ic800206.png "Administração")

    a. Em Tipo de Autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML**, execute as seguintes etapas: e clique em Salvar.  
            
    ![Gerenciar Usuários e Autenticação](./media/zscaler-zscloud-tutorial/ic800208.png "Gerenciar Usuários e Autenticação")
    
    a. Na caixa de texto **URL do Portal SAML**, cole o a **URL de Logon** copiada do portal do Azure.

    b. Na caixa de texto **Atributo de Nome de Logon**, insira **NameID**.

    c. Clique em **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure para o **Certificado SSL Público**.

    d. Acione **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa de texto **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa de texto **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Em **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Save** (Salvar).

6. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![A captura de tela mostra a caixa de diálogo Configurar Autenticação de Usuário com Ativar selecionado.](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.   
    
     ![Opções da Internet](./media/zscaler-zscloud-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **Conexões** .   
  
     ![Conexões](./media/zscaler-zscloud-tutorial/ic769493.png "conexões")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-zscloud-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto Endereço, digite **gateway.Zscaler ZSCloud.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)** .

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.


### <a name="create-zscaler-zscloud-test-user"></a>Criar usuário de teste do Zscaler ZSCloud

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Zscaler ZSCloud. O Zscaler ZSCloud é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Zscaler ZSCloud, um será criado após a autenticação.

>[!Note]
>Caso precise criar um usuário manualmente, contate a [equipe de suporte do Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zscaler ZSCloud, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Zscaler ZSCloud e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Zscaler ZSCloud em Meus Aplicativos, isso redirecionará você à URL de Logon do Zscaler ZSCloud. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zscaler ZSCloud, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
