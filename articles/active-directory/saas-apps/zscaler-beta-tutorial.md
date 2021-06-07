---
title: 'Tutorial: integração do Azure Active Directory com o Zscaler Beta | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Beta.
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
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735570"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: integração do Azure Active Directory com o Zscaler Beta

Neste tutorial, você aprenderá como integrar o Zscaler Beta ao Azure AD (Azure Active Directory).
Ao integrar o Zscaler Beta ao Azure AD, você pode:

* Controlar quem tem acesso ao Zscaler Beta no Azure AD.
* Permitir que seus usuários entrem automaticamente no Zscaler Beta usando suas contas do Azure AD. Esse controle de acesso é chamado de logon único (SSO).
* Gerenciar suas contas em um local central usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Beta, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zscaler Beta que usa logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler Beta dá suporte ao SSO iniciado por **SP**.
* O Zscaler Beta dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar o Zscaler Beta da galeria

Para configurar a integração do Zscaler Beta com o Azure AD, é necessário adicionar o Zscaler Beta da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zscaler Beta** na caixa de pesquisa.
1. Selecione **Zscaler Beta** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Configurar e testar o SSO do Azure AD para o Zscaler Beta

Configure e teste o SSO do Azure AD com o Zscaler Beta usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler Beta.

Para configurar e testar o SSO do Azure AD com o Zscaler Beta, execute as seguintes etapas:


1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zscaler Beta](#configure-zscaler-beta-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zscaler Beta](#create-zscaler-beta-test-user)** : para ter um equivalente de B.Fernandes no Zscaler Beta que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zscaler Beta**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa **URL de Entrada**, insira a URL usada pelos usuários para entrar no aplicativo Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o valor da URL de Entrada real. Para obtê-lo, entre em contato com a [equipe de suporte ao cliente do Zscaler Beta](https://www.zscaler.com/company/contact).

5. O aplicativo Zscaler Beta espera que as declarações SAML estejam em um formato específico. Você deve adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![A caixa de diálogo Atributos do Usuário](common/edit-attribute.png)

6. O aplicativo Zscaler Beta espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, siga as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo.
    
    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa **Nome**, insira o nome do atributo mostrado para aquela linha.

    c. Deixe a caixa **Namespace** em branco.

    d. Para **Origem**, selecione **Atributo**.

    e. Na lista **Atributo de origem**, insira o valor do atributo mostrado para essa linha.

    f. Selecione **OK**.

    g. Selecione **Salvar**.

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar a Função no Azure AD.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, selecione **Download** para baixar o certificado **(Base64)**. Salve-o no computador.

    ![Link de download do certificado](common/certificatebase64.png)

8. Na seção **Configurar o Zscaler Beta**, copie as URLs necessárias para seus requisitos:

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Zscaler Beta.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zscaler Beta**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zscaler-beta-sso"></a>Configurar o SSO do Zscaler Beta

1. Para automatizar a configuração no Zscaler Beta, instale a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, selecionando **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionar **Configurar o Zscaler Beta** direciona você para o aplicativo Zscaler Beta. Em seguida, forneça as credenciais de administrador para entrar no Zscaler Beta. A extensão do navegador configura automaticamente o aplicativo e automatiza as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Para configurar o Zscaler Beta manualmente, abra uma nova janela do navegador da Web. Entre em seu site de empresa do Zscaler Beta como administrador e siga estas etapas.

4. Acesse **Administração** > **Autenticação** > **Configurações de Autenticação**, e siga estas etapas.
   
    ![Administração](./media/zscaler-beta-tutorial/ic800206.png "Administração")

    a. Em **Tipo de Autenticação**, selecione **SAML**.

    b. Selecione **Configurar SAML**.

5. Na janela **Editar SAML**, siga estas etapas: 
            
    ![Gerenciar Usuários e Autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerenciar Usuários e Autenticação")
    
    a. Na caixa **URL do Portal SAML**, cole a **URL de Logon** copiada do portal do Azure.

    b. Na caixa **Atributo de Nome de Logon**, insira **NameID**.

    c. Na caixa **Certificado SSL Público**, selecione **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure.

    d. Alternar **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Na caixa **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Salvar**.

6. Na página de caixa de diálogo **Configurar Autenticação de Usuário**, siga as etapas:

    ![Menu Ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Selecione **Ativar**.

## <a name="configure-proxy-settings&quot;></a>Definir configurações de proxy
Para definir as configurações de proxy no Internet Explorer, siga estas etapas.

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**. 
    
     ![Caixa de diálogo de Opções da Internet](./media/zscaler-beta-tutorial/ic769492.png &quot;Opções da Internet")

3. Selecione a guia **Conexões**. 
  
     ![Guia Conexões](./media/zscaler-beta-tutorial/ic769493.png "conexões")

4. Selecione **Configurações da LAN** para abrir a caixa de diálogo **Configurações da Rede Local (LAN)**.

5. Na seção **Servidor Proxy**, siga estas etapas: 
   
    ![Seção do servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor proxy")

    a. Selecione a caixa de seleção **Usar um Servidor Proxy para LAN**.

    b. Na caixa **Endereço**, insira **gateway.Zscaler Beta.net**.

    c. Na caixa **Porta**, insira **80**.

    d. Selecione **Não usar servidor proxy para endereços locais**.

    e. Selecione **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)**.

6. Selecione **OK** para fechar a caixa de diálogo **Opções da Internet**.

### <a name="create-zscaler-beta-test-user"></a>Criar um usuário de teste do Zscaler Beta

Nesta seção, a usuária Brenda Fernandes será criada no Zscaler Beta. O Zscaler Beta dá suporte ao **provisionamento de usuário Just-In-Time**, que está habilitado por padrão. Não há nada a fazer nesta seção. Se um usuário ainda não existir no Zscaler Beta, um novo será criado após a autenticação.

>[!Note]
>Para criar um usuário manualmente, contate a [Equipe de suporte do Zscaler Beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zscaler Beta, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Zscaler Beta e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Zscaler Beta em Meus Aplicativos, você será redirecionado à URL de Logon do Zscaler Beta. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zscaler Beta, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
