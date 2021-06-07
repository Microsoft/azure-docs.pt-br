---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao JLL TIRIRIGA | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o JLL TIRIRIGA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: b33db1b766d99aeb8d0a08f6396b5384948f118e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736862"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jll-tririga"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao JLL TIRIRIGA

Neste tutorial, você aprenderá a integrar o JLL TIRIRIGA ao Azure AD (Azure Active Directory). Ao integrar o JLL TIRIRIGA ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao JLL TIRIRIGA.
* Permitir que os usuários sejam conectados automaticamente ao JLL TIRIRIGA com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do JLL TIRIRIGA habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O JLL TIRIRIGA é compatível com SSO iniciado por **IdP**

## <a name="adding-jll-tririga-from-the-gallery"></a>Adicionar o JLL TRIRIGA por meio da galeria

Para configurar a integração do JLL TIRIRIGA ao Azure AD, você precisará adicionar o JLL TIRIRIGA por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **JLL TIRIRIGA** na caixa de pesquisa.
1. Selecione **JLL TIRIRIGA** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-jll-tririga"></a>Configurar e testar o SSO do Azure AD para o JLL TIRIRIGA

Configure e teste o SSO do Azure AD com o JLL TIRIRIGA usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do JLL TIRIRIGA.

Para configurar e testar o SSO do Azure AD com o JLL TRIRIGA, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do JLL TIRIRIGA](#configure-jll-tririga-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do JLL TIRIRIGA](#create-jll-tririga-test-user)** – para ter um equivalente de B. Fernandes no JLL TIRIRIGA que esteja vinculado à declaração do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **JLL TIRIRIGA**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:
   
    | Identificador |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT` |
    | `https://<SUBDOMAIN>.jll.com` |
    |

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    | URL de resposta |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT/samlsps/trisaml` |
    | `https://<SUBDOMAIN>.jll.com/samlsps/trisaml` |
    |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do JLL TIRIRIGA](https://www.us.jll.com/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o JLL TIRIRIGA**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao JLL TIRIRIGA.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **JLL TIRIRIGA**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-jll-tririga-sso"></a>Configurar o SSO do JLL TIRIRIGA

Para configurar o logon único no lado do **JLL TIRIRIGA**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do JLL TIRIRIGA](https://www.us.jll.com/contact-us). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-jll-tririga-test-user"></a>Criar um usuário de teste do JLL TIRIRIGA

Nesta seção, você criará uma usuária chamada Brenda Fernandes no JLL TIRIRIGA. Trabalhe com a [equipe de suporte do JLL TIRIRIGA](https://www.us.jll.com/contact-us) para adicionar os usuários na plataforma do JLL TIRIRIGA. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao JLL TIRIRIGA para o qual configurou o SSO

* Use o Painel de Acesso da Microsoft. Ao clicar no bloco do JLL TIRIRIGA no Painel de Acesso, você deverá ser conectado automaticamente ao JLL TIRIRIGA para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o JLL TIRIRIGA, será possível impor o controle de sessão, que fornece proteção contra a infiltração e exfiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).