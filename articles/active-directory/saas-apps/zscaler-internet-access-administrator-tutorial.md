---
title: 'Tutorial: Integração do Azure Active Directory ao Administrador do Zscaler Internet Access | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Administrador do Zscaler Internet Access.
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
ms.openlocfilehash: 8af8d92ca66cfbd3d6223bc9a73125c457164d82
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735538"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: Integração do Azure Active Directory ao Administrador do Zscaler Internet Access

Neste tutorial, você aprenderá a integrar o Administrador do Zscaler Internet Access ao Azure AD (Azure Active Directory). Ao integrar o Administrador do Zscaler Internet Access ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Administrador do Zscaler Internet Access.
* Permitir que os usuários sejam conectados automaticamente ao Administrador do Zscaler Internet Access com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Administrador do Zscaler Internet Access, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Zscaler Internet Access Administrator

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Administrador do Zscaler Internet Access dá suporte ao SSO iniciado por **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Adicionando o Administrador do Zscaler Internet Access da galeria

Para configurar a integração do Administrador do Zscaler Internet Access ao Azure AD, você precisará adicionar o Administrador do Zscaler Internet Access da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Administrador do Zscaler Internet Access** na caixa de pesquisa.
1. Selecione **Administrador do Zscaler Internet Access** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Configurar e testar o SSO do Azure AD para o Administrador do Zscaler Internet Access

Configure e teste o SSO do Azure AD com o Administrador do Zscaler Internet Access usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Administrador do Zscaler Internet Access.

Para configurar e testar o SSO do Azure AD com o Administrador do Zscaler Internet Access, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Administrador do Zscaler Internet Access](#configure-zscaler-internet-access-administrator-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Administrador do Zscaler Internet Access](#create-zscaler-internet-access-administrator-test-user)** – para ter um equivalente de Brenda Fernandes no Administrador do Zscaler Internet Access que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Administrador do Zscaler Internet Access**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL de acordo com seus requisitos:

    | Identificador |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Na caixa de texto **URL de Resposta**, digite uma URL de acordo com seus requisitos:

    | URL de resposta |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. O aplicativo de Administrador do Zscaler Internet Access espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos e Declarações do Usuário**.

    ![O link do Atributo](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | Nome  | Atributo de Origem  |
    | ---------| ------------ |
    | Função | user.assignedroles |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na lista **Atributo de origem**, selecione o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Save** (Salvar).

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar a Função no Azure AD.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Administrador do Zscaler Internet Access**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Administrador do Zscaler Internet Access.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Administrador do Zscaler Internet Access**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.


## <a name="configure-zscaler-internet-access-administrator-sso"></a>Configurar o SSO do Administrador do Zscaler Internet Access

1. Em outra janela do navegador da Web, faça logon na interface do usuário do Administrador do Zscaler Internet Access.

2. Acesse **Administração > Gerenciamento de Administradores**, execute as seguintes etapas e clique em Salvar:

    ![A captura de tela mostra o Gerenciamento de Administradores com opções para Habilitar a Autenticação SAML, carregar o certificado SSL e especificar um Emissor.](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administração")

    a. Marque a opção **Habilitar Autenticação SAML**.

    b. Clique em **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure para o **Certificado SSL Público**.

    c. Opcionalmente, para segurança extra, adicione os detalhes do **Emissor** para verificar o Emissor da resposta SAML.

3. Na interface do usuário do Administrador, execute as seguintes etapas:

    ![A captura de tela mostra a guia IU do Administrador na qual você pode executar essas etapas.](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Clique em **Ativar**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Criar um usuário de teste do Administrador do Zscaler Internet Access

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Administrador do Zscaler Internet Access. O Zscaler Internet Access não dá suporte ao provisionamento Just-In-Time no SSO de Administrador. É necessário criar uma conta Administrador manualmente.
Para obter as etapas de como criar uma conta Administrador, consulte a documentação do Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao Administrador do Zscaler Internet Access, para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Administrador do Zscaler Internet Access em Meus Aplicativos, você será conectado automaticamente ao Administrador do Zscaler Internet Access, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Administrador do Zscaler Internet Access, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
