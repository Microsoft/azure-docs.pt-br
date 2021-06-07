---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Mapbox | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mapbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e74018d4b145f8dec44137ca1a3b0713b1b5cadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728525"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Mapbox

Neste tutorial, você aprenderá a integrar o Mapbox ao Azure AD (Azure Active Directory). Ao integrar o Mapbox ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Mapbox.
* Permitir que os usuários sejam conectados automaticamente ao Mapbox com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Mapbox habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Mapbox dá suporte ao SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-mapbox-from-the-gallery"></a>Adicionar o Mapbox da galeria

Para configurar a integração do Mapbox ao Azure AD, você precisará adicionar o Mapbox da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Mapbox** na caixa de pesquisa.
1. Selecione **Mapbox** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Configurar e testar o SSO do Azure AD para o Mapbox

Configure e teste o SSO do Azure AD com o Mapbox usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mapbox.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Mapbox:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Mapbox](#configure-mapbox-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Mapbox](#create-mapbox-test-user)** – para ter um equivalente de B.Fernandes no Mapbox que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Mapbox**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Seu aplicativo Mapbox espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Mapbox espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome   |  Atributo de Origem|
    | -----|--------- |
    | função | user.assignedroles |
    | | |

    > [!NOTE]
    > Para entender como configurar funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Bruto)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o Mapbox**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Mapbox.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Mapbox**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mapbox-sso"></a>Configurar o SSO do Mapbox

1. Em outra janela do navegador da Web, entre no site do Mapbox como administrador.

1. Clique na guia **Configurações**.

    ![Guia Configurações do Mapbox](./media/mapbox-tutorial/configure1.png)

1. Clique na guia **Segurança** no painel de navegação esquerdo.

    ![Guia Segurança do Mapbox](./media/mapbox-tutorial/configure2.png)

1. Clique em **Editar logon único**.

    ![Editar logon único do Mapbox](./media/mapbox-tutorial/configure3.png)

1. Role para baixo até **Etapa 3: Configurar logon único do SAML para o Mapbox** e execute as seguintes etapas:

    ![Configuração do Mapbox](./media/mapbox-tutorial/configure4.png)

    1. Na caixa de texto **URL de Logon IdP**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    1. Na caixa de texto **ID do Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Abra o arquivo **Certificado (Bruto)** baixado do portal do Azure no Bloco de notas, copie o conteúdo do certificado e cole-o na caixa de texto **Certificado X.509**.

    1. Clique em **Salvar configurações de logon único**.

### <a name="create-mapbox-test-user"></a>Criar um usuário de teste do Mapbox

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Mapbox. Trabalhe com a [equipe de suporte do Mapbox](mailto:help@mapbox.com) para adicionar os usuários na plataforma do Mapbox. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado de modo automático ao Mapbox para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Mapbox em Meus Aplicativos, você deverá ser conectado automaticamente ao Mapbox, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Mapbox, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
