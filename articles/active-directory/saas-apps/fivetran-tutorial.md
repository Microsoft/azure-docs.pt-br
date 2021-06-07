---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Fivetran | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Fivetran.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 88f6da7e43e269816c7e4ae2fd7bbc3f5e26522b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453086"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Fivetran

Neste tutorial, você aprenderá a integrar o Fivetran ao Azure AD (Azure Active Directory). Ao integrar o Fivetran ao Azure AD, você poderá:

* Controlar quem tem acesso ao Fivetran no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Fivetran com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do Fivetran.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Fivetran dá suporte ao SSO iniciado por **IdP**
* O Fivetran dá suporte ao provisionamento de usuário **just-in-time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-fivetran-from-the-gallery"></a>Adicionar o Fivetran por meio da galeria

Para configurar a integração do Fivetran ao Azure AD, você precisará adicionar o Fivetran por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Fivetran** na caixa de pesquisa.
1. Escolha **Fivetran** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Configurar e testar o SSO do Azure AD para o Fivetran

Configure e teste o SSO do Azure AD com o Fivetran usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fivetran.

Para configurar e testar o SSO do Azure AD com o Fivetran, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Fivetran](#configure-fivetran-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Fivetran](#create-fivetran-test-user)** : para ter um equivalente de B.Fernandes no Fivetran que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Fivetran**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.


1. O aplicativo Fivetran espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Fivetran espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | -------------- | --------- |
    | Nome | user.givenname |
    | LastName | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Fivetran**, copie os valores da **URL de logon** e do **Identificador do Azure AD**.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Fivetran.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Fivetran**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-fivetran-sso"></a>Configurar o SSO do Fivetran

Nesta seção, você configurará o logon único no lado do **Fivetran**.

1. Em outra janela do navegador da Web, entre em sua conta do Fivetran como proprietário da conta.
1. Selecione a seta no canto superior esquerdo da janela e selecione **Gerenciar conta** na lista suspensa.

   ![Captura de tela que mostra a opção de menu Gerenciar conta selecionada.](media/fivetran-tutorial/fivetran-1.png)

1. Vá para a seção **Configuração de SAML** da página **Configurações**.

   ![Captura de tela que mostra o painel de Configuração de SAML com as opções de configuração realçadas.](media/fivetran-tutorial/fivetran-2.png)

   1. Para **Habilitar autenticação SAML**, selecione **ATIVADO**.
   1. Em **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.
   1. Em **Emissor**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure.
   1. Abra seu arquivo de certificado baixado em um editor de texto, copie o certificado para a área de transferência e cole-o na caixa de texto **Certificado Público**.
   1. Selecione **SALVAR CONFIGURAÇÃO**.

### <a name="create-fivetran-test-user"></a>Criar um usuário de teste do Fivetran

Nesta seção, um usuário chamado B.Fernandes será criado no Fivetran. O Fivetran dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Fivetran, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Fivetran, para o qual configurou o SSO 

2. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Fivetran no Painel de Acesso, você será conectado automaticamente ao Fivetran, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Fivetran, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).