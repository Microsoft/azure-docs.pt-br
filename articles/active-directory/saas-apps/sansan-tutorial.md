---
title: 'Tutorial: Integração do Azure Active Directory com Sansan | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sansan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 6c35ce5e4b420b7f203b33b640a3175ba4c2739b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181493"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Tutorial: Integrar o Sansan ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Sansan ao Azure AD (Azure Active Directory). Ao integrar o Sansan ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Sansan.
* Permitir que os usuários sejam conectados automaticamente ao Sansan com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Sansan.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.
* O Sansan dá suporte ao SSO iniciado por **SP**.
* Depois de configurar o Sansan, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sansan-from-the-gallery"></a>Adicionando o Sansan por meio da galeria

Para configurar a integração do Sansan ao Azure AD, é necessário adicionar o Sansan à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **Sansan** na caixa de pesquisa.
1. Selecione **Sansan** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Sansan usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sansan.

Para configurar e testar o SSO do Azure AD com o Sansan, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
   * **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
   * **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o Sansan](#configure-sansan)** para definir as configurações de SSO no lado do aplicativo.
   * **[Criar um usuário de teste do Sansan](#create-sansan-test-user)** – para ter um equivalente de Brenda Fernandes no Sansan que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sansan**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite a URL: `https://ap.sansan.com/`

   1. Na caixa de texto **Identificador (ID da Entidade)**, digite a URL:   
   `https://ap.sansan.com/saml2/<company name>`

   1. Na caixa de texto **URL de Resposta**, digite qualquer das URLs usando o seguinte padrão:

    
       | Ambiente | URL |
      |:--- |:--- |
      | Computador |`https://ap.sansan.com/v/saml2/<company name>/acs` |
      | Aplicativo de smartphone |`https://internal.api.sansan.com/<company name>/acs` |
      | Web de smartphone |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    > [!NOTE]
    > Esses valores não são reais. Verifique os valores reais nas **Configurações de administrador do Sansan**.

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sansan**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Sansan.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sansan**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sansan"></a>Configurar o Sansan

Para executar as **Configurações de logon único** no lado do **Sansan**, siga as etapas abaixo, de acordo com seus requisitos.

   * Versão em [japonês](https://jp-help.sansan.com/hc/ja/articles/900001551383 ).

   * Versão em [inglês](https://jp-help.sansan.com/hc/en-us/articles/900001551383 ).


### <a name="create-sansan-test-user"></a>Criar usuário de teste do SanSan

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SanSan. Para obter mais informações sobre como criar um usuário, veja [estas](https://jp-help.sansan.com/hc/en-us/articles/206508997-Adding-users) etapas.

## <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Sansan no Painel de Acesso, você deverá ser conectado automaticamente ao Sansan, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)